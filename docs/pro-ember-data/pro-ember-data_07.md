# 6. 将 API 替换为 Local Storage

通过上一章的学习，我们已经知道如何从零编写适配器和序列化器。假设我们的任务是将后端切换为 Local Storage。我们知道适配器负责通过 RESTful API 与后端通信，确定如何检索数据以及将数据发送到何处进行存储。本章我们将用自定义的 Local Storage 适配器替换原有的 `RESTAdapter`，应用程序将继续无缝运行。

## 创建空适配器

首先，让我们创建一个新的空适配器：

```
ember generate adapter local-storage
app/adapters/local-storage.js
import Adapter from '@ember-data/adapter';
export default class LocalStorageAdapter extends Adapter {}
```

接下来，更新我们的 `application` 适配器，使其继承自 Local Storage 适配器：

```
app/adapters/application.js
import LocalStorageAdapter from './local-storage';
export default class ApplicationAdapter extends LocalStorageAdapter {
namespace = 'api';
}
```

`namespace` 属性仍将被使用，但此时它用于 Local Storage 中存储所有数据的存储键。由于 Local Storage 不存储 JavaScript 对象，我们持久化数据时需要先用 `JSON.stringify()` 将 JSON 序列化为字符串。当从 Local Storage 读取数据时，则用 `JSON.parse()` 进行解析。数据将以如下结构存储在 Local Storage 中：

```
{
"contacts": [],
"dogs": [],
"cats": []
}
```

我们将使用一个简单的对象结构，其中键对应复数形式的模型名称，并包含该类型的所有对象列表。你可能希望在开始前向 Local Storage 预置一些数据。运行以下命令即可：

```
localStorage.setItem(
'api',
JSON.stringify({
contacts: [
{ id: '1', name: 'Tom', phoneNumber: '(123) 456-7890' },
{ id: '2', name: 'Leah', phoneNumber: '(987) 456-7890' },
{ id: '3', name: 'Yehuda', phoneNumber: '(456) 456-7890' }
]
})
);
```

要查看 Local Storage 中的数据，请打开 Chrome 开发者工具的*Application*面板，点击*Local Storage*。你将看到 Local Storage 键*api*下存储的所有序列化后的 JSON 数据。

现在让我们开始实现 `findAll()` 方法。



### 实现 `findAll()`

在适配器上实现 `findAll()` 之前，我们先看看需求。首先，`findAll()` 期望返回一个 promise。在我们自定义的 `RESTAdapter` 中，我们是从 `$.get()` 返回 promise 的。由于 Local Storage 是同步的，不会返回 promise，我们可以使用 Ember 的 Promise 库 `RSVP` 来创建一个能传递数据结果的 promise。其次，promise 传递的数据需要与我们自定义 `RESTAdapter` 中使用的 Mirage 后端具有相同的结构，这样我们才能保留此方法的 API，并继续使用自定义的 `RESTSerializer`。

以下是完整实现：

```javascript
app/adapters/local-storage.js
import Adapter from '@ember-data/adapter';
import { resolve, reject } from 'rsvp';
import { pluralize } from 'ember-inflector';
export default class LocalStorageAdapter extends Adapter {
_getDataFromStorage()  {
let jsonString = localStorage.getItem(this.namespace);
if (!jsonString) {
return {};
}
return JSON.parse(jsonString);
}
findAll(store, type) {
let json = this._getDataFromStorage();
let { modelName } = type;
let storageKeyForModelName = pluralize(modelName);
let resources = json[storageKeyForModelName];
if (!resources) {
json[storageKeyForModelName] = [];
}
return resolve(json);
}
}
```

首先，定义了一个名为 `_getDataFromStorage()` 的“私有”方法，用于从 Local Storage 读取 JSON 字符串并解析它。如果没有数据，则返回一个空对象。这个方法将在我们的其他适配器方法中使用。

由于 JSON 以与 Mirage 后端相同的结构存储在 Local Storage 中，我们只需使用 `resolve()` 将其包装在一个已完成的 promise 中并返回即可。

现在我们还需要处理另一种情况，即确保 `findAll()` 在 Local Storage 为空时也能正常工作。如果 Local Storage 为空，我们会为 `json` 中正在查找的模型分配一个空数组。这样，对于我们的应用，返回的 promise 会解析为 `{ contacts: [] }`。

访问 `http://localhost:4200/contacts`，你应该会看到存储在 Local Storage 中的三个联系人。不算太难，对吧？接下来我们继续实现 `findRecord()`。

### 实现 `findRecord()`

`findRecord()` 的总体思路与 `findAll()` 类似。`findRecord()` 返回一个已完成的 promise（包含找到的资源），如果资源未找到则返回一个被拒绝的 promise：

```javascript
app/adapters/local-storage.js
import Adapter from '@ember-data/adapter';
import { resolve, reject } from 'rsvp';
import { pluralize } from 'ember-inflector';
export default class LocalStorageAdapter extends Adapter {
// ...
findRecord(store, type, id, snapshot) {
let json = this._getDataFromStorage();
let { modelName } = type;
let storageKeyForModelName = pluralize(modelName);
let resources = json[storageKeyForModelName];
if (!resources) {
return reject();
}
let foundResource = resources.find((resource) => {
return resource.id === snapshot.id;
});
if (foundResource) { let payload = {};
payload[modelName] = foundResource;
return resolve(payload);
}
return reject();
}
}
```

在上述实现中，我们使用了 `Array.prototype.find()` 根据 `id` 查找第一个资源。如果找到了资源，则返回一个已完成的 promise，其中资源的结构与我们的 API 响应 `/api/contacts/1` 的方式相同。例如，查找 `id` 为 1 的 `contact` 会得到一个包含以下内容的已完成的 promise：

```json
{
"contact": {
"id": 1,
"name": "Tom",
"phoneNumber": "(123) 456-7890"
}
}
```

如果未找到资源或该类型没有资源，则使用 `reject()` 返回一个被拒绝的 promise。

访问 `http://localhost:4200/contacts/1`，你应该会看到编辑表单中已填充了 Tom 的信息。

现在 `findRecord()` 已完成，我们来继续实现 `createRecord()`。

### 实现 `createRecord()`

`createRecord()` 不再通过 AJAX 发起 POST 请求，而是从 Local Storage 中取出所有数据，将新记录推送到对应类型的数组中，然后将数据写回 Local Storage。以前向 API 发送 POST 请求时，我们依赖数据库返回新创建记录的 `id`。但 Local Storage 没有自增 ID，所以我们需要自行模拟。记录将按 `id` 升序存储在 Local Storage 中。据此，我们可以将新记录的 `id` 设置为最后一条记录的 `id` 加 1。如果该类型没有记录，我们可以将 `id` 默认设为字符串 `"1"`。你可能会问，为什么是数字的字符串形式？因为 Ember Data 在后台会自动将 `id` 属性强制转换为字符串，如果我们保持一致，也向 Local Storage 写入字符串，实现会更简单。以下是实现代码：

```javascript
app/adapters/local-storage.js
import Adapter from '@ember-data/adapter';
import { resolve, reject } from 'rsvp';
import { pluralize } from 'ember-inflector';
export default class LocalStorageAdapter extends Adapter {
// ...
createRecord(store, type, snapshot) {
let { modelName } = type;
let data = {};
let serializer = store.serializerFor(modelName);
serializer.serializeIntoHash(data, type, snapshot);
let resource = data[modelName];
let json = this._getDataFromStorage();
let storageKeyForModelName = pluralize(modelName);
let resources = json[storageKeyForModelName];
if (resources) {
let lastResource = resources[resources.length - 1];
resource.id = String(Number(lastResource.id) + 1);
resources.push(resource);
} else {
resource.id = '1';
json[storageKeyForModelName] = [resource];
}
localStorage.setItem(this.namespace, JSON.stringify(json));
return resolve(data);
}
}
```

记录保存后，将返回一个包含持久化数据的已完成 promise，其结构与我们的 API 响应 POST 请求的方式相同。例如，创建一个名为 Mary 的联系人，会得到一个包含以下内容的已完成 promise：

```json
{
"contact": {
"id": 4,
"name": "Mary"
}
}
```

接下来，我们将处理 `updateRecord()`。



### 实现 `updateRecord()`

在我们的适配器中，`updateRecord()` 会发送一个包含更新数据的 PUT 请求，并返回与 `createRecord()` 结构相同的响应负载。在 Local Storage 适配器中，我们将使用记录的 `id` 在 Local Storage 中查找它。如果找到，将设置新属性并将数据写回 Local Storage。然后返回一个包含数据的已兑现（fulfilled）Promise。如果未找到资源，则返回一个已拒绝（rejected）的 Promise：

```javascript
app/adapters/local-storage.js
import Adapter from '@ember-data/adapter';
import { resolve, reject } from 'rsvp';
import { pluralize } from 'ember-inflector';
export default class LocalStorageAdapter extends Adapter {
// ...
updateRecord(store, type, snapshot) {
let { modelName } = type;
let data = {};
let serializer = store.serializerFor(modelName);
serializer.serializeIntoHash(data, type, snapshot);
let storageKeyForModelName = pluralize(modelName);
let json = this._getDataFromStorage();
let resources = json[storageKeyForModelName];
if (!resources) {
return reject();
}
let resourceToUpdate = json[storageKeyForModelName].find((resource) => {
return resource.id === snapshot.id;
});
if (resourceToUpdate) {
let resourceUpdates = data[modelName];
Object.assign(resourceToUpdate, resourceUpdates);
localStorage.setItem(this.namespace, JSON.stringify(json));
return resolve(data);
}
return reject();
}
}
```

与 `findRecord()` 类似，我们使用 `Array.prototype.find()` 和 `snapshot.id` 来定位要更新的资源。还记得我之前说过，如果我们将 `id` 存储为字符串，事情会简单一些吗？`snapshot.id` 总是返回一个字符串。如果我们把 `id` 存储为数字，那么就需要比较作为数字的 `resource.id` 和作为字符串的 `snapshot.id`，这就得自己做一些类型转换了。因为我们把 `id` 存储为字符串，所以可以确保 `resource.id` 和 `snapshot.id` 都是字符串。

最后，如果找到了资源，则使用 `Object.assign()` 将所有新属性合并到该资源中。

尝试编辑一个联系人。一切应该都能正常运行！

### 实现 `deleteRecord()`

与通过 AJAX 发起 DELETE 请求不同，`deleteRecord()` 需要先在 Local Storage 中找到该记录，如果找到，则将其从数组中移除。类似于对 DELETE 请求返回空响应的 API，我们可以返回一个不包含任何数据就已兑现的 Promise：

```javascript
app/adapters/local-storage.js
import Adapter from '@ember-data/adapter';
import { resolve, reject } from 'rsvp';
import { pluralize } from 'ember-inflector';
export default class LocalStorageAdapter extends Adapter {
// ...
deleteRecord(store, type, snapshot) {
let json = this._getDataFromStorage();
let { modelName } = type;
let storageKeyForModelName = pluralize(modelName);
let resources = json[storageKeyForModelName];
if (!resources) {
return reject();
}
let indexOfResourceToDelete = resources.findIndex((resource) => {
return resource.id === snapshot.id;
});
if (indexOfResourceToDelete > -1) {
resources.splice(indexOfResourceToDelete, 1);
if (resources.length === 0) {
delete json[storageKeyForModelName];
}
localStorage.setItem(this.namespace, JSON.stringify(json));
return resolve();
}
return reject();
}
}
```

在上述代码中，首先会检查 Local Storage 中是否存在给定类型的数据。如果没有，则返回一个已拒绝的 Promise。接下来，我们尝试使用 `Array.prototype.findIndex()` 查找要删除资源的索引。如果找到了资源，`indexOfResourceToDelete` 将大于 -1，这时可以结合 `Array.prototype.slice()` 方法将该资源从资源列表中移除。删除资源后，如果该类型对应的资源列表为空，则删除保存数据的键。然后将 JSON 数据写回 Local Storage，并返回一个已兑现的 Promise。最后，如果始终未找到要删除的资源，则返回一个已拒绝的 Promise，这类似于向 `/api/contacts/1` 这类端点发送 DELETE 请求时返回 404 状态码的情况。

再次启动应用程序，看看我们所有的 CRUD 操作是否仍然有效。不过这次使用的是 Local Storage！

## 总结

我们看到，仅通过对适配器进行修改，而不是修改应用程序的其他代码，就可以将后端替换为 Local Storage。让我们继续学习，下一章将介绍如何处理嵌套资源路径和关系链接。

脚注 1

