# 5. 从零编写适配器和序列化器

在本章中，我们将学习如何从零编写适配器和序列化器。如果在上一章中没有找到让你的 API 与 Ember Data 配合使用的方法，那么本章和下一章将帮助你更深入地理解适配器和序列化器的协作方式，并向你介绍它们各自的一些核心方法。从零编写适配器和序列化器的过程，将有助于你找到适用于特定边界情况的正确重写方法。此外，如果你需要处理一个差异极大的 API，那么你也会知道如何编写自己的适配器和序列化器！

我们将从重建标准 CRUD 操作的`RESTAdapter`和`RESTSerializer`的简化版本开始。

## 准备工作

我创建了一个简单的联系人应用，它执行所有标准 CRUD 操作。该应用的代码可以在本书源代码的`chapter-5`文件夹中找到。我们将使用这个应用来测试自定义的适配器和序列化器。

API 使用 Ember CLI Mirage ([www.ember-cli-mirage.com/](http://www.ember-cli-mirage.com/)) 进行设置。如果你不熟悉 Mirage，它是一个让我们可以模拟后端的库。

## 自定义适配器和序列化器

首先，让我们创建名为`my-rest`的自定义适配器，它继承自基础适配器类`Adapter`：

```
ember generate adapter my-rest
```

```javascript
// app/adapters/my-rest.js
import Adapter from '@ember-data/adapter';

export default class MyRESTAdapter extends Adapter {}
```

接下来，创建一个`application`适配器，它带有`api`命名空间，并继承自我们的自定义适配器：

```
ember generate adapter application
```

```javascript
import MyRESTAdapter from './my-rest';

export default class ApplicationAdapter extends MyRESTAdapter {
  namespace = 'api';
}
```

对序列化器也进行同样的操作。我们将创建一个名为`my-rest`的自定义序列化器，它继承自基础序列化器类`Serializer`：

```
ember generate serializer my-rest
```

```javascript
// app/serializers/my-rest.js
import Serializer from '@ember-data/serializer';

export default class MyRESTSerializer extends Serializer {}
```

最后，创建一个`application`序列化器，它继承自我们的自定义序列化器：

```
ember generate serializer application
```

```javascript
// app/serializers/application.js
import MyRESTSerializer from './my-rest';

export default class ApplicationSerializer extends MyRESTSerializer {}
```

现在我们已经有了一个继承自定义适配器的`application`适配器，以及一个继承自定义序列化器的`application`序列化器，接下来继续实现代码，使`store.findAll()`能够正常工作。



### 查找所有记录

启动测试应用，访问 `http://localhost:4200/contacts`，然后打开浏览器控制台。你会发现页面抛出了一个错误。这是因为我们的 `application` 适配器和序列化器分别不再继承自内置的 `RESTAdapter` 和 `RESTSerializer`。

让我们从实现必要的方法开始，让 `/contacts` 页面正常工作。如果你查看 `contacts` 路由，会看到以下内容：

```
app/routes/contacts.js
import Route from '@ember/routing/route';
export default class ContactsRoute extends Route {
model() {
return this.store.findAll('contact');
}
}
```

让我们仔细查看一下错误信息。

错误

你尝试加载所有记录，但你的适配器没有实现 `findAll`。

请记住，适配器负责确定如何向 API 发起请求。正如第 2 章——使用适配器与 API 通信——中讨论的，`store.findAll()` 在后台调用了 `adapter.findAll()`。适配器上的 `findAll()` 方法用于查找给定类型的所有记录。让我们继续实现它：

```
app/adapters/my-rest.js
import Adapter from '@ember-data/adapter';
import { pluralize } from 'ember-inflector';
import $ from 'jquery';
export default class MyRESTAdapter extends Adapter {
findAll(store, type, neverSet, snapshotRecordArray) {
let url = '/${this.namespace}/${pluralize(type.modelName)}';
return $.get(url);
}
}
```

`findAll()` 方法接收了几个参数，但我们特别关注的是 `type`，它包含了模型类，该类有一个属性存储了模型的名称。这里我们获取模型名称并将其复数化，以便向 `/api/contacts` 发起 GET 请求。如果我们查看控制台，仍然会看到错误：

错误

`serializer.normalizeResponse is not a function`

`normalizeResponse()` 方法必须在序列化器上实现，它负责将来自服务器的负载规范化为 JSON:API 文档。如前所述，Ember Data 内部使用 JSON:API，即使你的 API 不使用也是如此。目前，我们的 Mirage 后端返回的联系人列表是这样的：

```
{
"contacts": [
{
"id": "1",
"name": "Tom",
"phoneNumber": "(123) 456-7890"
}
]
}
```

让我们继续实现 `normalizeResponse()`，使其接收这个负载并返回一个 JSON:API 文档。如果你不熟悉基本的 JSON:API 结构，请返回第 3 章——API 响应格式和序列化器，阅读“JSONAPISerializer”部分：

```
app/serializers/my-rest.js
import Serializer from '@ember-data/serializer';
export default class MyRESTSerializer extends Serializer {
normalizeResponse(store, primaryModelClass, payload, id, requestType) {
return {
data: payload.contacts.map((resource) => {
return {
id: resource.id,
type: 'contact',
attributes: resource
};
})
};
}
}
```

`normalizeResponse()` 方法有多个参数，但我们关注的是 `payload`。序列化器中还有很多方法，但如果只能记住一个，那就是 `normalizeResponse()`。如果你需要在负载进入 Ember Data 之前对其进行修改，可以随时覆盖此方法。还有其他方法可以覆盖以更高效地操作负载，但要知道你始终可以使用这个方法。这里我们创建了根 `data` 键，其中包含一个联系人列表。每个联系人被重新结构为符合 JSON:API 的格式，包含 `id`、`type` 和 `attributes`。再次检查页面，一切正常！

我们还没有完成。如果我们尝试将这个序列化器用于其他模型，它会失败，因为我们在 `normalizeResponse()` 中硬编码了 `contact`。让我们修改代码使其更通用：

```
app/serializers/my-rest.js
import Serializer from '@ember-data/serializer';
import { pluralize } from 'ember-inflector';
export default class MyRESTSerializer extends Serializer {
normalizeResponse(store, primaryModelClass, payload, id, requestType) {
let pluralizedModelName = pluralize(primaryModelClass.modelName);
return {
data: payload[pluralizedModelName].map((resource) => {
return {
id: resource.id,
type: primaryModelClass.modelName,
attributes: resource
};
})
};
}
}
```

这里我们利用了 `primaryModelClass` 参数，它是我们正在查找的记录的模型类。我们提取了 `modelName` 并将其复数化，以便动态地从根负载键获取数据。加载联系人列表仍然有效！接下来我们继续处理 `findRecord()`。



### 查找单条记录

如果尝试点击某个联系人，我们会发现报错了。在 `contacts.contact` 路由中，我们通过 `id` 动态分段来获取单条联系人的数据：

```python
app/routes/contacts/contact.js
import Route from '@ember/routing/route';
export default class ContactsContactRoute extends Route {
model(params) {
return this.store.findRecord('contact', params.id);
}
}
```

我们需要更新适配器以使 `store.findRecord()` 正常工作。与 `store.findRecord()` 对应的适配器方法，你猜对了，就是 `findRecord()`。让我们来实现它：

```python
app/adapters/my-rest.js
import Adapter from '@ember-data/adapter';
import { pluralize } from 'ember-inflector';
import $ from 'jquery';
export default class MyRESTAdapter extends Adapter {
// ...
findRecord(store, type, id, snapshot) {
let url = '/${this.namespace}/${pluralize(type.modelName)}/${id}';
return  $.get(url);
}
}
```

这个实现与我们之前完成的 `findAll()` 实现类似，但这次我们试图查找的记录 `id` 被附加到了 URL 的末尾。不过，现在它仍然无法工作。控制台输出了以下错误：

Error

Cannot read property ‘map’ of undefined

这个错误现在与负载有关。之前，我们在序列化器中实现了 `normalizeResponse()`，并使用 `Array.prototype.map()` 将负载转换为符合 JSON:API 规范的文档。然而，`GET /api/contacts/:id` 端点返回的是单个资源，而不是数组。对于 `/api/contacts/1` 的负载如下所示：

```json
{
"contact": {
"id": "1",
"name": "Tom",
"phoneNumber": "(123) 456-7890"
}
}
```

为了适应单个资源的响应，我们可以通过几种不同的方式来实现。还记得我说过，如果你要记住一个序列化器方法，那应该是 `normalizeResponse()` 吗？好吧，我们可以在此方法中添加一些逻辑，以测试返回的是对象还是数组：

```python
app/serializers/my-rest.js
import Serializer from '@ember-data/serializer';
import { pluralize } from 'ember-inflector';
export default class MyRESTSerializer extends Serializer {
normalizeResponse(store, primaryModelClass, payload, id, requestType) {
let { modelName } = primaryModelClass;
let pluralizedModelName = pluralize(modelName);
if (Array.isArray(payload[pluralizedModelName])) {
return {
data: payload[pluralizedModelName].map((resource) => {
return {
id: resource.id,
type: modelName,
attributes: resource
};
})
};
}
let resource = payload[modelName];
return {
data: {
id: resource.id,
type: modelName,
attributes: resource
}
};
}
}
```

通过上述代码，我们现在可以点击单个联系人了。不过这个实现仍然不够理想。我们有一些用于生成 JSON:API 资源对象的重复代码。原来有一个专门用于规范化单个资源对象的方法叫 `normalize()`。`normalize()` 方法接收类型（模型类）和资源对象。让我们稍微清理一下代码来实现这一点：

```python
app/serializers/my-rest.js
import Serializer from '@ember-data/serializer';
import { pluralize } from 'ember-inflector';
export default class MyRESTSerializer extends Serializer {
normalizeResponse(store, primaryModelClass, payload, id, requestType) {
let { modelName } = primaryModelClass;
let pluralizedModelName = pluralize(modelName);
if (Array.isArray(payload[pluralizedModelName])) {
return {
data: payload[pluralizedModelName].map((resource) => {
return this.normalize(primaryModelClass, resource);
})
};
}
return {
data: this.normalize(primaryModelClass, payload[modelName])
};
}
normalize(typeClass, hash) {
return {
id: hash.id,
type: typeClass.modelName,
attributes:  hash
};
}
}
```

在上述代码中，`normalize()` 方法用于将单个资源对象（如下所示）：

```json
{
"id": 1,
"name": "Tom",
"phoneNumber": "(123) 456-7890"
}
```

转换为符合 JSON:API 规范的资源，如下所示：

```json
{
"id": 1,
"type": "contact",
"attributes": {
"name": "Tom",
"phoneNumber": "(123) 456-7890"
}
}
```

## 重新审视 `normalizeResponse()`

在继续创建记录之前，让我们重新审视序列化器上的 `normalizeResponse()` 方法。如果我们查看 `normalizeResponse()` 的实际实现^(¹¹)，会看到类似这样的代码：

```python
import Serializer from '@ember-data/serializer';
export default Serializer.extend({
// ...
normalizeResponse(store, primaryModelClass, payload, id, requestType) {
switch (requestType) {
case 'findRecord':
return this.normalizeFindRecordResponse(...arguments);
case 'queryRecord':
return this.normalizeQueryRecordResponse(...arguments);
case 'findAll':
return this.normalizeFindAllResponse(...arguments);
case 'findBelongsTo':
return this.normalizeFindBelongsToResponse(...arguments);
case 'findHasMany':
return this.normalizeFindHasManyResponse(...arguments);
case 'findMany':
return this.normalizeFindManyResponse(...arguments);
case 'query':
return this.normalizeQueryResponse(...arguments);
case 'createRecord':
return this.normalizeCreateRecordResponse(...arguments);
case 'deleteRecord':
return this.normalizeDeleteRecordResponse(...arguments);
case 'updateRecord':
return this.normalizeUpdateRecordResponse(...arguments);
}
}
});
```

Ember Data 通过委托给特定请求类型的规范化方法来实现 `normalizeResponse()`。每当调用 `store.findRecord()` 时，序列化器上的 `normalizeFindRecordResponse()` 方法就会被调用；每当调用 `store.findAll()` 时，`normalizeFindAllResponse()` 方法就会被调用；以此类推。如果我们需要特定 `requestType` 的自定义规范化方法，可以覆盖这些规范化方法之一。

此外，如果我们想用某种方式处理所有返回资源集合的响应，并用另一种方式处理单个资源，也有专门的方法。这些方法是 `normalizeArrayResponse()` 和 `normalizeSingleResponse()`。实际上，每个特定于 `requestType` 的规范化方法在后台都会调用 `normalizeArrayResponse()` 或 `normalizeSingleResponse()`。以下是执行流程（从左到右阅读）：

```
| `normalizeFindAllResponse()`     | `normalizeArrayResponse()`     |     |
| `normalizeFindRecordResponse()`     | `normalizeSingleResponse()`     |     |
| `normalizeCreateRecordResponse()`     | `normalizeSaveResponse()`     | `normalizeSingleResponse()`     |
| `normalizeDeleteRecordResponse()`     | `normalizeSaveResponse()`     | `normalizeSingleResponse()`     |
| `normalizeUpdateRecordResponse()`     | `normalizeSaveResponse()`     | `normalizeSingleResponse()`     |
| `normalizeQueryResponse()`     | `normalizeArrayResponse()`     |     |
| `normalizeQueryRecordResponse()`     | `normalizeSingleResponse()`     |     |
| `normalizeFindBelongsToResponse()`     | `normalizeSingleResponse()`     |     |
| `normalizeFindHasManyResponse()`     | `normalizeArrayResponse()`     |     |
```

希望这能让你更深入地了解序列化器中的规范化工作原理。

既然我们现在已经可以使用 `store.findAll()` 和 `store.findRecord()` 获取数据，接下来让我们继续实现创建记录的功能。



### 创建记录

接下来我们学习创建记录。请访问 `http://localhost:4200/contacts/new`，填写并提交新建联系人的表单。此时你会看到以下错误：

错误

你试图更新一条记录，但你的适配器（针对 `contact`）未实现 `createRecord` 方法

要创建记录，我们需要使用 `store.createRecord()`。但这个方法并不会保存记录。要保存记录，需在记录上调用 `save()` 方法，通过 `POST /api/contacts` 请求将变更持久化，示例如下：

```
app/controllers/contacts/new.js
let contact = this.store.createRecord('contact', {
name: this.name,
phoneNumber: this.phoneNumber
});
contact.save();
```

从 `model.save()` 调用的适配器方法是 `createRecord()`。我们的 API 期望 JSON 载荷的根键名与模型名称匹配，这与获取单条记录时的规则类似：

```
{
"contact": {
"name": "David",
"phoneNumber": "(310) 123-4567"
}
}
```

以下是 `createRecord()` 的实现：

```
app/adapters/my-rest.js
import Adapter from '@ember-data/adapter';
import { pluralize } from 'ember-inflector';
import $ from 'jquery';
export default class MyRESTAdapter extends Adapter {
// ...
createRecord(store, type, snapshot) {
let data = {};
let serializer = store.serializerFor(type.modelName);
serializer.serializeIntoHash(data, type, snapshot);
return $.ajax({
type: 'POST',
url: '/${this.namespace}/${pluralize(type.modelName)}',
data:  JSON.stringify(data)
});
}
}
```

在 `createRecord()` 方法中，我们使用复数形式的模型名构造端点（如 `/api/contacts`）来发起 POST 请求，这样便于其他模型复用。如何获取待发送的数据？数据包含在 `snapshot` 参数中。`snapshot` 参数是 `Snapshot` 类的实例，代表某个时间点上的记录快照。我们稍后会详细讨论。现在你只需要知道它是一个包含待保存记录的对象。

请记住，序列化器负责格式化发送给服务器和从服务器接收的数据。我们本可以在适配器中直接格式化快照数据，但正确的做法应在序列化器中完成。要获取模型的序列化器，可以在 store 上调用 `serializerFor()` 方法并传入模型名。接着，序列化器提供了 `serializeIntoHash()` 方法，用于格式化请求载荷数据。本例中我们使用 `serializeIntoHash()` 来构建 `data` 变量（该变量通过引用修改）。我们稍后实现这个方法。同时传入 `type` 和 `snapshot` 参数。最终 `data` 变量会成为序列化后的 JSON 载荷。

最后，我们需要在序列化器中实现 `serializeIntoHash()` 方法，使发送给服务器的数据符合后端期望的格式：

```
app/serializers/my-rest.js
import Serializer from '@ember-data/serializer';
import { pluralize } from 'ember-inflector';
export default class MyRESTSerializer extends Serializer {
// ...
serializeIntoHash(hash, typeClass, snapshot) {
let serializedData = {};
snapshot.eachAttribute((name) => {
serializedData[name] = snapshot.attr(name);
});
hash[typeClass.modelName] = serializedData;
}
}
```

我们实现的 `serializeIntoHash()` 方法会分析快照中包含的模型数据，生成 API 期望的载荷。快照提供了 `eachAttribute()` 方法，用于遍历模型上的所有属性。通过 `snapshot.attr()` 可以访问记录的属性。在上述代码中，我们遍历所有属性并将其设置到 `serializedData` 对象中，最终发送给服务器。

你可能会疑惑：“为什么 Ember Data 团队要创建这个额外的快照对象，而不是直接使用记录？” 正如之前提到的，快照是 Ember Data 中的一种类，代表某个时间点上的记录。处理记录时，你可以检查异步关联，如果这些关联尚未加载，Ember Data 会触发请求去获取数据。与常规记录不同，快照是一个可以检查记录内容而不会产生副作用的对象（例如不会触发请求）。快照上只有少量你可能常用的属性和方法，如表 5-1 所示。

表 5-1

| 快照属性/方法 | 描述 |
| --- | --- |
| `snapshot.id` | 获取记录的 ID |
| `snapshot.attr('name')` | 获取记录的某个属性 |
| `snapshot.hasMany('emails')` | 获取记录的 hasMany 关联，返回另一个快照 |
| `snapshot.belongsTo('company')` | 获取记录的 belongsTo 关联，返回另一个快照 |
| `snapshot.record` | 获取原始记录 |
| `snapshot.eachAttribute(callback, binding)` | 遍历所有模型属性，并对每个属性调用回调函数 |

现在如果你查看 `RESTSerializer` 的源码，会发现它实际上将这个功能拆分为两个独立的方法：`serializeIntoHash()` 和 `serialize()`。我们可以调整实现，使其更贴近 Ember Data 的做法：

```
app/serializers/my-rest.js
import Serializer from '@ember-data/serializer';
import { pluralize } from 'ember-inflector';
export default class MyRESTSerializer extends Serializer {
// ...
serializeIntoHash(hash, typeClass, snapshot) {
hash[typeClass.modelName] = this.serialize(snapshot);
}
serialize(snapshot) {
let serializedData = {};
snapshot.eachAttribute((name) => {
serializedData[name] = snapshot.attr(name);
});
return serializedData;
}
}
```

`serialize()` 方法负责从快照中提取数据并按需格式化，而 `serializeIntoHash()` 方法则负责自定义根载荷的键名。

太棒了！现在我们已经能够查找全部记录、查找单条记录以及创建记录。接下来我们继续处理记录的更新操作。



### 更新记录

请随意点击一个联系人。你将看到一个弹出式模态框，允许我们编辑联系人信息。

现在我们已经实现了 `createRecord()`，`updateRecord()` 的实现方式与之极为相似。两者的区别在于 URL 和请求类型。首先，URL 需要包含待更新记录的`id`，例如 `/api/contacts/1` 而非 `/api/contacts`。其次，使用 PUT HTTP 方法替代 POST。主要区别仅此而已。

如果尝试提交表单，将会出现以下错误：

错误

你尝试更新一条记录，但你的适配器（针对联系人）未实现 `updateRecord`

接下来让我们实现该功能：

```
app/adapters/my-rest.js
import Adapter from '@ember-data/adapter';
import { pluralize } from 'ember-inflector';
import $ from 'jquery';
export default class MyRESTAdapter extends Adapter {
updateRecord(store, type, snapshot) {
let data = {};
let serializer = store.serializerFor(type.modelName);
serializer.serializeIntoHash(data, type, snapshot);
return  $.ajax({
type: 'PUT',
url: '/${this.namespace}/${pluralize(type.modelName)}/${snapshot.id}',
data: JSON.stringify(data)
});
}
}
```

我们需要对序列化器做一个小调整，具体来说是 `serialize()` 方法。当前序列化记录时，会创建一个包含所有模型属性的普通对象，但如果存在`id`，则不会包含在内。我们需要添加一个检查逻辑，使得当快照包含`id`时，该`id`也会被包含在序列化后的数据载荷中：

```
app/serializers/my-rest.js
import Serializer from '@ember-data/serializer';
import { pluralize } from 'ember-inflector';
export default class MyRESTSerializer extends Serializer {
// ...
serializeIntoHash(hash, typeClass, snapshot) {
hash[typeClass.modelName] = this.serialize(snapshot);
}
serialize(snapshot) {
let serializedData = {};
if (snapshot.id) {
serializedData.id = snapshot.id;
}
snapshot.eachAttribute((name) => {
serializedData[name] = snapshot.attr(name);
});
return serializedData;
}
}
```

更新记录就这些内容！现在我们已经能够更新记录，接下来继续实现删除记录的功能。

### 删除记录

请点击表格中任一联系人的"删除"按钮。你应该会看到以下错误：

错误

你尝试更新一条记录，但你的适配器（针对联系人）未实现 `deleteRecord`

要删除联系人，我们需要向 `/contacts/:id` 发送一个 DELETE 请求，API 将返回空响应。为此，我们可以调用模型上的 `destroyRecord()` 方法。模型上的 `destroyRecord()` 方法会映射到适配器的 `deleteRecord()` 方法。让我们实现它：

```
app/adapters/my-rest.js
import Adapter from '@ember-data/adapter';
import { pluralize } from 'ember-inflector';
import $ from 'jquery';
export default class MyRESTAdapter extends Adapter {
// ...
deleteRecord(store, type, snapshot) {
return  $.ajax({
type: 'DELETE',
url: '/${this.namespace}/${pluralize(type.modelName)}/${snapshot.id }'
});
}
}
```

在 `deleteRecord()` 方法中，我们使用 DELETE HTTP 方法向与 `findRecord()` 和 `updateRecord()` 中相同的 URL 发送 AJAX 请求。试试看，你会发现一切运行正常。

恭喜！你已经为标准的 CRUD 操作完成了自定义 REST 适配器和序列化器的开发！

## 总结

本章我们探讨了如何从 Ember Data 提供的基础类开始，从零编写自己的适配器和序列化器。适配器和序列化器中还有其他未在本章涉及的方法。虽然我们可以继续逐一讲解每个适配器和序列化器方法，但我们已经涵盖的内容应该能让你对 Ember Data 这两个核心组件如何协同工作有必要的认知。当需要更多功能时，在文档中找到合适的适配器或序列化器方法应该会变得轻而易举。在下一章中，我们将继续使用同一个应用程序，通过简单地更换适配器将后端切换为 Local Storage，因为适配器负责确定如何获取数据以及将数据发送到何处进行存储。

脚注 1

