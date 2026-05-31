# 10. 测试适配器和序列化器

在本章中，我们将探讨如何测试适配器和序列化器。

## 测试适配器

每当我们生成一个适配器时，都会同时生成一个对应的单元测试。以下是默认测试：

```
tests/unit/adapters/application-test.js
import { module, test } from 'qunit';
import { setupTest } from 'ember-qunit';
module('Unit | Adapter | application', function (hooks) {
setupTest(hooks);
// 替换为你的真实测试。
test('it exists', function (assert) {
let adapter = this.owner.lookup('adapter:application');
assert.ok(adapter);
});
});
```

适配器非常容易测试。我们可以通过 `this.owner.lookup()` 在容器中查找适配器实例来获取它。然后，我们可以调用任何我们覆盖过的方法。

在第 9 章"处理自定义错误响应"中，我们了解到状态码为 422 的响应被视为服务器端验证错误。如果我们希望所有状态码为 400 或 422 的响应都被视为验证错误，该怎么办？正如我们在上一章中所做的那样，我们可以覆盖 `isInvalid()` 方法：

```
app/adapters/application.js
import RESTAdapter from '@ember-data/adapter/rest';
export default class ApplicationAdapter extends RESTAdapter {
isInvalid(status) {
return status === 400 || status === 422;
}
}
```

要对此进行测试，我们可以使用 400 和 422 状态码调用 `isInvalid()`，并断言返回值为 `true`。我们还可以使用既不是 400 也不是 422 的状态码调用它，并断言返回值为 `false`：

```
tests/unit/adapters/application-test.js
import { module, test } from 'qunit';
import { setupTest } from 'ember-qunit';
module('Unit | Adapter | application', function (hooks) {
setupTest(hooks);
test('isInvalid() 对于 400 状态码返回 true', function (assert) {
let adapter = this.owner.lookup('adapter:application');
assert.ok(adapter.isInvalid(400));
});
test('isInvalid() 对于 422 状态码返回 true', function (assert) {
let adapter = this.owner.lookup('adapter:application');
assert.ok(adapter.isInvalid(422));
});
test('isInvalid() 对于 500 状态码返回 false', function (assert) {
let adapter = this.owner.lookup('adapter:application');
assert.notOk(adapter.isInvalid(500));
});
});
```

单元测试适配器基本上就是这样。在本章后面，我们将探讨如何将适配器和序列化器放在一起进行测试。



## 测试序列化器

每次生成序列化器时，都会伴随生成一个包含两个测试的单元测试文件。如果你还没有为 `application` 序列化器创建单元测试，请立即创建一个：

```
ember generate serializer-test application
```

你应该会在 `tests/unit/serializers/application-test.js` 中看到以下内容：

```
tests/unit/serializers/application-test.js
import { module, test } from 'qunit';
import { setupTest } from 'ember-qunit';
module('Unit | Serializer | application', function (hooks) {
setupTest(hooks);
// 在此处替换为你的实际测试。
test('it exists', function (assert) {
let store = this.owner.lookup('service:store');
let serializer = store.serializerFor('application');
assert.ok(serializer);
});
test('it serializes records', function (assert) {
let store = this.owner.lookup('service:store');
let record = store.createRecord('application', {});
let serializedRecord = record.serialize();
assert.ok(serializedRecord);
});
});
```

第一个测试展示了一个序列化器测试示例，我们可以通过存储实例直接获取序列化器，对其调用方法，并针对结果编写断言。第二个测试展示了一个通过记录测试序列化逻辑的示例。由于 `application` 序列化器不绑定特定模型（即不存在 `application` 模型），我们可以删除第二个测试。

现在，让我们编写几个 `application` 序列化器测试，验证载荷是否被正确标准化和序列化。假设我们的 API 遵循 `RESTSerializer` 格式，并且载荷中的所有键都使用蛇形命名法（snake_case）。例如，`GET /api/contacts` 可能返回以下内容：

```
{
"contacts": [
{ "id": 1, "first_name": "Tom" },
{ "id": 2, "first_name": "Yehuda" }
]
}
```

让我们编写一个测试来验证像 `first_name` 这样的蛇形命名键是否映射到模型上的驼峰命名（camelCase）属性，例如 `firstName`。`keyForAttribute()` 方法可用于定义如何将模型中的属性名称转换为 JSON 载荷中的键的规则：

```
tests/unit/serializers/application-test.js
import { module, test } from 'qunit';
import { setupTest } from 'ember-qunit';
module('Unit | Serializer | application', function (hooks) {
setupTest(hooks);
test('snake_cased JSON attribute keys map to camelCased model attributes', function (assert) {
let store = this.owner.lookup('service:store');
let serializer = store.serializerFor('application');
assert.equal(serializer.keyForAttribute('firstName'), 'first_name');
});
});
```

此时测试应该会失败。为了让测试通过，让我们在 `application` 序列化器中实现 `keyForAttribute()`：

```
app/serializers/application.js
import RESTSerializer from '@ember-data/serializer/rest';
import { underscore } from '@ember/string';
export default class ApplicationSerializer extends RESTSerializer {
keyForAttribute(attr) {
return underscore(attr);
}
}
```

成功！测试现在通过了。

在应用中，假设我们用以下代码更新了某个地方的 `contact` 记录：

```
contact.firstName = firstName;
contact.save();
```

发出的 `PUT /contacts/:id` 请求的根键将是：

```
{
"contact": {
"first_name": "David"
}
}
```

如果我们希望所有发出请求的根键都是复数形式，可以重写 `payloadKeyFromModelName()` 方法。让我们先编写一个测试：

```
tests/unit/serializers/application-test.js
import { module, test } from 'qunit';
import { setupTest } from 'ember-qunit';
module('Unit | Serializer | application', function (hooks) {
setupTest(hooks);
test('snake_cased JSON attribute keys map to camelCased model attributes', function (assert) {
// ...
});
test('the root key for all outgoing requests is plural', function (assert) {
let store = this.owner.lookup('service:store');
let serializer = store.serializerFor('application');
let type = serializer.payloadKeyFromModelName('contact');
assert.equal(type, 'contacts');
});
});
```

以下是通过测试的实现代码：

```
app/serializers/application.js
import RESTSerializer from '@ember-data/serializer/rest';
import { underscore } from '@ember/string';
import { pluralize } from 'ember-inflector';
export default class ApplicationSerializer extends RESTSerializer {
// ...
payloadKeyFromModelName(modelName) {
let rootKey = super.payloadKeyFromModelName(modelName);
return pluralize(rootKey);
}
}
```

我发现，像本节所示，通过调用序列化器实例上的方法来测试序列化器，在测试适用于多个模型的通用自定义（如 `application` 序列化器）时非常有用。如果自定义内容非常简单（如前面提到的那些），我的验收测试通常可以覆盖这些情况，因此不需要专门的序列化器测试。无论如何，你现在知道了在需要时如何对序列化器进行单元测试。

## 联合测试适配器和序列化器

通常，我们不会编写直接与适配器和序列化器交互的代码。我们通过存储间接使用适配器和序列化器。在某些情况下，我们可能需要编写一个更高级别的测试，来验证所有适配器和序列化器的自定义是否协同工作。在这些情况下，编写一个利用存储的测试会很有帮助，就像我们在应用中如何使用存储一样。让我们看几个例子。



### 测试规范化

在前面的章节中，我们学习了 `normalizeResponse()` 方法，它用于将服务器返回的负载规范化为 JSON:API 文档。现在，让我们看看如何通过 store 来测试规范化方法。假设我们有一个 `GET /api/contacts` 的 API 端点，它返回如下 JSON：

```
{
"data": [
{ "id": 1, "name": "Tom Dale" },
{ "id": 2, "name": "Yehuda Katz" }
]
}
```

我们希望提取出 `data` 键，就像我们在第 4 章——通用适配器和序列化器定制——中所做的那样，这样当我们调用 `store.findAll()` 时，Ember Data 就能正确规范化 `data` 键下的联系人列表。我们先从测试开始：

```
tests/unit/serializers/contact.js
import { module, test } from 'qunit';
import { setupTest } from 'ember-qunit';
import { setupMirage } from 'ember-cli-mirage/test-support';
import { Response } from 'miragejs';
module('单元测试 | 序列化器 | contact', function (hooks) {
setupTest(hooks);
setupMirage(hooks);
test('规范化 findAll()', async function (assert) {
this.server.get('/api/contacts', function () {
return new Response(
200,
{},
{
data: [
{ id: 1, name: 'Fiona' },
{ id: 2, name: 'Steve' }
]
}
);
});
let store = this.owner.lookup('service:store');
let contacts = await store.findAll('contact');
assert.equal(contacts.length, 2);
});
});
```

这个测试使用 Ember CLI Mirage^((16)) 来模拟 API。对于 `GET /api/contacts` 端点，我们指定了一个静态响应，其格式与 API 预期返回的格式相同。接下来，我们可以从容器中获取 store 并调用 `findAll()`。当 promise 解析时，会执行一个断言来验证 `RecordArray` 的长度是否等于响应中 `data` 键下的联系人数量。

现在，让我们编写实现来使测试通过。首先，确保我们的适配器具有 `api` 命名空间：

```
app/adapters/application.js
import RESTAdapter from '@ember-data/adapter/rest';
export default class ApplicationAdapter extends RESTAdapter {
namespace = 'api';
}
```

其次，这是我们序列化器的实现，我们在前面的第 4 章——通用适配器和序列化器定制——中看到过：

```
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

有了这个实现，我们的测试通过了。

我倾向于用这种方式测试适配器和序列化器定制，原因有几个。首先，测试中的代码与我将在应用程序中编写的大部分代码相似。我的应用程序调用 store 上的方法，而不是适配器和序列化器上的方法。我发现这种方法通常使我的测试更容易理解。其次，这个测试同时触发了我们的适配器和序列化器。第三，这种类型的测试在重构时创造了更多的灵活性。例如，假设后来我决定只在期望返回数组时才进行规范化，比如调用 `store.findAll()` 或 `store.query()` 时。在这种情况下，我应该重写 `normalizeArrayResponse()` 方法，正如我们在第 5 章——从头编写适配器和序列化器——中学到的。更新后的序列化器实现如下所示：

```
app/serializers/contact.js
import JSONSerializer from '@ember-data/serializer/json';
export default class ContactSerializer extends JSONSerializer {
normalizeArrayResponse(store, primaryModelClass, payload, id, requestType) {
return super.normalizeArrayResponse(
store,
primaryModelClass,
payload.data,
id,
requestType
);
}
}
```

尽管我更改了规范化实现，但我的测试无需更改。

### 测试序列化

现在，让我们测试序列化器在创建和更新资源时能否正确序列化数据。当我们创建一个新的 `contact` 记录并调用 `save()` 时，我们希望验证请求负载的格式是否符合 API 的预期。假设 API 期望 `POST /api/contacts` 的请求负载如下所示：

```
{
"data": {
"firstName": "Zoey"
}
}
```

同时，假设 `POST /api/contacts` 的响应负载如下所示：

```
{
"data": {
"id": "1",
"firstName": "Zoey"
}
}
```

继续上一个示例，其中我们的 `contact` 序列化器继承了 `JSONSerializer`，默认发送给 API 的请求负载如下所示：

```
{
"firstName": "Zoey"
}
```

默认响应负载将是：

```
{
"id": "1",
"firstName": "Zoey"
}
```

让我们编写一个测试来捕获我们需要进行的序列化定制：

```
tests/unit/serializers/contact-test.js
import { module, test } from 'qunit';
import { setupTest } from 'ember-qunit';
import { setupMirage } from 'ember-cli-mirage/test-support';
import { Response } from 'miragejs';
module('单元测试 | 序列化器 | contact', function (hooks) {
setupTest(hooks);
setupMirage(hooks);
test('规范化 findAll()', async function (assert) {
// ...
});
test('序列化', async function (assert) {
this.server.post('/api/contacts', function (schema, request) {
let requestPayload = JSON.parse(request.requestBody);
assert.deepEqual(requestPayload, {
data: {
firstName: 'Zoey'
}
});
return new Response(
201,
{},
{
data: {
id: '1',
firstName: 'Zoey'
}
}
);
});
let store = this.owner.lookup('service:store');
let contact = store.createRecord('contact', {
firstName: 'Zoey'
});
await contact.save();
assert.equal(contact.id, '1');
});
});
```

在上述测试中，我们模拟了 `POST /api/contacts` 端点，创建了一个 `contact` 记录并保存它。在我们模拟的 Mirage 路由处理程序中，我们对请求负载编写了一个断言。每个请求对象都有一个 `requestBody` 属性，其中包含作为 JSON 字符串的请求负载。通过它，我们可以检查数据是否被正确“发送”。最后，我们返回预期的响应。这个测试还断言从服务器返回的 `id` 已被设置到我们的 `contact` 记录上，这验证了响应被正确规范化。

为了使测试通过，我们需要进行两次序列化器定制。首先，我们需要确保请求负载有一个 `data` 根键。正如我们在第 5 章——从头编写适配器和序列化器——中学到的，我们可以重写 `serializeIntoHash()` 方法来控制根键。其次，我们需要规范化响应并从负载中提取根 `data` 键，以便 Ember Data 可以使用资源对象并将 `id` 分配给 `contact` 记录。我们可以为此重写 `normalizeCreateRecordResponse()` 方法。以下是两个定制的实现：

```
app/serializers/contact.js
import JSONSerializer from '@ember-data/serializer/json';
export default class ContactSerializer extends JSONSerializer {
serializeIntoHash(hash, type, snapshot, options) {
hash.data = this.serialize(snapshot, options);
}
normalizeCreateRecordResponse(
store,
primaryModelClass,
payload,
id,
requestType
) {
return super.normalizeCreateRecordResponse(
store,
primaryModelClass,
payload.data,
id,
requestType
);
}
}
```

现在，我们的测试通过了！



不过仍有改进空间。观察我们的测试，顶部有一条断言，底部还有一条断言。我个人更倾向于测试遵循“准备-执行-断言（AAA）”模式。如果你不熟悉这种模式，它是一种组织测试不同部分的方式。“准备”阶段包括设置测试环境，比如模拟 API 端点。“执行”阶段包括调用被测方法或函数。在这个例子中，“执行”阶段是“`contact.save()`”。“断言”阶段则包含所有断言。遵循这种模式可以提高测试的可读性，并使所有断言都集中在测试的底部。让我们看看如何重构测试以遵循“准备-执行-断言”模式。

首先，安装 Sinon.js，^((17)) 这是一个为 JavaScript 提供间谍、存根和模拟功能的库：

```
npm i sinon --save-dev
```

接着，在测试文件中导入 Sinon：

```
tests/unit/serializers/contact-test.js
import sinon from 'sinon';
```

以下是重构后的测试：

```
test('序列化（使用 sinon）', async function (assert) {
// 准备
let routeHandler = sinon.stub().returns(
  new Response(
    201,
    {},
    {
      data: {
        id: '1',
        firstName: 'Zoey'
      }
    }
  )
);
this.server.post('/api/contacts', routeHandler);
let store = this.owner.lookup('service:store');
// 执行
let contact = store.createRecord('contact', {
  firstName: 'Zoey'
});
await contact.save();
// 断言
assert.equal(contact.id, '1');
let request = routeHandler.getCall(0).args[1];
let requestPayload = JSON.parse(request.requestBody);
assert.deepEqual(requestPayload, {
  data: { firstName: 'Zoey' }
});
});
```

我们修改了测试，使用 Sinon 提供的测试存根来替代 Mirage 路由处理器。测试存根是具有预设行为的函数。在这个例子中，我们创建了一个名为 `routeHandler` 的存根，它在保存新的 `contact` 记录时返回预期的响应。Sinon 中的存根会收集关于其使用方式的信息，例如被调用的次数、每次调用的参数和返回值。在这个场景中，我们特别关注捕获存根被调用时的请求参数，以便访问请求的有效载荷。我们可以通过 `routeHandler.getCall(0).args[1]` 获取这些信息。然后，我们解析 `request.requestBody` 并编写断言。

测试通过了！我们成功地将测试重构为遵循“准备-执行-断言”模式，使得所有断言都集中在测试的末尾，在我看来这样测试更易读。

## 总结

在本章中，我们探讨了如何对适配器和序列化器进行单元测试，以及如何通过存储间接地测试适配器和序列化器的协同工作。归根结底，要采用最适合您、能为应用程序带来最大价值的测试方式。

