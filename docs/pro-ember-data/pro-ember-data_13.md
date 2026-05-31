# 12. 消费 Reddit API

在本章中，我们将构建一个简单的应用程序，使用 Ember Data 消费 Reddit API。在构建过程中，我们将进行多次适配器和序列化器自定义，并观察它们如何协同工作。让我们开始吧！

## 设置

我创建了一个简单的 Reddit 应用程序，用于显示来自 `emberjs` 子版块的帖子。该应用程序的代码可以在本书源代码的 chapter-12 文件夹中找到。



## 我们将使用的 Reddit API

我们将使用两个 Reddit API 来构建 [`https://www.reddit.com/r/emberjs/`](http://www.reddit.com/r/emberjs/) 的精简版本：

1. `GET` `https://www.reddit.com/r/<子版块名称>.json`
2. `GET` `https://www.reddit.com/api/info.json?id=<子版块 ID>`

第一个 API 返回指定子版块名称下的 `post` 资源列表。例如，`GET` [`https://www.reddit.com/r/emberjs.json`](https://www.reddit.com/r/emberjs.json) 会返回 `emberjs` 子版块的所有帖子。此端点的 URL 与您通常访问的 HTML 页面相同，但带有 `.json` 后缀。我们将为这些资源创建一个 `post` 模型。

第二个 API 返回单个资源，其中包含指定子版块的相关信息，例如标题、描述、页面 URL、创建时间以及订阅者数量。例如，`GET` [`https://www.reddit.com/api/info.json?id=t5_2tjb2`](https://www.reddit.com/api/info.json%253Fid%253Dt5_2tjb2) 会返回 `emberjs` 子版块的这些详细信息。我们将为此资源创建一个 `subreddit` 模型。现在您可能会想，查询字符串参数 `id` 中的值 `t5_2tjb2` 是从哪里来的。这个值可以在第一个 API 端点的响应中找到，位于每个 `post` 资源的 `subreddit_id` 键下，您将在后面看到。这个键将用于在 `post` 记录上设置一个异步的 `belongsTo` 关系，该关系将用于获取 `subreddit` 记录。

以下是每个端点的示例响应。我已删除部分数据，以便更清楚地看到有效负载的结构。

以下是 `GET` `https://www.reddit.com/r/<子版块名称>.json` 端点的响应示例：

```
{
"kind": "Listing",
"data": {
"children": [
{
"kind": "t3",
"data": {
"title": "Ember Data：查找记录",
"num_comments": 2,
"subreddit_id": "t5_2tjb2",
"url": "https://guides.emberjs.com/release/models/finding-records/"
},
{
"kind": "t3",
"data": {
"title": "Ember Data：定义模型",
"num_comments": 5,
"subreddit_id": "t5_2tjb2",
"url": "https://guides.emberjs.com/release/models/defining-models/"
}
}
],
"after": "t3_fuimy0",
"before": null
}
}
```

以下是 `GET` `https://www.reddit.com/api/info.json?id=<子版块 ID>` 端点的响应示例：

```
{
"kind": "Listing",
"data": {
"modhash": "aicumd7e1n985f0e542d5e66cf935817c95a95d821cfdcc56b",
"dist": 1,
"children": [
{
"kind": "t5",
"data": {
"display_name": "emberjs",
"title": "ember.js：面向雄心勃勃的 Web 开发者的框架",
"primary_color": "#e34c32",
"subscribers": 3867,
"id": "2tjb2",
"description": "Ember.js 是一个高效、久经考验的 JavaScript 框架，用于构建现代 Web 应用程序。它包含构建适用于任何设备的丰富 UI 所需的一切。",
"url": "/r/emberjs/",
"created_utc": 1328907736.0
}
}
],
"after": null,
"before": null
}
}
```

现在我们已经知道需要访问哪些 API 端点，让我们开始构建应用程序。

## 在子版块中获取帖子

### 路由

首先，让我们创建一个名为 `reddit` 的路由，当用户访问 `/` 时该路由将被激活：

```
ember generate route reddit --path="/"
```

在路由的 `model` 钩子中，我们将查询 `emberjs` 子版块的最新帖子：

```
app/routes/reddit.js
import Route from '@ember/routing/route';
export default class RedditRoute extends Route {
  model() {
    return this.store.query('post', {
      subreddit: 'emberjs'
    });
  }
}
```

### Post 模型

接下来，让我们创建一个 `post` 模型：

```
ember generate model post title:string url:string numComments:number

app/models/post.js
import Model, { attr } from '@ember-data/model';
export default class PostModel extends Model {
  @attr('string') title;
  @attr('string') url;
  @attr('number') numComments;
}
```

### Post 和 Reddit 适配器

此时，如果我们查看控制台，将会看到以下错误：

```
GET http://localhost:4200/posts?subreddit=emberjs 404 (Not Found)
```

正如我们在前几章所了解的，适配器负责确定 API 的 URL。让我们再次查看我们将要使用的两个 API：

1. `GET` `https://www.reddit.com/r/<子版块名称>.json`
2. `GET` `https://www.reddit.com/api/info.json?id=<子版块 ID>`

这两个 API 端点具有相同的主机 [`https://www.reddit.com`](http://www.reddit.com)，但 URL 路径不同。我们将采取的方法是创建两个适配器。我们将拥有一个 `reddit` 适配器，用于设置主机，并可能处理 Reddit API 端点之间的其他共性。然后我们将拥有一个 `post` 适配器，用于处理第一个端点的具体细节：

```
ember generate adapter reddit
ember generate adapter post

app/adapters/reddit.js
import RESTAdapter from '@ember-data/adapter/rest';
export default class RedditAdapter extends RESTAdapter {
  host = 'https://www.reddit.com';
}

app/adapters/post.js
import RedditAdapter from './reddit';
export default class PostAdapter extends RedditAdapter {
  namespace = 'r';
}
```

此时，控制台中会出现一个错误，因为我们的应用程序正在向 [`https://www.reddit.com/r/posts?subreddit=emberjs`](http://www.reddit.com/r/posts%253Fsubreddit%253Demberjs) 发起 `GET` 请求，这不是正确的 URL。然而，当我们调用 `store.query('post', { subreddit: 'emberjs' })` 时，这正是我们应预期的 URL。我们想要访问的 URL 是 [`https://www.reddit.com/r/emberjs.json`](https://www.reddit.com/r/emberjs.json)。让我们重写 `urlForQuery(query)` 方法，使应用程序能够访问正确的端点：

```
app/adapters/post.js
import RedditAdapter from './reddit';
export default class PostAdapter extends RedditAdapter {
  namespace = 'r';
  urlForQuery(query) {
    let { subreddit } = query;
    delete query.subreddit;
    return `${this.host}/${this.namespace}/${subreddit}.json`;
  }
}
```

我们现在正在向正确的 URL 发起请求！



### 帖子序列化器

尽管我们已经做了处理，但控制台中仍然显示一个错误：

```
Error
while processing route: reddit Assertion Failed: The response to `store.query` is expected to be an array but it was a single record. Please wrap your response in an array or use `store.queryRecord` to query for a single record.
```

出现此错误的原因是，如果没有显式定义序列化器，Ember Data 默认会使用 `JSONAPISerializer`。由于这些 API 并不遵循 JSON:API 规范，我们不希望使用这个默认行为。相反，我们可以继承 `JSONSerializer` 或 `RESTSerializer`。由于此 API 不包含任何侧加载资源，让我们为 `post` 模型创建一个序列化器，并继承 `JSONSerializer`：

```bash
ember generate serializer post
```

```javascript
// app/serializers/post.js
import JSONSerializer from '@ember-data/serializer/json';
export default class PostSerializer extends JSONSerializer {}
```

让我们再来看一下 API 返回的响应：

```json
{
  "kind": "Listing",
  "data": {
    "children": [
      {
        "kind": "t3",
        "data": {
          "title": "Ember Data: Finding Records",
          "num_comments": 2,
          "subreddit_id": "t5_2tjb2",
          "url": "https://guides.emberjs.com/release/models/finding-records/"
        }
      },
      {
        "kind": "t3",
        "data": {
          "title": "Ember Data: Defining Models",
          "num_comments": 5,
          "subreddit_id": "t5_2tjb2",
          "url": "https://guides.emberjs.com/release/models/defining-models/"
        }
      }
    ],
    "after": "t3_fuimy0",
    "before": null
  }
}
```

因为我们正在调用 `store.query()`，`JSONSerializer` 期望响应中包含一个数组。而这个响应中的资源数组位于 `data.children` 中，我们可以通过使用 `normalizeArrayResponse()` 来提取它。现在，如果我们查看每个资源，会发现存在一个额外的 `data` 属性。我们还需要将其提取出来，这可以通过使用 `normalize()` 来实现。最终，我们希望标准化后的数据负载看起来像下面这样：

```json
[
  {
    "title": "Ember Data: Finding Records",
    "num_comments": 2,
    "subreddit_id": "t5_2tjb2",
    "url": "https://guides.emberjs.com/release/models/finding-records/"
  },
  {
    "title": "Ember Data: Defining Models",
    "num_comments": 5,
    "subreddit_id": "t5_2tjb2",
    "url": "https://guides.emberjs.com/release/models/defining-models/"
  }
]
```

一旦我们将响应标准化为此格式，我们的 `post` 序列化器就能正常工作。让我们继续实现它：

```javascript
// app/serializers/post.js
import JSONSerializer from '@ember-data/serializer/json';
export default class PostSerializer extends JSONSerializer {
  normalizeArrayResponse(store, primaryModelClass, payload, id, requestType) {
    return super.normalizeArrayResponse(
      store,
      primaryModelClass,
      payload.data.children,
      id,
      requestType
    );
  }

  normalize(typeClass, hash) {
    return super.normalize(typeClass, hash.data);
  }
}
```

### 渲染帖子

现在错误已经解决，让我们更新模板并渲染数据：

```handlebars
{{!-- app/templates/reddit.hbs --}}

{{#each @model as |post|}}
  {{post.title}} ({{post.numComments}} comments)
{{/each}}
```

### 修复帖子序列化器

如果我们查看页面，会发现帖子的 `url` 和 `title` 能够正常渲染，但 `numComments` 没有显示。如果我们打开 Ember 检查器中的“数据”选项卡并查看一条 `post` 记录，可以看到 `numComments` 为 `undefined`。正如我们在第 3 章 – API 响应格式与序列化器中学到的，当使用 `JSONSerializer` 时，默认情况下 JSON 键预期是驼峰式命名（camelCased）。让我们通过重写 `keyForAttribute()` 来更新 `post` 序列化器，以处理蛇形命名（snake_cased）的键：

```javascript
// app/serializers/post.js
import JSONSerializer from '@ember-data/serializer/json';
import { underscore } from '@ember/string';

export default class PostSerializer extends JSONSerializer {
  keyForAttribute(attr) {
    return underscore(attr);
  }

  normalizeArrayResponse(store, primaryModelClass, payload, id, requestType) {
    return super.normalizeArrayResponse(
      store,
      primaryModelClass,
      payload.data.children,
      id,
      requestType
    );
  }

  normalize(typeClass, hash) {
    return super.normalize(typeClass, hash.data);
  }
}
```

完成这些之后，API 返回的所有数据都被正确地标准化，我们的页面也按照预期渲染了！现在让我们继续获取并展示 `emberjs` 子版块的详细信息。

## 获取子版块详情

在页面顶部，我们想要展示关于 `emberjs` 子版块的详细信息，这些信息可以从第二个 API 端点 `GET https://www.reddit.com/api/info.json?id=<subreddit_id>` 获取。如前所述，`subreddit_id` 的值可以从第一次 API 调用中每个 `post` 资源的 `subreddit_id` 字段获取。

### 子版块模型

首先，让我们创建另一个名为 `subreddit` 的模型：

```bash
ember generate model subreddit title:string description:string subscribers:number
```

```javascript
// app/models/subreddit.js
import Model, { attr } from '@ember-data/model';

export default class SubredditModel extends Model {
  @attr('string') title;
  @attr('string') description;
  @attr('number') subscribers;
}
```

接下来，我们将在 `post` 模型上创建一个名为 `subreddit` 的 `belongsTo` 关系。该关系将用于使用 `subreddit_id` 字段异步加载 `subreddit` 资源：

```javascript
// app/models/post.js
import Model, { attr, belongsTo } from '@ember-data/model';

export default class PostModel extends Model {
  @attr('string') title;
  @attr('string') url;
  @attr('number') numComments;
  @belongsTo('subreddit', { async: true }) subreddit;
}
```

### 更新帖子序列化器

如果你还记得第 4 章 – 常见适配器与序列化器自定义，其中的“将外键映射到关联”部分，为了让 `JSONSerializer` 能够建立 `subreddit` 关联，我们需要将 API 响应中的 `subreddit_id` 映射到 `subreddit`。我们可以使用 `attrs` 来实现，它允许我们将 API 中的 JSON 键映射到模型的属性或关联上：

```javascript
// app/serializers/post.js
import JSONSerializer from '@ember-data/serializer/json';
import { underscore } from '@ember/string';

export default class PostSerializer extends JSONSerializer {
  attrs = {
    subreddit: 'subreddit_id'
  };
  // ...
}
```



### 加载异步的 Subreddit 关联

我们将此关联设为异步，以便在模板中访问 `subreddit` 记录时，让 Ember Data 自动加载该记录：

```handlebars
{{!-- app/templates/reddit.hbs --}}
{{#let @model.firstObject.subreddit as |subreddit|}}
{{subreddit.title}}
{{subreddit.description}}
Members: {{subreddit.subscribers}}
{{/let}}

{{!-- ... --}}
```

由于所有帖子的 `subreddit` 记录都是同一份，我们可以访问其中任意一个。在上面的模板中，我们通过第一个 `post` 记录来获取 `subreddit` 记录。

如果打开控制台，会看到以下错误：

```text
GET http://localhost:4200/subreddits/t5_2tjb2 404 (Not Found)
```

这并不是我们期望应用用来加载 `subreddit` 记录的正确 URL。仔细观察，这个 URL 使用的子版块 ID 是正确的，而且如果换成 `store.findRecord('subreddit', 't5_2tjb2')` 调用，URL 看起来也符合预期。这暗示我们需要重写哪个适配器方法。现在让我们为 `subreddit` 模型创建一个适配器，它继承自 `RedditAdapter`，需要设置 `namespace` 并重写 `urlForFindRecord()` 方法：

```bash
ember generate adapter subreddit
```

```javascript
// app/adapters/subreddit.js
import RedditAdapter from './reddit';
export default class SubredditAdapter extends RedditAdapter {
  namespace = 'api';

  urlForFindRecord(id) {
    return `${this.host}/${this.namespace}/info.json?id=${id}`;
  }
}
```

现在适配器已向正确的 `https://www.reddit.com/api/info.json?id=t5_2tjb2` 发出请求，不过仍会出现一些警告。

**警告**

> 在载荷中遇到 "kind"，但未找到对应的模型 "kind"（通过 `DS.RESTSerializer.modelNameFromPayloadKey("kind")` 解析模型名）。
>
> 在载荷中遇到 "data"，但未找到对应的模型 "datum"（通过 `DS.RESTSerializer.modelNameFromPayloadKey("data")` 解析模型名）。

由于这些警告与序列化器有关，这意味着我们需要创建一个 `subreddit` 序列化器：

```bash
ember generate serializer subreddit
```

```javascript
// app/serializers/subreddit.js
import JSONSerializer from '@ember-data/serializer/json';
export default class SubredditSerializer extends JSONSerializer {}
```

现在我们需要规范化 `findRecord()` 的响应，这与之前在 `post` 序列化器中的做法类似。让我们再看看这个响应：

```json
{
  "kind": "Listing",
  "data": {
    "modhash": "aicumd7e1n985f0e542d5e66cf935817c95a95d821cfdcc56b",
    "dist": 1,
    "children": [
      {
        "kind": "t5",
        "data": {
          "display_name": "emberjs",
          "title": "ember.js: a framework for ambitious web developers",
          "primary_color": "#e34c32",
          "subscribers": 3867,
          "id": "2tjb2",
          "description": "Ember.js is a productive, battle-tested JavaScript framework for building modern web applications. It Includes everything you need to build rich UIs that work on any device.",
          "url": "/r/emberjs/",
          "created_utc": 1328907736.0
        }
      }
    ],
    "after": null,
    "before": null
  }
}
```

`subreddit` 资源似乎位于 `data.children[0].data` 中，我们可以通过 `normalizeFindRecordResponse()` 将其提取出来：

```javascript
// app/serializers/subreddit.js
import JSONSerializer from '@ember-data/serializer/json';
export default class SubredditSerializer extends JSONSerializer {
  normalizeFindRecordResponse(
    store,
    primaryModelClass,
    payload,
    id,
    requestType
  ) {
    return super.normalizeFindRecordResponse(
      store,
      primaryModelClass,
      payload.data.children[0].data,
      id,
      requestType
    );
  }
}
```

现在 `emberjs` 子版块的所有详细信息都正确渲染了！

### 修复警告信息

马上就要完成了！如果打开控制台，会看到以下警告：

**警告**

> 您请求的 record 类型为 "subreddit"，id 为 "t5_2tjb2"，但适配器返回的载荷中主要数据的 id 是 "2tjb2"。
>
> 当请求的 id 与适配器返回的 id 相同时，请使用 `store.findRecord()`。在其他情况下，请改用 `store.queryRecord()`。

看来响应返回的 `id` 是 `2tjb2` 而不是 `t5_2tjb2`。让我们通过将资源中的 `id` 更新为我们请求的 `id` 来解决这个问题：

```javascript
// app/serializers/subreddit.js
import JSONSerializer from '@ember-data/serializer/json';
export default class SubredditSerializer extends JSONSerializer {
  normalizeFindRecordResponse(
    store,
    primaryModelClass,
    payload,
    id,
    requestType
  ) {
    let resource = payload.data.children[0].data;
    resource.id = id;
    return super.normalizeFindRecordResponse(
      store,
      primaryModelClass,
      resource,
      id,
      requestType
    );
  }
}
```

更新 `id` 轻而易举，因为 `normalizeFindRecordResponse()` 将其作为参数提供了。

至此，我们的应用已无任何错误或警告地正常运行！

## 总结

在本章中，我们通过使用 `RESTAdapter` 和 `JSONSerializer`，利用 Ember Data 消费了两个 Reddit API。我们无需编写大量代码就实现了这一目标。我们自定义了几个适配器和序列化器的属性与方法，并见证了它们如何协同工作，从而消费一个初看起来可能令人望而生畏的非标准 API。

