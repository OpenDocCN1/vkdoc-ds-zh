# 常见的适配器和序列化器自定义

到目前为止，你已经了解了 Ember Data 各组件的职责。你可能在想，这很好，但我只想让我的 API 尽快与 Ember Data 协同工作！这正是本章的目的。我无法覆盖所有边缘情况，但本章的目标是向你介绍那些为了将 Ember Data 与自定义 API 集成而经常被重写的方法和属性。

## 修改 RESTful URL 路径

并非所有 API 都遵循 Ember Data 预期的 RESTful URL 约定（详情参见第 2 章——使用适配器与 API 通信）。例如，如果我们的 API URL 使用资源的单数形式而非复数形式，比如使用 `/contact/1` 而不是 `/contacts/1` 呢？如果我们所有的 `contact` 端点都遵循这种单数形式约定，我们可以在 `contact` 适配器中重写 `pathForType()` 方法：

```
app/adapters/contact.js
import ApplicationAdapter from './application';
export default class ContactAdapter extends ApplicationAdapter {
  pathForType(modelName) {
    return modelName;
  }
}
```

这里我们直接返回了模型名称，它本身就是单数形式。

如果 API 中所有的 URL 都遵循这种单数资源路径约定，我们可以将此逻辑放在 `application` 适配器中：

```
app/adapters/application.js
import RESTAdapter from '@ember-data/adapter/rest';
export default class ApplicationAdapter extends RESTAdapter {
  pathForType(modelName) {
    return modelName;
  }
}
```

## 为特定操作修改 URL

现在，假设我们想要查询某个城市的所有联系人：

```
this.store.query('contact', {
  city: 'Los Angeles'
});
```

Ember Data 的默认行为是发起一个 GET 请求，其中城市信息以查询字符串参数的形式呈现，例如 `/api/contacts?city=Los+Angeles`。然而，假设我们的端点期望城市以动态路径段的形式出现，例如 `/api/contacts/los-angeles`。因为这只与联系人有关，我们可以创建一个 `contact` 适配器来处理 URL 的修改。我们首先创建 `application` 适配器：

```
app/adapters/application.js
import RESTAdapter from '@ember-data/adapter/rest';
export default class ApplicationAdapter extends RESTAdapter {
  host = 'http://myapi.com';
  namespace = 'api';
}
```

接下来，我们可以重写 `urlForQuery()` 方法。每当调用 `store.query()` 时，该方法负责返回 URL：

```
app/adapters/contact.js
import ApplicationAdapter from './application';
import { dasherize } from '@ember/string';
export default class ContactAdapter extends ApplicationAdapter {
  urlForQuery(query, modelName) {
    let city = dasherize(query.city.toLowerCase());
    delete query.city;
    return '${this.host}/${this.namespace}/contacts/${city}';
  }
}
```

适配器还有其他类似的方法用于常见的 store 操作，例如 `urlForCreateRecord()`、`urlForDeleteRecord()` 和 `urlForFindAll()` 等。

## 将不同命名的载荷键映射到模型属性

如果我们的 API 返回的载荷中，键名不符合 JavaScript 约定的驼峰式（camelCased）命名，该怎么办？这些键可能是下划线式（snake_cased）、标题式（TitleCased）或混合约定。我们可以让模型属性遵循 API 的风格和命名约定，但这可能会降低我们 JavaScript 代码的直观性和一致性。假设我们收到以下载荷：

```
[
  {
    "id": 1,
    "first_name": "David",
    "years_old": 40
  }
]
```

我们可以使用序列化器上的 `attrs` 属性来实现载荷键与模型属性之间的相互映射：

```
app/serializers/contact.js
import JSONSerializer from '@ember-data/serializer/json';
export default class ContactSerializer extends JSONSerializer {
  attrs = {
    firstName: 'first_name',
    age: 'years_old'
  };
}
```

载荷中的 `first_name` 键将被映射到模型上的 `firstName` 属性。类似地，`years_old` 键将被映射到模型上的 `age` 属性。当数据被序列化并发送回服务器时，这些属性将被重新映射回它们原始的键。

## 将外键映射到关联关系

如果你想将特定的载荷键映射到模型属性，使用 `attrs` 会很方便。但是，如果我们的 API 暴露了遵循 `XXX_id` 约定的外键，这些外键旨在用于 `belongsTo` 关联关系呢？这在关系型数据库中是非常常见的约定，并且在 API 中也常有体现。例如，每个 `contact` 可能有一个 `company_id` 属性而不是 `company` 属性：

```
{
  "contact": {
    "id": 1,
    "name": "David",
    "company_id": 3
  }
}
```

为每个模型都创建模型专属的序列化器并在 `attrs` 中指定属性映射可能会很繁琐。相反，更好的方法是在序列化器中重写 `keyForRelationship()` 方法：

```
app/serializers/application.js
import RESTSerializer from '@ember-data/serializer/rest';
export default class ApplicationSerializer extends RESTSerializer {
  keyForRelationship(key, relationship) {
    if (relationship === 'belongsTo') {
      return '${key}_id';
    }
    return super.keyForRelationship(key, relationship);
  }
}
```

现在，像 `company_id` 这样包含每个 `contact` 资源外键的 JSON 载荷，就能映射到我们 `contact` 模型上对应的 `belongsTo` 关联关系了：

```
app/models/contact.js
import Model, { attr, belongsTo } from '@ember-data/model';
export default class ContactModel extends Model {
  @attr('string') name;
  @belongsTo('company') company;
}
```

## 设置主键

Ember Data 要求每条记录都有一个名为 `id` 的属性。如果记录需要使用另一个键作为其 `id`，你可以覆盖 `primaryKey` 属性：

```
app/serializers/contact.js
import RESTSerializer from '@ember-data/serializer/rest';
export default class ContactSerializer extends RESTSerializer {
  primaryKey = 'ssn'; // 社会安全号码
}
```

这里我们将`contact`模型的`id`更改为使用`ssn`。我们可以访问任何`contact`记录上的`id`来获取`ssn`。如果所有记录都使用像`_id`这样的属性作为`id`（例如在 MongoDB 中），那么可以在`application`序列化器中覆盖`primaryKey`，这样它将应用于所有模型。

## 规范化响应

假设我们调用了`store.findAll('contact')`，并且返回了来自第 1 章 —— Ember Data 概述中的示例 JSON：

```json
{
  "data": [
    { "id": 1, "firstName": "Yehuda" },
    { "id": 2, "firstName": "Tom" }
  ]
}
```

默认情况下，Ember Data 中的内置序列化器都无法直接处理这种格式的 API 负载。这种格式类似于`JSONSerializer`所期望的格式，只是多了一个根级别的`data`键。我们只需要提取出`data`，可以通过覆盖`normalizeResponse()`来实现：

```javascript
app/serializers/contact.js
import JSONSerializer from '@ember-data/serializer/json';
export default class ContactSerializer extends JSONSerializer {
  normalizeResponse(store, primaryModelClass, payload, id, requestType) {
    return super.normalizeResponse(
      store,
      primaryModelClass,
      payload.data,
      id,
      requestType
    );
  }
}
```

`normalizeResponse()`方法用于将来自服务器的负载规范化为规范的 JSON:API 文档，我们在第 3 章 —— API 响应格式和序列化器中讨论过。我们不必直接创建规范的 JSON:API，而是可以修改负载以使其符合`JSONSerializer`的结构，并通过`super`调用父类上的相同方法，这将为我们返回规范的 JSON:API。这种方法需要编写的代码少得多，无需我们手动将负载转换为规范的 JSON:API 格式。如果你想查看规范的 JSON:API，可以执行以下操作：

```javascript
app/serializers/contact.js
import JSONSerializer from '@ember-data/serializer/json';
export default class ContactSerializer extends JSONSerializer {
  normalizeResponse(store, primaryModelClass, payload, id, requestType) {
    let json = super.normalizeResponse(
      store,
      primaryModelClass,
      payload.data,
      id,
      requestType
    );
    console.log(JSON.stringify(json));
    return json;
  }
}
```

你应该会在控制台中看到以下规范的 JSON:API 被记录：

```json
{
  "data": [
    {
      "id": "1",
      "type": "contact",
      "attributes": { "firstName": "Yehuda" },
      "relationships": {}
    },
    {
      "id": "2",
      "type": "contact",
      "attributes": { "firstName": "Tom" },
      "relationships": {}
    }
  ],
  "included": []
}
```

注意到这种格式与我们第 3 章 —— API 响应格式和序列化器中讨论的规范化 JSON:API 格式是否一致？也就是说，`type`是单数、短横线命名的形式，像`firstName`这样的属性是驼峰命名的。

处理此响应的另一种方法是将负载中的`data`键更改为模型名称，并使用`RESTSerializer`：

```javascript
app/serializers/contact.js
import RESTSerializer from '@ember-data/serializer/rest';
export default class ContactSerializer extends RESTSerializer {
  normalizeResponse(store, primaryModelClass, payload, id, requestType) {
    let newPayload = {};
    newPayload[primaryModelClass.modelName] = payload.data;
    return super.normalizeResponse(
      store,
      primaryModelClass,
      newPayload,
      id,
      requestType
    );
  }
}
```

如果你回想一下第 3 章 —— API 响应格式和序列化器，对于`RESTSerializer`，根键是模型名称，它可以是单数也可以是复数。在上面的代码中，使用的根键是单数形式的模型名称。与之前类似，我们将在`normalizeResponse()`中修改负载结构，使其符合当前序列化器的预期格式，然后调用`super.normalizeResponse()`。

## 按存储库调用方式规范化响应

在上一节中，我们通过覆盖`normalizeResponse()`来处理负载中的`data`键。这将适用于所有`contact`负载对应的不同 RESTful 操作。如果这个`data`键只存在于某些 RESTful 端点呢？我们可以向`normalizeResponse()`中添加一些条件逻辑，但这可能会变得混乱。如果我们只想在调用`store.findAll()`时提取该`data`键，我们可以改为覆盖一个特定的序列化器方法，该方法在`store.findAll()`被调用时使用。这个方法就是`normalizeFindAllResponse()`：

```javascript
app/serializers/contact.js
import JSONSerializer from '@ember-data/serializer/json';
export default class ContactSerializer extends JSONSerializer {
  normalizeFindAllResponse(store, primaryModelClass, payload, id, requestType) {
    return super.normalizeFindAllResponse(
      store,
      primaryModelClass,
      payload.data,
      id,
      requestType
    );
  }
}
```

这与之前完全相同，只是将覆盖`normalizeResponse()`改为了覆盖`normalizeFindAllResponse()`。在上面的代码中，使用了`JSONSerializer`，但我们也可以像上一节那样使用`RESTSerializer`。

表 4-1 显示了其他序列化器规范化方法及其如何被调用。

**表 4-1** 序列化器规范化方法

| 存储库或模型调用 | 序列化器方法 |
| --- | --- |
| `store.findAll()` | `normalizeFindAllResponse()` |
| `store.findRecord()` | `normalizeFindRecordResponse()` |
| `model.save()` | `normalizeCreateRecordResponse()` |
| `store.deleteRecord()` | `normalizeDeleteRecordResponse()` |
| `store.query()` | `normalizeQueryResponse()` |
| `store.queryRecord()` | `normalizeQueryRecordResponse()`、`normalizeFindBelongsToResponse()`、`normalizeFindHasManyResponse()` |

当`belongsTo`和`hasMany`关系被请求时，会调用`normalizeFindBelongsToResponse()`和`normalizeFindHasManyResponse()`。

## 标准化单一资源对象

假设我们的`contact`API 数据负载符合内置序列化器格式之一。例如，`/api/contacts`可能返回：

```
[
  {
    "id": 1,
    "name": { "first": "John", "last": "Doe" }
  },
  {
    "id": 2,
    "name": { "first": "Jane", "last": "Doe" }
  }
]
```

而`/api/contacts/:id`可能返回：

```
{
  "id": 1,
  "name": { "first": "John", "last": "Doe" }
}
```

这两种数据负载都符合`JSONSerializer`格式，但每个联系人中的`name`是嵌套对象。为了拼接`name.first`和`name.last`，我们可以重写`normalize()`方法，该方法可将数据负载中的单一资源对象标准化为 JSON:API 对象：

```javascript
// app/serializers/contact.js
import JSONSerializer from '@ember-data/serializer/json';

export default class ContactSerializer extends JSONSerializer {
  normalize(modelClass, resourceHash, prop) {
    let { name } = resourceHash;

    return {
      data: {
        id: resourceHash.id,
        type: 'contact',
        attributes: {
          name: `${name.first} ${name.last}`
        }
      }
    };
  }
}
```

在`normalize()`方法中，我们处理了每个`contact`并将其标准化为 JSON:API 格式。对于像`/api/contacts/:id`这样的单一资源端点，`normalize()`方法只被调用一次。而对于返回资源集合的端点（如`/api/contacts`），`normalize()`则会为每个资源对象分别调用。

与其亲手将每个`contact`转换为 JSON:API，我们还可以使用`super`调用父类的同一方法，以更高效的方式实现相同效果：

```javascript
// app/serializers/contact.js
import JSONSerializer from '@ember-data/serializer/json';

export default class ContactSerializer extends JSONSerializer {
  normalize(modelClass, resourceHash, prop) {
    let { name } = resourceHash;
    resourceHash.name = `${name.first} ${name.last}`;
    return super.normalize(...arguments);
  }
}
```

在上述代码中，我们修改了每个作为`resourceHash`传入`normalize()`的对象，然后通过`super`调用父类的原始`normalize()`方法。这种方法不仅代码量更少，而且让 Ember Data 自行处理 JSON:API 的具体细节以及我们在处理更复杂响应时未考虑到的其他场景。

## 总结

在本章中，我介绍了一些在适配器和序列化器中常用的自定义方法，以使 Ember Data 能与自定义 API 协同工作。如果本章内容恰好解决了你当前的问题，那我很高兴能帮到你！不过，如果你对适配器和序列化器如何协同工作仍有些疑惑，请继续阅读接下来的两章，我们将从零开始构建两个适配器和一个序列化器。