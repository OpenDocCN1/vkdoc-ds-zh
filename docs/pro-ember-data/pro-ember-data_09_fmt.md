# 8. 处理嵌套数据和嵌入式记录

在本章中，我们将探讨处理 API 数据负载中嵌套数据的两种方法。第一种方法涉及定义不带转换的模型属性。第二种方法则利用嵌入式记录。让我们开始吧！

## 声明无转换的属性

假设我们有以下`contact`资源的 JSON 数据，其中`address`是一个嵌套对象：

```json
{
  "id": 1,
  "name": "Richard Hendrix",
  "address": {
    "street": "123 Main St.",
    "zip": "90003"
  }
}
```

在第一章（Ember Data 概述）中，我们学习了四种不同的转换：`string`、`number`、`boolean`和`date`。并没有对象转换。为了在模型中添加一个包含对象的`address`属性，我们可以声明该属性而不指定转换：

```javascript
// app/models/contact.js
import Model, { attr } from '@ember-data/model';
export default class ContactModel extends Model {
  @attr('string')  name;
  @attr address;
}
```

当不指定转换时，Ember Data 会直接传递该值并将其设置在模型上。

要修改`address`的特定属性，请使用`model.set()`的点符号语法：

```javascript
model.set('address.street', '1234 New St.');
```

现在假设我们有如下`contact`资源的 JSON 数据：

```json
{
  "id": 1,
  "name": "Richard Hendricks",
  "history": [
    { "url": "http://piedpiper.com", "time": "2015-10-01T20:12:53Z" },
    { "url": "http://hooli.com", "time": "2014-10-01T20:12:53Z" },
    { "url": "http://endframe.com", "time": "2013-10-01T20:12:53Z" }
  ]
}
```

这里有一个包含 URL 数组的`history`属性。我们将不在模型上指定转换，以便`history`数据直接传递并设置在记录上：

```javascript
// app/models/contact.js
import Model, { attr } from '@ember-data/model';
export default class ContactModel extends Model {
  @attr('string') name;
  @attr history;
}
```

让我们看看如何操作`history`属性。你可能会认为可以通过直接修改某个历史项来更新 UI：

```javascript
model.history[0].url = 'http://amazon.com';
```

然而，这样是行不通的。如果需要修改特定的历史项，我们必须使用`set`方法，例如：

```javascript
import { set } from '@ember/object';
let googleItem = model.history[0];
set(googleItem, 'url', 'http://amazon.com');
```

使用`set()`会改变属性并通知 Ember 重新渲染。或者，我们可以创建一个新的数组引用并重新赋值`history`属性：

```javascript
let modifiedHistory = [...model.history, 'http://amazon.com'];
model.set('history', modifiedHistory);
```

## 嵌入式记录

带有`id`的嵌套对象也可以使用`EmbeddedRecordsMixin` mixin 作为记录来处理。假设 JSON 现在看起来像这样：

```json
{
  "id": 1,
  "name": "Richard Hendricks",
  "skills": [
    { "id": 1, "name": "Compression" },
    { "id": 2, "name": "Java" },
    { "id": 3, "name": "Algorithms" }
  ]
}
```

我们可以将`skills`下的每个对象转换为记录，并在`contact`和`skill`之间建立`hasMany`关系：

```javascript
// app/models/contact.js
import Model, { attr, hasMany } from '@ember-data/model';
export default class ContactModel extends Model {
  @attr('string') name;
  @hasMany('skill') skill;
}
```

为了让 Ember Data 建立`hasMany`关系，我们可以在序列化器中使用`EmbeddedRecordsMixin`：

```javascript
// app/serializers/contact.js
import JSONSerializer from '@ember-data/serializer/json';
import { EmbeddedRecordsMixin } from '@ember-data/serializer/rest';
export default class ContactSerializer extends JSONSerializer.extend(
  EmbeddedRecordsMixin
) {
  attrs = {
    skills: { embedded: 'always' }
  };
}
```

在`attrs`属性中，将`skills`设置为`{ embedded: 'always' }`。这同样适用于`belongsTo`关系。此示例使用的是`JSONSerializer`，但相同技术也可应用于基于`RESTSerializer`的 API。请注意，`EmbeddedRecordsMixin`不适用于`JSONAPISerializer`。

`EmbeddedRecordsMixin`同样适用于嵌套数据中的嵌套数据！例如，假设每个`skill`现在都有一个嵌入的`category`模型：

```json
{
  "id": 1,
  "name": "Richard Hendricks",
  "skills": [
    {
      "id": 1,
      "name": "Compression",
      "category": {
        "id": 3,
        "name": "Technology"
      }
    },
    {
      "id": 2,
      "name": "Algorithms",
      "category": {
        "id": 6,
        "name": "Technology"
      }
    }
  ]
}
```

与前面的代码类似，创建一个`category`模型并指定关系：

```javascript
// app/models/skill.js
import Model, { attr, belongsTo } from '@ember-data/model';
export default class SkillModel extends Model {
  @attr('string') name;
  @belongsTo('category', { async: false }) category;
}

// app/models/category.js
import Model, { attr } from '@ember-data/model';
export default class CategoryModel extends Model {
  @attr('string') name;
}
```

接下来，创建一个使用`EmbeddedRecordsMixin`的`skill`序列化器：

```markdown
```javascript
// app/serializers/skill.js
import RESTSerializer, {
  EmbeddedRecordsMixin
} from '@ember-data/serializer/rest';
export default class SkillSerializer extends RESTSerializer.extend(
  EmbeddedRecordsMixin
) {
  attrs = {
    category: { embedded: 'always' }
  };
}
```

嵌套模型可以递归使用`EmbeddedRecordsMixin`来处理记录中的嵌套记录。

## 总结

在本章中，我们探讨了几种处理嵌套数据和嵌入式记录的不同方法。如果需要将嵌套数据转换为记录，请使用`EmbeddedRecordsMixin`。否则，直接声明不带转换的属性即可。

到目前为止，我们一直研究如何处理成功的 API 响应。在下一章中，我们将学习如何处理错误响应。
```