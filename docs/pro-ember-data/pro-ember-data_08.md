# 7. 嵌套资源 URL 路径与关系链接

许多 API 使用嵌套资源 URL 路径来表达关系。嵌套资源 URL 路径的一个例子是 `/contacts/1/pets`，从这个端点返回的 `pet` 资源集合属于 `id` 为 1 的 `contact` 资源。这些端点通常被称为关系链接，Ember Data 知道如何使用它们来加载关系。我们先看看 Ember Data 如何在响应负载中使用关系链接的默认行为。然后，我们将探讨当 API 中没有提供关系链接时，如何告知 Ember Data 这些链接。

本章的代码可在本书源代码的 `chapter-7` 文件夹中找到。

### 关系链接的工作原理

Ember Data 为 `RESTAdapter` 和 `JSONAPIAdapter` 提供了开箱即用的关系链接支持。在本节中，我们将具体探讨与 `RESTAdapter` 和 `RESTSerializer` 相关的关系链接，但这些概念同样适用于使用 JSON:API 的情况。

`RESTAdapter` 支持在单个资源上使用名为 `links` 的属性，该属性包含指向相关资源的 URL。例如，假设我们有一个 `contact` 模型，它包含异步的 `belongsTo` 和 `hasMany` 关系：

```javascript
app/models/contact.js
import Model, { attr, hasMany, belongsTo } from '@ember-data/model';
export default class ContactModel extends Model {
@attr('string') name;
@hasMany('pet', { async: true }) pets;
@belongsTo('company', { async: true }) company;
}
```

如果我们向 `/api/contacts` 发起请求，并且响应负载符合 `RESTSerializer` 或 `JSONSerializer` 格式，那么响应中的每个 `contact` 资源都可以包含一个 `links` 属性：

```json
{
"contacts": [
{
"id": 1,
"name": "David",
"links": {
"company": "/api/contacts/1/company",
"pets": "/api/contacts/1/pets"
}
}
]
}
```

类似地，`links` 属性也可以出现在从 `/api/contacts/1` 等端点返回的单个资源负载中：

```json
{
"contact": {
"id": 1,
"name": "David",
"links": {
"company": "/api/contacts/1/company",
"pets": "/api/contacts/1/pets"
}
}
}
```

如果我们访问 `contact.pets` 或 `contact.company`，Ember Data 将使用 `links` 中定义的 URL 触发一次数据获取。正如 API 文档中所述：

> 注意
>
> 你的链接值的格式将通过 `urlPrefix` 方法影响最终的请求 URL：以 `//`、`http://`、`https://` 开头的链接将按原样使用，不做任何处理。以单个 `/` 开头的链接，会在其前面加上当前适配器的 `host` 值。没有以 `/` 开头的链接，则会通过当前适配器的 `buildURL` 方法，在其前面加上 `parentURL`。

很多 API 虽然提供了遵循嵌套资源 URL 路径约定的端点，但并不会在资源的 `links` 属性中返回这些链接。接下来，我们将了解如何将这类端点告知 Ember Data。



## 当 API 不返回关系链接时

在前几章中，我们学习了序列化器中不同的规范化方法，这些方法允许我们在 Ember Data 使用数据之前拦截数据负载。为了处理缺失的`links`属性，我们可以覆盖多种规范化方法中的一种。

例如，假设调用`store.findRecord('contact', 1)`向`/api/contacts/1`发起请求，并返回以下数据负载：

```
{
  "contact": {
    "id": 1,
    "name": "David"
  }
}
```

我们想要为`pet`和`company`这两个关联关系向`contact`资源添加`links`。虽然我们可以覆盖几种不同的规范化方法，但最简单的做法是覆盖`normalize()`方法，因为它作用于单个资源：

```javascript
// app/serializers/contact.js
import RESTSerializer from '@ember-data/serializer/rest';
export default class ContactSerializer extends RESTSerializer {
  normalize(modelClass, resourceHash) {
    resourceHash.links = {
      pets: '/api/contacts/${resourceHash.id}/pets',
      company: '/api/contacts/${resourceHash.id}/company'
    };
    return super.normalize(...arguments);
  }
}
```

就这样！很简单，对吧？

如前所述，JSON:API 也使用`links`，但响应格式略有不同。如果你在使用 JSON:API 时需要手动添加`links`，其过程与本章示例类似。更多详情请访问 JSON:API 规范中关于链接的部分^(¹³)。

## 总结

现在我们已经了解如何处理关系链接，接下来我们将学习处理嵌套数据的不同方法。

脚注 1

