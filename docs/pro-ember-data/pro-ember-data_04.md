# 3. API 响应格式与序列化器

Ember Data 中的序列化器用于在客户端和服务器之间传输数据时对其进行格式化。当序列化器格式化响应数据时，此过程称为*规范化*。相反，当序列化器格式化发送到服务器的数据时，此过程称为*序列化*。

Ember Data 内置了三个序列化器：

1. `JSONSerializer`
2. `RESTSerializer`
3. `JSONAPISerializer`

每个序列化器都针对特定的 API 载荷结构。为了让自定义 API 能够与 Ember Data 协作，了解每个序列化器所期望的数据格式是有帮助的，这样您就可以选择与您的 API 最匹配的一个。选择一个与您的 API 最相似的序列化器，很可能会减少您需要进行的自定义工作量。

## `JSONSerializer`

`JSONSerializer`（不要与 `JSONAPISerializer` 混淆）是一种可用于具有非常简单的载荷结构的 API 的序列化器。例如，假设我们的应用程序向 `/api/contacts/1` 发起请求。预期的 JSON 响应是：

```
{
"id": 1,
"fullName": "David Tang"
}
```

这个资源对象很简单，仅包含资源的 `id` 和采用驼峰命名法的属性，这些属性映射到模型属性。其中不包含任何额外的元信息。

在创建资源时，我们会发送：

```
{
"fullName": "David Tang"
}
```

在更新资源时，我们会发送：

```
{
"id": 1,
"fullName": "David Tang"
}
```

那么像返回资源集合的 `/api/contacts` 这样的端点呢？正如您可能猜到的，预期的响应包含一个 `contact` 资源数组：

```
[
{ "id": 1, "fullName": "David Tang" },
{ "id": 2, "fullName": "Tom Dale" }
]
```

这里我们有一个包含 `hasMany` 和 `belongsTo` 关系的模型：

```
app/models/contact.js
import Model, { attr, hasMany, belongsTo } from '@ember-data/model';
export default class ContactModel extends Model {
@attr('string') fullName;
@hasMany('pet', { async: true }) pets;
@belongsTo('company', { async: true }) company;
}
```

为了正确建立这些关系，`contact` 资源应包含以 ID 形式呈现的相关数据：

```
{
"id": 1,
"fullName": "David Tang",
"pets": [1, 2, 3],
"company": 7
}
```

`pets` 和 `company` 属性包含每个宠物和公司的 ID。因为我们已将关系声明为异步，Ember Data 会在我们需要时（例如在模板中通过 `contact` 记录访问它们时）异步加载这些相关记录。



## `RESTSerializer`

`RESTSerializer` 继承自 `JSONSerializer`，期望的格式与之类似，但结构更丰富。它与 `JSONSerializer` 的区别在于引入了一个与模型名称匹配的额外根键。例如，如果向 `/api/contacts/1` 发起请求，预期的 JSON 响应是：

```
{
"contact": {
"id": 1,
"fullName": "David Tang",
"pets": [1, 2, 3],
"company":  7
}
}
```

这里的根键是 `contact`，与模型名称匹配。类似地，如果向 `/api/contacts` 发起请求，预期的 JSON 响应是：

```
{
"contact": [
{
"id": 1,
"fullName": "David Tang",
"pets": [1, 2, 3],
"company": 7
},
{
"id": 2,
"fullName": "Tom Dale",
"pets": [4],
"company": 7
}
]
}
```

值得注意的是，根键也可以是复数形式（即 `contacts`）。

在之前使用 `JSONSerializer` 格式的 `contact` 资源示例中，`pets` 和 `company` 需要在访问相关数据之前，从服务器异步加载或预先加载到存储中。使用 `RESTSerializer` 格式的好处之一是它支持数据侧加载，允许我们在不重复数据的情况下，将相关记录包含在请求的主数据响应中。例如，当向 `/api/contacts/1` 发起请求时，一个包含侧加载数据的响应如下所示：

```
{
"contact": {
"id": 1,
"fullName": "David Tang",
"pets": [1, 3],
"company": 7
},
"pet": [
{ "id": 1, "name": "Fiona" },
{ "id": 3, "name": "Biscuit" }
],
"company": [{ "id": 7, "name": "Company A" }]
}
```

该响应包含了与侧加载数据对应的键 `pet` 和 `company`。这在 `JSONSerializer` 格式中是无法实现的。使用侧加载数据还可以让我们通过单个请求使模型关系同步：

```
app/models/contact.js
import Model, { attr, hasMany, belongsTo } from '@ember-data/model';
export default class ContactModel extends Model {
@attr('string') fullName;
@hasMany('pet', { async: false }) pets; // 同步
@belongsTo('company', { async: false }) company; // 同步
}
```

如果我们希望使用 `JSONSerializer` 实现关系的同步，就需要确保在访问相关记录之前，所有公司和宠物都已加载到存储中。

## `JSONAPISerializer`

与 `RESTSerializer` 类似，`JSONAPISerializer` 也继承自 `JSONSerializer`，它期望数据遵循 JSON:API 规范。由于 Ember Data 内部使用 JSON:API 来表示数据，因此即使你的 API 没有遵循 JSON:API，了解 JSON:API 的基础知识也是有用的。

对于返回单个资源的端点，例如 `/api/contacts/1`，一个符合 JSON:API 规范的响应如下所示：

```
{
"data": {
"type": "contacts",
"id": "1",
"attributes": {
"first-name": "David",
"last-name": "Tang"
}
}
}
```

JSON:API 资源对象必须包含顶层成员 `id` 和 `type`。`type` 属性包含模型名称，可以是单数或复数形式。你会注意到在 JSON:API 规范的所有示例中，`type` 都是复数形式。资源的数据包含在 `attributes` 属性中。属性使用连字符命名，并将映射到我们的模型上的驼峰式属性。例如，`first-name` 和 `last-name` 会像这样映射到模型上的 `firstName` 和 `lastName`：

```
app/models/contact.js
import Model, { attr } from '@ember-data/model';
export default class ContactModel extends Model {
@attr('string') firstName;
@attr('string') lastName;
}
```

对于返回资源集合的端点，例如 `/api/contacts`，一个符合 JSON:API 规范的响应如下：

```
{
"data": [
{
"type": "contacts",
"id": "1",
"attributes": {
"first-name": "David",
"last-name": "Tang"
}
},
{
"type": "contacts",
"id": "2",
"attributes": {
"first-name": "Tom",
"last-name": "Dale"
}
}
]
}
```

和之前一样，有一个 `data` 键，但这次它包含的是一个数组而不是对象。数组中的每个元素都是一个资源对象，其结构与获取单个资源对象时相同，即一个包含 `type`、`id` 和 `attributes` 键的对象。

### 关系

关系通过 `relationships` 键来处理：

```
{
"data": {
"type": "contacts",
"id": "1",
"attributes": {
"first-name": "David",
"last-name": "Tang"
},
"relationships": {
"pets": {
"data": [
{ "type": "pets", "id": 1 },
{ "type": "pets", "id": 2 }
]
},
"company": {
"data": {
"type": "companies",
"id": 1
}
}
}
}
}
```

在前面的 JSON 中，`relationships` 对象的结构是为一个 `hasMany` 的 `pet` 关系和一个 `belongsTo` 的 `company` 关系定义的：

```
app/models/contact.js
import Model, { attr, hasMany, belongsTo } from '@ember-data/model';
export default class ContactModel extends Model {
@attr('string') firstName;
@attr('string') lastName;
@hasMany('pet') pets; // 异步
@belongsTo('company') company; // 异步
}
```

注意 `relationships` 下直接键的形式。这些键的单数或复数形式直接对应我们的模型关系，即 `pets` 和 `company`。不要将其与 `type` 的单复数形式混淆，`type` 可以是单数也可以是复数。

还要注意，`data/relationships/pets/data` 是一个数组，而 `data/relationships/company/data` 是一个对象。`data/relationships/pets/data` 数组中的每个元素都是一个 `pet` 资源对象，但没有属性。`data/relationships/company/data` 也是如此。这些资源对象只包含 `id` 和 `type`，并指明存在关系。`pet` 和 `company` 资源需要从服务器加载，或者通过侧加载的方式加载。

### 侧加载

相关资源可以通过 `included` 键进行侧加载：

```
{
"data": {
"type": "contacts",
"id": "1",
"attributes": {
"first-name":  "David",
"last-name": "Tang"
},
"relationships": {
"pets": {
"data": [
{ "type": "pets", "id": 1 },
{ "type": "pets", "id": 2 }
]
},
"company": {
"data": {
"type": "companies",
"id": 1
}
}
}
},
"included": [
{
"type": "pets",
"id": "1",
"attributes": {
"name": "Fiona"
}
},
{
"type": "pets",
"id": "2",
"attributes": {
"name": "Biscuit"
}
},
{
"type": "companies",
"id": "1",
"attributes": {
"name": "Apple"
}
}
]
}
```

`included` 键是一个用于侧加载资源的数组。`id` 为 1 的 `user` 资源在 `relationships` 下声明了两个 `pet` 资源和一个 `company` 资源。`included` 数组中的数据使用相同的 JSON:API 资源对象结构包含相关联的资源，其键为 `type`、`id` 和 `attributes`。现在我们的模型关系可以变为同步的：

```
app/models/contact.js
import Model, { attr, hasMany, belongsTo } from '@ember-data/model';
export default class ContactModel extends Model {
@attr('string') firstName;
@attr('string') lastName;
@hasMany('pet', { async: false }) pets; // 同步
@belongsTo('company', { async: false }) company; // 同步
}
```

### 规范化的 JSON:API

我之前提到，Ember Data 的内部格式使用规范化的 JSON:API，这就是为什么即使你的 API 没有使用 JSON:API，了解它也是有用的。规范化的 JSON:API 是对 JSON:API 的少量调整。当我们规范化载荷时，我们正在将载荷的结构更改为这种规范化的 JSON:API 格式，以便 Ember Data 可以使用它。这些少量调整如下：

- `type` 应始终为单数且采用连字符形式。
- 成员（属性）应为驼峰式。



## 基础序列化器 `Serializer`

值得一提的是，还存在另一个序列化器 `Serializer`。它是一个抽象类，`JSONSerializer` 正是继承自它。如果你的 API 与常规差异很大，以至于无法使用内置序列化器，那么可以考虑使用这个。查看 `Serializer` 的源码你会发现，它的内容并不多。它只是一个带有少量默认配置的简单 `EmberObject`。在下一章中，我们将从头编写一个继承自 `Serializer` 的自定义序列化器，以便更深入地理解序列化器的工作原理。

## 使用序列化器

我们已经了解了每种序列化器所期望的不同数据格式。要使用 Ember CLI 创建序列化器，可以执行以下命令：

```
ember generate serializer application
```

该命令会生成一个应用级别的序列化器，它将用于所有模型。如果没有生成 `application` 序列化器，Ember Data 将会使用 `JSONAPISerializer`。或者，我们也可以为特定模型创建序列化器：

```
ember generate serializer
```

在接下来的几章中，我们将根据具体的定制需求，同时使用模型专属和 `application` 序列化器。如果你认为某项定制适用于所有模型，可以将其设计得足够通用，并放入 `application` 序列化器中。如果定制仅针对某个特定模型，则将其放入该模型专属的序列化器中。

## 总结

Ember Data 开箱即用地提供了三种序列化器。了解这些序列化器所期望的 API 数据格式，可以帮助你确定哪一个与你的 API 最匹配，从而继承该序列化器，并最终尽量减少对适配器和序列化器的自定义修改。现在我们已知晓 Ember Data 内置序列化器所期望的有效载荷结构，下一章我们将探讨一些常见的自定义方法。

脚注 1

