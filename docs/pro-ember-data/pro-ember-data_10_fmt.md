# 9. 处理自定义错误响应

到目前为止，我们一直在研究如何处理返回成功响应的 API。在本章中，我们将了解 `Ember Data` 如何开箱即用地处理错误，以及我们可以覆盖哪些不同的适配器和序列化器方法来处理自定义错误响应。让我们开始吧！

## 验证错误

HTTP 状态码为 `422`（不可处理的实体）的响应被视为无效错误，换言之，即验证错误。无论使用哪种序列化器，验证错误响应均遵循 `JSON:API` 规范。一个 `JSON:API` 错误响应如下所示：

```
{
  "errors": [
    {
      "id": "{此特定出现的唯一标识符}",
      "links": {
        "about": "{指向此问题更多详细信息的链接}"
      },
      "status": "{HTTP 状态码}",
      "code": "{应用特定的错误代码}",
      "title": "{问题的摘要}",
      "detail": "{针对此问题出现的具体说明}",
      "source": {
        "pointer": "{指向请求文档中关联实体的 JSON 指针}",
        "parameter": "{指示哪个 URI 查询参数导致错误的字符串}"
      },
      "meta": {}
    }
  ]
}
```

响应必须包含一个根键 `errors`，它是一个错误对象数组。每个错误对象可以包含前述的任何属性。要详细了解错误对象的每个属性，请访问 `JSON:API` 错误文档（14）。`JSON:API` 指出错误对象*可能*包含这些属性，但 `Ember Data` 只需要其中的一个子集。

假设我们要创建一个新的 `contact` 记录，并处理验证 `name` 属性时出现错误的场景。错误响应需要两个属性：`detail` 和 `source`：

```
{
  "errors": [
    {
      "detail": "名称必须至少 2 个字符。",
      "source": {
        "pointer": "data/attributes/name"
      }
    }
  ]
}
```

错误对象中 `source.pointer` 的值是一个指向特定属性的 JSON 指针（15），在此例中指向 `name` 属性。JSON 指针是一种使用类似文件路径语法的字符串，用于标识 JSON 文档中特定值的路径。

当适配器看到 `422` 响应状态码时，会返回一个包含 `InvalidError` 实例的被拒绝的 Promise，以指示记录未能通过服务器端验证。`InvalidError` 实例会接收来自响应负载的 `errors` 数据。然后可以通过记录的 `errors` 属性检索这些验证错误。例如，我们可以按如下方式渲染记录中 `name` 属性的验证错误：

```
{{#each model.errors.name as |error|}}
  {{error.message}}
{{/each}}
```

我们还可以在 `catch` 块中访问这些错误以及 `InvalidError` 实例：

```
try {
  await contact.save();
} catch (invalidError) {
  console.log(invalidError); // InvalidError 实例
  console.log(contact.errors); // Errors 实例
  console.log(contact.get('errors.name')); // name 属性的错误对象数组
  console.log(contact.isValid); // false
}
```

### 控制无效状态码

你的第一个问题可能是：“如果我的 API 返回的不是 `422` 状态码怎么办？” 如果我们查看 `RESTAdapter` 源代码，可以看到 `InvalidError` 的创建仅当状态通过 `isInvalid()` 方法被判定为无效时才会发生：

```
isInvalid(status, headers, payload) {
  return status === 422;
},

handleResponse(status, headers, payload, requestData) {
  if (this.isSuccess(status, headers, payload)) {
    return payload;
  } else if (this.isInvalid(status, headers, payload)) {
    return new InvalidError(payload.errors);
  }
  // ...
  return new AdapterError(errors, detailedMessage);
}
```

我们可以覆盖适配器中的公共方法 `isInvalid()` 来适应非 `422` 的状态码。例如，我们的 API 可能返回 `400` 而不是 `422`：

```
app/adapters/application.js
import RESTAdapter from '@ember-data/adapter/rest';

export default class ApplicationAdapter extends RESTAdapter {
  namespace = 'api';

  isInvalid(status) {
    return status === 400;
  }
}
```

### 控制错误响应负载

你的第二个问题可能是：“如果我的错误响应负载不遵循 `JSON:API` 格式怎么办？”

假设我们的错误响应如下所示：

```
{
  "errors": {
    "name": "名称必须至少 2 个字符。"
  }
}
```

如果负载包含一个非数组类型的 `errors` 属性，你将在控制台中看到以下错误消息：

`Error` — `AdapterError` 期望 `JSON:API` 格式的错误数组。

此错误消息提到的是 `AdapterError` 而非 `InvalidError` 的原因，是因为 `InvalidError` 继承了 `AdapterError` 类。

让我们再次查看 `handleResponse()` 的实现：

```
handleResponse(status, headers, payload, requestData) {
  if (this.isSuccess(status, headers, payload)) {
    return payload;
  } else if (this.isInvalid(status, headers, payload)) {
    return new InvalidError(payload.errors);
  }
  // ...
  return new AdapterError(errors, detailedMessage);
}
```

如你所见，负载的 `errors` 属性被直接传递给 `InvalidError`，如果它不是数组，则会抛出前述错误消息。为了处理 `errors` 不是数组或根本没有 `errors` 属性的自定义错误负载，我们可以在适配器中覆盖 `handleResponse()` 方法：

```
app/adapters/application.js
import RESTAdapter from 'ember-data/adapters/rest';

export default class ApplicationAdapter extends RESTAdapter {
  namespace = 'api';

  handleResponse(status, headers, payload, requestData) {
    if (this.isInvalid(status)) {
      payload.errors = Object.keys(payload.errors).map((attribute) => {
        return {
          detail: payload.errors[attribute],
          source: {
            pointer: 'data/attributes/${attribute}'
          }
        };
      });
    }
    return super.handleResponse(status, headers, payload, requestData);
  }
}
```

在上述代码中，如果状态无效，我们会将自定义错误负载转换为符合 `JSON:API` 规范的格式。然后，调用原始的 `handleResponse()` 方法以创建 `InvalidError` 对象。

现在你的第三个问题可能是："如果负载确实有一个数组类型的 `errors` 属性，但它不符合 JSON:API 规范怎么办？" 假设我们的错误负载如下所示：

```json
{
  "errors": [
    {
      "attribute": "name",
      "messages": {
        "size": "名称必须至少 2 个字符。",
        "alpha": "名称必须全部由字母组成。"
      }
    }
  ]
}
```

每个验证规则及其对应的消息都存储在 `errors/messages` 下。要处理此错误负载，我们可以像之前一样覆盖 `handleResponse()`，或者覆盖序列化器上的 `extractErrors()` 方法。`extractErrors()` 方法用于在模型上的 `save()` 调用因 `InvalidError` 失败时提取模型错误。`extractErrors()` 方法接收负载作为其参数之一，并期望返回值格式如下：

```javascript
{
  name: [
    '名称必须至少 2 个字符。',
    '名称必须全部由字母组成。'
  ]
}
```

以下是覆盖 `extractErrors()` 以规范化响应中 `errors` 数组的实现示例：

```javascript
// app/serializers/contact.js
import ApplicationSerializer from './application';

export default class ContactSerializer extends ApplicationSerializer {
  extractErrors(store, typeClass, payload, id) {
    let extractedErrors = {};
    payload.errors.forEach((error) => {
      extractedErrors[error.attribute] = Object.keys(error.messages).map(
        (rule) => {
          return error.messages[rule];
        }
      );
    });
    return extractedErrors;
  }
}
```

那么你的第四个问题可能是："如果所有情况我都能用 `handleResponse()` 处理，为什么还要费心使用 `extractErrors()`？" 这个问题的答案又回到了"哪个类负责格式化请求和响应数据？"这个问题上。答案是序列化器。通常，如果能在序列化器中处理错误响应的规范化，我倾向于这样做。如果无法在序列化器中进行规范化，那么我会在适配器中处理。

## 其他错误类型

我们学习了当收到 422 响应时会被实例化的 `InvalidError` 类。Ember Data 也为常见的 HTTP 状态码提供了其他几种错误类型（表 9-1）。

**表 9-1 常见 HTTP 状态码**

| 错误类 | HTTP 状态码 |
| --- | --- |
| `InvalidError` | 422 |
| `UnauthorizedError` | 401 |
| `ForbiddenError` | 403 |
| `NotFoundError` | 404 |
| `ConflictError` | 409 |
| `ServerError` | 500 |

当 HTTP 状态码为 401 时，会抛出 `UnauthorizedError`，表示需要授权，但授权失败或未提供授权。当 HTTP 状态码为 403 时，会抛出 `ForbiddenError`，表示已认证用户没有执行该请求所需的权限。当 HTTP 状态码为 404 时，会抛出 `NotFoundError`，表示服务器找不到请求的资源。当 HTTP 状态码为 409 时，会抛出 `ConflictError`，表示请求与服务器的当前状态存在冲突。例如，如果用户尝试使用过时的数据更新资源，服务器可能会返回 409 状态码。最后，当 HTTP 状态码为 500 时，会抛出 `ServerError`，表示服务器内部发生错误。

所有这些错误类都继承自 `AdapterError` 类。

假设我们的 API 返回了 500 状态码。当这种情况发生时，以下代码将成立：

```javascript
import AdapterError, {
  ServerError,
  TimeoutError
} from '@ember-data/adapter/error';
// ...
try {
  await contact.save();
} catch (serverError) {
  console.log(serverError instanceof AdapterError); // true
  console.log(serverError instanceof ServerError); // true
  console.log(serverError instanceof TimeoutError); // false
}
```

正如我们在学习 `InvalidError` 时所了解的，预期的错误载荷必须包含一个名为 `errors` 的属性，其值为一个数组。这对于 `AdapterError` 的所有子类都适用。

假设我们的 API 响应 500 状态码并返回以下载荷：

```json
{
  "errors": [
    {
      "status": "500",
      "title": "There was an error on the server.",
      "detail": "Oh snap! Something went wrong.",
      "foo": "bar"
    },
    {
      "bar": "baz"
    }
  ]
}
```

使用此载荷，我们 `ServerError` 实例上的 `errors` 属性将是：

```javascript
[
  {
    status: '500',
    title: 'There was an error on the server.',
    detail: 'Oh snap! Something went wrong.',
    foo: 'bar'
  },
  {
    bar: 'baz'
  }
];
```

如果响应中没有 `errors` 键，适配器将为我们的 `ServerError` 实例的 `errors` 属性创建以下内容：

```javascript
[
  {
    status: '500',
    title: 'The backend responded with an error', // 此消息由 Ember Data 创建
    detail: '[object Object]' // 载荷的字符串表示形式
  }
];
```

如果 API 返回一个字符串作为载荷，例如 `"FAILED"`，那么 `detail` 将是该字符串：

```javascript
[
  {
    status: '500',
    title: 'The backend responded with an error', // 此消息由 Ember Data 创建
    detail: 'FAILED'
  }
];
```

就我个人而言，我还没有遇到需要规范化非验证错误（`InvalidError`）的载荷的情况，但如果你需要，你可以像之前一样在适配器中覆盖 `handleResponse()` 方法。例如，假设 API 返回了 500 状态码和以下响应：

```json
{
  "status": "500",
  "title": "There was an error on the server.",
  "detail": "Oh snap! Something went wrong.",
  "foo": "bar"
}
```

在这种情况下，我们 `ServerError` 实例上的 `errors` 属性将包含：

```javascript
[
  {
    status: '500',
    title: 'The backend responded with an error', // 此消息由 Ember Data 创建
    detail: '[object Object]' // 载荷的字符串表示形式
  }
];
```

我们可以在 `handleResponse()` 中像这样规范化此错误响应：

```
import RESTAdapter from '@ember-data/adapter/rest';
export default class ApplicationAdapter extends RESTAdapter {
handleResponse(status, headers, payload, requestData) {
if (status === 500) {
let normalizedPayload = {
errors: [payload]
};
return super.handleResponse(
status,
headers,
normalizedPayload,
requestData
);
}
return super.handleResponse(status, headers, payload, requestData);
}
}
```

这里，我们将原始载荷放入一个对象的 `errors` 数组中。有了这个改变，我们 `ServerError` 实例上的 `errors` 属性将包含：

```
[
{
status: '500',
title: 'There was an error on the server.',
detail: 'Oh snap! Something went wrong.',
foo: 'bar'
}
];
```

## 总结

在使用 API 时，处理错误是一个重要的方面，而 Ember Data 足够灵活，可以处理任何错误响应。在本章中，我们研究了 Ember Data 中的默认错误处理，并学习了验证错误和其他适配器错误。接下来，我们将学习测试 Ember Data 自定制的不同方法。