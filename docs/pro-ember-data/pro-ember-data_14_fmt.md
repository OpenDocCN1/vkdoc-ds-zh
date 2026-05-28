# 13. 多态关联

在本章中，我们将探讨多态性及其与 `Ember Data` 的关系。如果查看多态性在编程中的定义，我们会看到它被描述为“对象能够呈现多种形态的能力”。例如，一个产品可以表现为一本书或一门课程。在 Ember 应用中，我们会有一个 `book` 模型和一个 `course` 模型，它们都继承自基础的 `product` 模型。如果将多态性的定义延伸到 `Ember Data` 模型的关联中，这意味着我们的关联可以包含不同类型但共享相同接口的对象，比如都继承自同一个 `product` 模型。让我们看看本章将要介绍的两个示例。

## 示例 1 - 已购产品

假设我们有一个网站，用户可以在上面购买不同的产品，比如课程和书籍。我们会创建一个 `course` 模型和一个 `book` 模型，它们都继承自基础的 `product` 模型。同时还会有一个 `user` 模型来表示当前用户。通常在这类网站上，会有一个页面列出已认证用户购买的所有产品。我们可以将用户与他们购买的各种产品之间的关系表达为一个 `hasMany` 关联。

回到多态性的定义：用户购买的产品可以呈现多种形态，例如课程或书籍。这是一个多态 `hasMany` 关联的例子，因为该关联可以包含多种类型、且都继承自基础 `product` 类的记录。

## 示例 2 - 带评论的内容

假设我们的应用还提供博客文章和视频形式的内容。我们希望用户能够对每种类型的内容进行评论。因此，我们会有一个 `post` 模型和一个 `video` 模型，它们都继承自基础的 `content` 模型。同时还会有一个 `comment` 模型。帖子和视频可以拥有多条评论。然而，一条评论只能属于单一内容，在我们的应用中，该内容可以是帖子或视频。我们可以将评论与内容之间的关系表达为一个多态 `belongsTo` 关联，因为与评论关联的内容可以是不同类型，只要该记录继承自我们的基础 `content` 类即可。

现在我们已经有了几个概念性的示例，接下来看看如何在 `Ember Data` 中实现这种数据模型。

## 搭建环境

我已经创建了一个简单的应用来实现前文示例。该应用的代码位于本书源代码的 `chapter-13-restserializer` 文件夹中。我们将使用这个应用来测试模型定义和多态关系。

与之前的章节类似，API 通过 Ember CLI Mirage 进行模拟。^((21)) 在本章中，我们的模拟 API 将遵循 `RESTSerializer` 规范，但所涉及的模式和技术同样适用于遵循 `JSONSerializer` 和 `JSONAPISerializer` 规范的 API，仅存在一些细微差异。如果你希望查看使用 `JSONSerializer` 时的应用代码，请参阅本书源代码的 `chapter-13-jsonserializer` 文件夹。如果你希望查看使用 `JSONAPISerializer` 时的应用代码，请参阅本书源代码的 `chapter-13-jsonapiserializer` 文件夹。

## 多态 `hasMany` 关系

首先，我们定义示例 1 中的模型，其中包括一个 `product` 模型、一个 `course` 模型和一个 `book` 模型：

```javascript
app/models/product.js
import Model, { attr } from '@ember-data/model';
export default class ProductModel extends Model {
  @attr('string') title;
}

app/models/course.js
import ProductModel from './product';
import { attr } from '@ember-data/model';
export default class CourseModel extends ProductModel {
  @attr('string') length;
}

app/models/book.js
import ProductModel from './product';
import { attr } from '@ember-data/model';
export default class BookModel extends ProductModel {
  @attr('number') pages;
}
```

如前所述，`course` 和 `book` 模型将继承自基础 `product` 模型，该基础模型包含所有产品共有的功能，例如 `title` 属性。

前面我们讨论过，一个用户可以拥有多个已购买产品。我们可以将此关系实现为 `hasMany` 关系：

```javascript
app/models/user.js
import Model, { attr, hasMany } from '@ember-data/model';
export default class UserModel extends Model {
  @hasMany('product', {
    polymorphic: true,
    async: false
  })
  purchasedProducts;
}
```

`purchasedProducts` 关系是一个标准的 `hasMany` 关系，但带有一个 `polymorphic` 选项。这将允许我们存储不同类型的记录，只要它们继承自 `product` 模型。我在示例应用中将此关系设置为同步关系，但该关系也可以是异步的。

接下来，让我们看看如果我们获取一个已购买产品的用户，预期的响应负载是什么。同样，我们的模拟 API 将遵循 `RESTSerializer` 规范。

在我们的示例应用中，有以下端点用于返回当前用户：

```javascript
mirage/config.js
this.get('/users/:id', function () {
  return {
    users: {
      id: '1',
      name: 'David',
      purchasedProducts: [
        { id: '5', type: 'course' },
        { id: '10', type: 'book' }
      ]
    }
  };
});
```

正如我们在第 3 章“API 响应格式与序列化器”中所学，当使用 `RESTSerializer` 或 `JSONSerializer` 时，`hasMany` 关系在 API 负载中应表示为 ID 数组。为了使用多态关系，`purchasedProducts` 必须是一个 JSON:API 资源标识符对象数组，即包含 `id` 和 `type` 属性的对象。`type` 属性对应 `Ember Data` 模型的单数或复数形式名称，在本例中为 `course` 或 `book`。

通过这个 API 响应，我们的 `purchasedProducts` `hasMany` 关系将是多态的。也就是说，`purchasedProducts` 可以包含 `course` 记录和 `book` 记录。

正如我们在第 1 章“Ember Data 概述”中所学，如果访问 `purchasedProducts` 关系但相关记录未加载到存储中，则会抛出错误，因为该关系已被声明为同步。在示例应用中，我们有以下端点用于将相关的 `course` 和 `book` 记录加载到存储中：

```javascript
mirage/config.js
this.get('/products', function () {
  return {
    courses: [
      {
        id: '5',
        title: 'Introduction to Ember Octane',
        length: '2 hours'
      }
    ],
    books: [
      {
        id: '10',
        title: 'Ember Data in the Wild',
        pages: 100
      }
    ]
  };
});
```

在使用 `RESTSerializer` 时，我们也可以将这些资源放在一个 `products` 键下返回：

```javascript
mirage/config.js
this.get('/products', function () {
  return {
    products: [
      {
        id: '5',
        type: 'course',
        title: 'Introduction to Ember Octane',
        length: '2 hours'
      },
      {
        id: '10',
        type: 'book',
        title: 'Ember Data in the Wild',
        pages: 100
      }
    ]
  };
});
```

这里唯一的要求是每个 `product` 资源中需要包含一个 `type` 属性，该属性对应“产品”子类的模型名称。

## 多态 `belongsTo` 关系

现在让我们看看如何在示例 2 中实现多态 `belongsTo` 关系。我们将从定义 `content`、`post` 和 `video` 模型开始：

# 数据模型定义

```
app/models/content.js
import Model, { attr, hasMany } from '@ember-data/model';
export default class ContentModel extends Model {
@attr('string') title;
@hasMany('comment', { async: false }) comments;
}

app/models/post.js
export default class PostModel extends ContentModel {
@attr('number') wordCount;
}

app/models/video.js
import { attr } from '@ember-data/model';
import ContentModel from './content';
export default class VideoModel extends ContentModel {
@attr('number') length;
}
```

`post` 和 `video` 模型将继承自基础 `content` 模型，该基础模型包含 `comments` 关系。

一条评论将属于单一内容，该内容可以是帖子或视频。我们可以将此关系实现为多态 `belongsTo` 关系，如下所示：

```
app/models/comment.js
import Model, { attr, belongsTo } from '@ember-data/model';
export default class CommentModel extends Model {
@attr('string') body;
@belongsTo('content', {
polymorphic: true,
async: false
})
content;
}
```

`content` 关系是一个标准的 `belongsTo` 关系，但带有一个 `polymorphic` 选项。这将允许我们将不同类型的 `content` 记录分配给 `content` 关系，只要这些记录继承自 `content` 模型。

接下来，让我们看看返回用户所有评论的端点预期响应负载：

```
mirage/config.js
this.get('/comments', function () {
return {
comments: [
{
id: '1',
body: 'Looking forward to using all the new features!',
content: {
id: '1',
type: 'post'
}
},
{
id: '2',
body: 'Great video! Please make more!',
content: {
id: '2',
type: 'video'
}
}
],
posts: [
{
id: '1',
title: 'Ember Octane Released',
wordCount: 300,
comments: ['1']
}
],
videos: [
{
id: '2',
title: 'Introduction to Ember.js',
length: 300,
comments: ['2']
}
]
};
});
```

请注意，`comments` 下的每个资源都在 `content` 属性中包含一个 JSON:API 资源标识符对象。与多态 `hasMany` 关系类似，`id` 和 `type` 将用于与正确的 `content` 子类记录建立 `belongsTo` 关系。

## 自定义多态关系序列化

在最后两节中，我们学习了如何定义模型和预期的响应格式，以便 `hasMany` 和 `belongsTo` 多态关系能够正确关联。现在，我们将探讨当 API 不遵循多态关系的默认约定时，如何自定义序列化器。

在示例应用中，访问`http://localhost:4200/recent-comments`并向其中一个项目添加评论。如果打开浏览器控制台，您将看到类似以下结构的请求负载：

```
{
  "comment": {
    "body": "我的新评论",
    "content": "1",
    "contentType": "post"
  }
}
```

这个序列化的负载与序列化任何 `belongsTo` 关系时的预期结果类似，只是多了一个新键 `contentType`。当序列化多态 `belongsTo` 关系时，`RESTSerializer` 的默认行为是创建一个键，其中 `Type` 被追加到多态关系的名称后面。在这个例子中，我们的多态关系名为 `content`，因此新键就变成了 `contentType`。

假设我们希望多态键为 `type`，而不是遵循 `*Type` 的模式。我们可以在序列化器中覆盖 `keyForPolymorphicType()` 方法：

```
app/serializers/application.js
export default class ApplicationSerializer extends RESTSerializer {
  keyForPolymorphicType(key, typeClass, method) {
    return 'type';
  }
}
```

现在，请求负载将如下所示：

```
{
  "comment": {
    "body": "我的新评论",
    "content": "1",
    "type": "post"
  }
}
```

也许我们希望请求负载包含一个 JSON:API 资源标识符对象，以使请求和响应负载之间保持对称，例如：

```
{
  "comment": {
    "body": "我的新评论",
    "content": {
      "id": "1",
      "type": "post"
    }
  }
}
```

我们可以通过覆盖 `serializePolymorphicType()` 方法来实现这一点：

```
app/serializers/application.js
export default class ApplicationSerializer extends RESTSerializer {
  serializePolymorphicType(snapshot, json, relationship) {
    super.serializePolymorphicType(snapshot, json, relationship);
    let { name } = relationship.meta;
    json[name] = {
      id: json[name],
      type: json[`${name}Type`]
    };
    delete json[`${name}Type`];
  }
}
```

在上述代码中，我们调用了 `super.serializePolymorphicType()` 来执行默认的序列化行为。这将导致 `json` 变量包含一个结构类似于以下的对象：

```
{
  comment: {
    body: '我的新评论',
    content: '1',
    contentType: 'post'
  }
}
```

然后，我们将 `comment.content` 属性覆盖为 `{ id: '1', type: 'post' }`，并删除 `comment.contentType` 属性。

正如我们在前几章中学到的，我们也可以将此逻辑添加到序列化器的 `serialize()` 方法中，但使用 `keyForPolymorphicType()` 和 `serializePolymorphicType()` 方法很可能会减少多态关系序列化所需的代码量。

## 自定义多态关系反规范化

想象一下，API 以 `type:id` 的形式序列化每个 `comment` 资源中的多态 `content` 关系。如果我们的示例应用是这种情况，`GET /comments` 将返回以下 JSON：

```
{
  "comments": [
    {
      "id": "1",
      "body": "期待使用所有新功能！",
      "content": "post:1"
    },
    {
      "id": "2",
      "body": "很棒的视频！请多制作一些！",
      "content": "video:2"
    }
  ],
  "posts": [
    {
      "id": "1",
      "title": "Ember Octane 发布",
      "comments": ["1"]
    }
  ],
  "videos": [
    {
      "id": "2",
      "title": "Ember.js 简介",
      "comments": ["2"]
    }
  ]
}
```

# 多态关系

我知道这个例子有点牵强，但谁知道在实际中会遇到什么样的 API！之前，`content`包含一个 JSON:API 资源标识符对象，例如`{ "id": "1", "type": "post" }`。我们如何规范化我们的多态`content`关系？我们可以覆盖我们在前几章中介绍的众多规范化方法之一，或者我们可以覆盖`extractPolymorphicRelationship()`方法。这个方法会为使用`polymorphic: true`选项声明的关系调用，并返回一个 JSON:API 资源标识符对象。

假设我们的多态关系既遵循这种自定义约定，也遵循标准约定。我们可以在我们的`application`序列化器中覆盖`extractPolymorphicRelationship()`来处理这两种情况：

```javascript
app/serializers/application.js
export default class ApplicationSerializer extends RESTSerializer {
  extractPolymorphicRelationship(
    relationshipType,
    relationshipHash,
    relationshipOptions
  ) {
    if (typeof relationshipHash === 'string') {
      let [type, id] = relationshipHash.split(':');
      return { id, type };
    }
    return super.extractPolymorphicRelationship(...arguments);
  }
}
```

在上面的实现中，对于前面的响应，`relationshipHash`将包含`post:1`和`video:2`。我们可以按冒号分割，创建一个符合 JSON:API 资源标识符对象结构的新对象，并在使用自定义多态关系约定时返回该对象。否则，我们将通过`super.extractPolymorphicRelationship(...arguments)`让默认的多态提取行为运行。

## 总结

多态是一个强大的概念，它允许我们拥有通过继承相互关联的模型，而 Ember Data 的关系可以支持这一点。最终，这允许一个`belongsTo`关系通过单一关系包含不同类型的记录，只要这些记录继承自一个共同的基类。同样，这也允许一个`hasMany`关系包含多种类型的记录，只要这些记录继承自一个共同的基类。

在本章中，我们研究了如何设置模型和 API 以支持多态关系。然后，我们探讨了如果您的 API 不遵循 Ember Data 期望的默认约定，如何自定义多态关系的序列化和反规范化。尽管我们使用`RESTSerializer`研究了多态关系的实现，但如果您使用其他内置序列化器之一，模型定义是相同的。此外，我们覆盖的序列化器方法也相同，除了`JSONSerializer`和`JSONAPISerializer`不支持`keyForPolymorphicType()`方法。`JSONSerializer`和`JSONAPISerializer`的默认 API 约定也有一些细微差别。尽管存在这些细微差异，但本章涵盖的模式和技术应该能使您处理多态关系，无论您的应用程序使用哪个序列化器。

嗯，到这里就结束了。感谢阅读我的书！希望这本书能帮助到您，并让您处于一个能够很好地处理 Ember Data 和任何自定义 API 的位置。如果您有任何问题，请通过 Twitter @iamdtang 或发送电子邮件至 david@thejsguy.com 联系我。我很乐意听到您使用 Ember Data 的经验。您也可以在我的博客 [`https://davidtang.io`](https://davidtang.io) 上了解我的 Ember 开发之旅的最新动态。

脚注¹  

## 索引

### A

`ActiveModelAdapter`  

`Adapters`  

`ActiveModelAdapter` 后台重载 主机/命名空间属性  

`JSONAPIAdapter` 参见 `JSONAPIAdapter` 方法  

`RESTAdapter`  

架构分析 适配器设计模式  

适配器 API 异步/同步  

`attr()` 装饰器  

### B

`belongsTo()` 方法  

内置转换  

组件构造函数  

### C

`findAll()` 方法  

模型 关系 路由与控制器  

序列化 server.js 文件  

Store（数据仓库）  

`belongsTo` 关系  

### D

`createRecord()` 方法  

自定义 适配器 应用适配器  

外键/关系 `keyForRelationship()`  

标准化 载荷键  

`primaryKey` 属性  

RESTful URL 路径  

`store.query()`  

`urlForQuery()` 方法  

数据处理错误  

`AdapterError`  

`ConflictError`  

错误响应 参见 错误响应  

`ForbiddenError`  

`handleResponse()`  

HTTP 状态码  

`InvalidError` 类  

无效状态码  

`NotFoundError`  

`ServerError`  

`UnauthorizedError`  

验证错误  

`deleteRecord()` 方法  

### E

嵌入式记录  

分类模型  

`EmbeddedRecordsMixin`  

`hasMany` 关系  

JSON 数据  

序列化器  

空适配器  

错误响应  

错误消息  

`extractErrors()` 方法  

`handleResponse()`  

载荷  

### F, G

`findAll()` 方法  

`findRecord()` 方法  

### H

`hasMany` 关系  

实现  

`JSONSerializer`  

`purchasedProducts`  

`RESTSerializer`  

### I

`isInvalid()` 方法  

### J, K

`JSONAPIAdapter`  

接受头  

API 响应格式  

`coalesceFindRequests` 选项  

差异  

带连字符的 URL  

加载关系  

MIME/内容类型  

`PATCH` 方法  

`pathForType()` 方法  

请求模板  

`JSONAPISerializer`  

API 资源  

驼峰式属性  

`hasMany`/`belongsTo`  

标准化 JSON:API  

关系  

同步加载旁路资源  

JSON:API 规范  

属性大小写  

联系人模型  

`hasMany` 关系  

`keyForAttribute()` 方法  

`keyForRelationship()` 方法  

载荷键  

宠物模型  

资源覆盖 HTTP 方法  

资源对象  

`companyModel`  

`contactModel`  

`modelNameFromPayloadKey()` 方法  

`payloadKeyFromModelName()` 方法  

旁路加载的数据  

### L, M

本地存储适配器  

`createRecord()` 方法  

`deleteRecord()` 方法  

空适配器  

`findAll()` 方法  

`findRecord()` 方法  

`updateRecord()` 方法  

### N, O

嵌套数据  

联系人资源  

嵌入式记录历史  

数据/属性  

`model.set()`  

资源转换  

嵌套资源 URL 路径  

标准化  

JSON:API 格式  

含义  

`normalize()` 方法  

`normalizeFindAllResponse()` 方法  

`normalizeResponse()` 方法  

多态关系  

`RESTSerializer`  

序列化器方法  

单个资源对象  

`store.findAll()`  

`normalizeResponse()` 方法  

### P, Q

`pathForType()` 方法  

多态关系  

`belongsTo` 关系  

内容/评论  

`hasMany` 关系  

实现  

`JSONSerializer`  

模型定义  

`purchasedProducts`  

`RESTSerializer`  

`hasMany` 关系  

`keyForPolymorphicType()` 方法  

标准化  

购买的产品  

序列化


`serializePolymorphicType()` 方法  
设置  

### R

记录适配器方法  
联系人请求  
`createRecord()` 方法  
`deleteRecord()` 方法  
`destroyRecord()` 方法  
错误  
`POST` 请求  
属性/方法  
`serialize()` 方法  
`serializeIntoHash()` 方法  
`serializerFor()` 方法  
`updateRecord()` 方法  
Reddit 应用设置  
精简版  
子版块/`POST` 参见 子版块/`POST` 应用  
关系链接  
分析  
`belongsTo`/`hasMany` 关系  
链接属性  
`normalize()` 方法  
`RESTAdapter` 返回载荷  
`RESTAdapter` RESTful URL 路径  
`RESTSerializer` JSON 响应  
同步加载旁路数据  

### S

从头创建的适配器类  
CRUD 操作  
`findAll()` 方法  
内置  
联系人页面  
实现  
`normalizeResponse()` 方法  
`normalizeResponse()` 方法  
记录 参见 记录  
序列化器类  
设置  
序列化  
抽象类  
内置命令  
`JSONAPISerializer` 参见 `JSONAPISerializer`  
`JSONSerializer`  
模型  
多态关系  
`RESTSerializer`  
序列化器 后测试  
子版块应用, Ember.js  
子版块/`POST` 应用适配器  
异步 `findRecord()` 响应  
`normalizeFindRecordResponse()`  
序列化器模板  
`urlForFindRecord()` 方法  
警告消息  
`belongsTo` 关系创建错误  
`JSONSerializer`/`RESTSerializer`  
`keyForAttribute()` 标准化  
`normalizeArrayResponse()`  
帖子模型创建 模板/渲染  
更新属性/关系  
`urlForQuery()`  

### T

测试适配器  
默认测试  
`isInvalid()` 方法  
重写的方法  
标准化自定义  
`normalizeArrayResponse()` 方法  
`normalizeResponse()` 方法  
从头创建的 `store.findAll()`  
序列化  
排列-动作-断言（AAA）模式  
内容  
自定义  
`JSONSerializer`  
`keyForAttribute()` 方法  
`normalizeCreateRecordResponse()`  
通过实现  
`payloadKeyFromModelName()` 方法  
`POST` 请求  
`requestBody` 属性  
`RESTSerializer` 根键  
`serializeIntoHash()` 方法  
`Sinon.js` 单元测试  

### U

`updateRecord()` 方法  

### V, W, X, Y, Z

验证错误  
`detail`/`source`  
`InvalidError` 实例  
JSON:API 错误响应  
