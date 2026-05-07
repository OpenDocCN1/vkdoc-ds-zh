# 11. JSON:API 的常见自定义设置

JSON:API 规范的目标是在构建 API 时引入更多约定，并减少关于响应格式应如何组织的讨论（希望不至于引发争论）。使用 JSON:API 的一大好处是 Ember Data 原生支持它！尽管如此，即使您的 API 遵循规范，也不能保证它能与 Ember Data 完美配合。Ember Data 期望在 JSON:API 之上采用更多约定，这些约定在规范的“建议”^((18))部分有说明。在本章中，我们将探讨可能需要做出的一些自定义设置，以便让 Ember Data 能与我们的 JSON:API 正常工作。

## 更改属性命名风格

在第 3 章“API 响应格式和序列化器”中，我们在“JSONAPISerializer”部分讨论过，有效载荷属性应使用连字符分隔。然而，这并非 JSON:API 规范的一部分。这个约定仅仅是一个建议。正如 JSON:API 规范的建议页面所指出的：

> **注意**  
> 成员名称应仅包含字符“a-z”（U+0061 至 U+007A）、“0-9”（U+0030 至 U+0039）以及连字符减号（U+002D HYPHEN-MINUS，“-”）作为单词之间的分隔符。

由于这只是一个建议，您可能会遇到团队选择使用蛇形命名（snake_case）或驼峰命名（camelCase）属性的情况，因为这对他们特定的服务器端技术可能更合适。

为了适应采用不同命名风格的有效载荷键，我们可以像第 4 章“常见的适配器和序列化器自定义”中学习的那样，使用 `attrs` 对象来映射属性。这种方法对于少量属性效果不错，但如果需要为每个属性都这样做，就会变得很繁琐。幸运的是，有一种更好的方法，即使用 `keyForAttribute()` 方法。此方法可用于将模型属性转换为有效载荷键。例如，假设我们有以下符合 JSON:API 规范的响应，它对两种不同的资源类型 `contacts` 和 `pets` 使用了蛇形命名属性：

```
{
"data": {
"type": "contacts",
"id": "1",
"attributes": {
"first_name": "John",
"last_name": "Doe"
},
"relationships": {
"pets": {
"data": [
{
"id": 1,
"type": "pets"
}
]
}
}
},
"included": [
{
"type": "pets",
"id": "1",
"attributes": {
"first_name": "Fiona"
}
}
]
}
```

我们的 `contact` 模型如下所示：

```
app/models/contact.js
import Model, { attr } from '@ember-data/model';
export default class ContactModel extends Model {
@attr('string') firstName;
@attr('string') lastName;
}
```

我们的 `pet` 模型如下所示：

```
app/models/pet.js
import Model, { attr } from '@ember-data/model';
export default class PetModel extends Model {
@attr('string') firstName;
}
```

我们可以在继承自 `JSONAPISerializer` 的 `application` 序列化器中重写 `keyForAttribute()` 方法，以便所有这些蛇形命名属性在模型上被设置为驼峰命名，例如：

```
app/serializers/application.js
import JSONAPISerializer from '@ember-data/serializer/json-api';
import { underscore } from '@ember/string';
export default class ApplicationSerializer extends JSONAPISerializer {
keyForAttribute(attr, method) {
return underscore(attr);
}
}
```

`keyForAttribute()` 方法将为每个模型属性的每个资源对象调用。对于前面的 JSON，`keyForAttribute()` 会针对一个 `contact` 资源对象的 `firstName` 和 `lastName` 调用两次，并针对一个 `pet` 资源对象的 `firstName` 调用一次。

如果关系属性也遵循蛇形命名惯例呢？例如，在以下 JSON:API 响应中，我们有一个关系属性 `inside_pets`：

```
{
"data": {
"type": "contacts",
"id": "1",
"attributes": {
"first_name": "John",
"last_name": "Doe"
},
"relationships": {
"inside_pets": {
"data": [
{
"id": 1,
"type": "pets"
}
]
}
}
},
"included": [
{
"type": "pets",
"id": "1",
"attributes": {
"first_name": "Fiona"
}
}
]
}
```

在我们的模型中，我们会将 `insidePets` 声明为 `hasMany` 关系：

```
app/models/contact.js
import Model, { attr, hasMany } from '@ember-data/model';
export default class ContactModel extends Model {
@attr('string') firstName;
@attr('string') lastName;
@hasMany('pet', { async: false }) insidePets;
}
```

为了使其能在 `contact` 模型上被设置为 `insidePets`，我们可以在序列化器上重写 `keyForRelationship()` 方法。



```
app/serializers/application.js
import JSONAPISerializer from '@ember-data/serializer/json-api';
import { underscore } from '@ember/string';
export default class ApplicationSerializer extends JSONAPISerializer {
// ...
keyForRelationship(key, relationship, method) {
return underscore(key);
}
}
```

`keyForRelationship()` 方法的行为与 `keyForAttribute()` 方法类似。

如果你需要为符合其他序列化格式的 API 更改属性大小写，可以像本节中所做的那样覆盖这两个方法。

## 覆盖资源对象的类型

正如我们在第 3 章——API 响应格式与序列化器中所了解的，每个资源对象都有一个必需的 `type` 属性，该属性对应于模型名称。根据所使用的服务器端技术，`type` 可能会基于后端中的类名自动设置。如果这些名称与 Ember Data 模型名称不匹配，则会发生错误。解决此问题的一种方法是覆盖我们学过的序列化器的规范化方法之一。

这种方法虽然可行，但效率不高。有一种更好的方法，即使用序列化器上的 `modelNameFromPayloadKey()` 方法。

假设我们有以下 JSON:API 响应：

```
{
"data": {
"type": "contactModel",
"id": "1",
"attributes": {
"first-name": "John",
"last-name": "Doe"
}
}
}
```

我们的模型名为 `contact`。为了将此资源正确映射到我们的 `contact` 模型，我们可以为 `contact` 创建一个序列化器，并覆盖 `modelNameFromPayloadKey()`：

```
app/serializers/contact.js
import JSONAPISerializer from '@ember-data/serializer/json-api';
export default class ContactSerializer extends JSONAPISerializer {
modelNameFromPayloadKey(key) {
return 'contact';
}
}
```

这比覆盖某个规范化方法简单得多。关于此方法需要注意的一点是，它也会为响应中的其他资源对象调用。假设现在的负载包含带有侧加载数据的关系：

```
{
"data": {
"type": "contactModel",
"id": "1",
"attributes": {
"first-name": "John",
"last-name": "Doe"
},
"relationships": {
"company": {
"data": {
"id": 1,
"type": "companyModel"
}
}
}
},
"included": [
{
"type": "companyModel",
"id": "1",
"attributes": {
"name": "Apple"
}
}
]
}
```

我们 `contact` 序列化器中的 `modelNameFromPayloadKey()` 方法也会为 `companyModel` 调用。我们可以调整实现，使其在参数 `key` 等于 `companyModel` 时进行处理：

```
app/serializers/contact.js
import JSONAPISerializer from '@ember-data/serializer/json-api';
export default class ContactSerializer extends JSONAPISerializer {
modelNameFromPayloadKey(key) {
if (key === 'companyModel') {
return 'company';
} else if (key === 'contactModel') {
return 'contact';
}
return key;
}
}
```

如果我们的 API 遵循一种约定，即 `type` 始终是模型名称加上 `Model` 后缀，我们可以通过在 `application` 序列化器中覆盖 `modelNameFromPayloadKey()` 来使其更通用：

```
app/serializers/application.js
import JSONAPISerializer from '@ember-data/serializer/json-api';
export default class ApplicationSerializer extends JSONAPISerializer {
modelNameFromPayloadKey(key) {
return key.replace('Model', '');
}
}
```

如果我们将类型规范化了，那么在保存记录时，很可能需要将其改回原始值。我们可以使用 `payloadKeyFromModelName(modelName)` 方法来实现这一点：

```
app/serializers/application.js
import JSONAPISerializer from '@ember-data/serializer/json-api';
export default class ApplicationSerializer extends JSONAPISerializer {
payloadKeyFromModelName(modelName) {
return '${modelName}Model';
}
}
```

如果我们保存一条 `contact` 记录，`type` 将被设置为 “contactModel”，如下列请求负载所示：

```
{
"data": {
"type": "contactModel",
"attributes": {
"first-name": "John",
"last-name": "Doe"
}
}
}
```

## 覆盖 HTTP 方法

JSON:API 规范规定，在更新资源时应使用 PATCH 方法。如果我们使用的 API 仍然使用 PUT 怎么办？在撰写本文时，还没有很好的方式来覆盖 HTTP 方法。

如果我们查看 `JSONAPIAdapter` 中 `updatedRecord` 的实现，会发现它与 `RESTAdapter` 中 `updateRecord` 的实现几乎相同。唯一的区别是 “PUT” 被改为了 “PATCH”：

*   `RESTAdapter` 中的 `updateRecord()`^(¹⁹)

*   `JSONAPIAdapter` 中的 `updateRecord()`^(²⁰)

如果你的 API 基本符合 JSON:API 规范，但仍期望使用 PUT 而不是 PATCH，我们可以覆盖私有的 `ajax()` 方法：

```
app/adapters/application.js
import JSONAPIAdapter from '@ember-data/adapter/json-api';
export default class ApplicationAdapter extends JSONAPIAdapter {
// 注意！此方法是私有的！
ajax(url, type, options) {
if (type === 'PATCH') {
type = 'PUT';
}
return super.ajax(url, type, options);
}
}
```

请注意，`ajax()` 方法是**私有的**，因此将来更新 Ember Data 时可能会遇到问题。

## 总结

在本章中，我们讨论了一些在使用 JSON:API 时可能需要进行的自定义设置，尽管 Ember Data 支持 JSON:API。根据我的经验，与使用完全不遵循 JSON:API 的 API 相比，自定义设置的数量要少得多。

脚注 1  2  3

