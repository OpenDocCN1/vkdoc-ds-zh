# 4. API 核心基础

在 ArcGIS API 4 for JavaScript 中，有少数几个基本概念构成了 API 的构建方式。如果您熟悉这些核心概念，那么在使用 API 时将会受益匪浅。

## Accessor 模块

ArcGIS API for JavaScript 的一个重要新增功能是 `Accessor` 模块。它位于 `esri/core/Accessor`，这足以说明它是该 API 的核心部分。这并非偶然，因为该 API 的大部分功能都构建在 `Accessor` 及其能力之上。

`Accessor` 模块大致基于通过 `Object.defineProperty` 方法实现的 ES5 getter/setter。

### 监听属性变化

`Accessor` 的有趣之处在于，你无需监听事件来了解对象何时发生了变化，而只需直接监听属性本身的变化即可。

```
// 监听事件
widget.on("value-change", (val) => {
    console.log(val);
});

// 新方法
widget.watch("value", (val) => {
    console.log(val);
})
```

这也开启了监听任何值变化的能力，而不仅仅是那些分配了事件的变化。这与 `dojo/Stateful` 的工作方式类似，区别在于你无需使用 `get/set` 方法。

```
// dojo/stateful
widget.set("value", newValue);

// Accessor
widget.value = newValue;
```

不过，你仍然可以使用 `set` 方法来设置 `Accessor` 的深层属性。

```
// 更新属性 "view.map.basemap.title"
view.set("map.basemap.title", newTitle);
```

或者，你也可以一次性设置多个属性。

```
// 更新属性 "view.map.basemap.title"
view.set({
  constraints: {
    minScale: 250000,
    maxScale: 0
  },
  rotation: 45
});
```

你还可以监听嵌套属性的变化。

```
// 更新属性 "view.map.basemap.title"
view.watch("map.basemap.title", (newValue) => /*处理结果*/);
```

每个方法都会返回一个 `WatchHandle`，因此你可以通过 `remove` 方法停止监听属性变化。

```
// 更新属性 "view.map.basemap.title"
const handler = view.watch("map.basemap.title", (newValue) => /*处理结果*/);
// 在应用程序的某个时刻移除该处理器
function cleanup() {
  handler.remove();
}
```

监听属性变化还有另一个很棒的特性，那就是能够同时监听多个属性的变化。

```
import Map from "esri/Map";
import MapView from "esri/views/MapView";

const map = new Map({
  basemap: "streets"
});

const view = new MapView({
  container: "viewDiv",
  map: map,
  zoom: 4,
  center: [15, 65]
});

view.watch("center, scale", (value, oldValue, propertyName) => {
  if (propertyName === "center") {
    // 打印 center 的 x 和 y 值
    console.log(value.x, value.y);
  } else {
    // 打印 scale 值
    console.log(value);
  }
});
```

在这个例子中，你同时监听了视图的 `center` 和 `scale` 属性。当这些属性发生变化时，你只需决定如何处理它们。在这个演示中，你只需将结果打印到开发者控制台。在你的应用程序中能够灵活地监听属性变化，这充分证明了 `Accessor` 为 API 带来的强大功能。

由于监听属性变化的能力是 API 的核心功能，因此提供了一个实用模块来帮助你实现这一点。你可以在 `esri/core/watchUtils` 中找到这个实用工具。如果你发现在监听属性变化时需要进行一些条件检查，这个工具会非常有用。

```
view.watch("stationary", (value) => {
  if (value) {
    // 仅在值为 true 时执行某些操作
  }
});
```

这增加了一些认知负担，至少对我来说是这样，我想避免这种情况。我只关心值何时为 `true`，因此我可以使用一个实用工具来实现这一点。

```
watchUtils.whenTrue(view, "stationary", () => {
  // 执行某些操作
});
```

让我们看一个示例，该示例会在你与地图交互时统计屏幕上的点数。

以下是一些示例代码，展示了如何实现这一点：

```
import Map from "esri/Map";
import FeatureLayer from "esri/layers/FeatureLayer";
import watchUtils from "esri/core/watchUtils";
import SceneView from "esri/views/SceneView";
import QueryTask from "esri/tasks/QueryTask";
import Query from "esri/tasks/supports/Query";

const url = "http://services.arcgis.com/P3ePLMYs2RVChkJx/arcgis/rest/services/USA_Major_Cities/FeatureServer/0";
const map = new Map({
    basemap: "streets",
    layers: [ new FeatureLayer({ url }) ]
}):
const query = new Query();
const queryTask = new QueryTask({ url });
const view = new SceneView({
    container: "map",
    map: map,
    center: [-118.182, 33.913],
    scale: 836023
});

query.watch("geometry", () =>{
    qTask.executeForCount(query).then((count) =>{
        document.getElementById("cityCount").innerText = count;
    });
});

view.then(() =>{
    watchUtils.whenTrue(view, "stationary", () =>{
        query.geometry = view.extent;
    });
    watchUtils.whenFalse(view, "stationary", () =>{
        document.getElementById("cityCount").innerText = "...";
    });
});
```

### watchUtils

正如我们所看到的，监听属性变化在 API 中非常有用。由于在监听属性时，你的具体需求可能因应用程序而异，因此有一个名为 `watchUtils` 的辅助工具可供使用。

让我们来看几个更有趣的方法：

- `watchUtils.init()`：当你需要获取属性的初始值时，这个工具会派上用场。请记住，当你监听属性变化时，只有在属性实际发生变化时你才会收到通知。这意味着，除非你使用此工具来监听变化并同时获取初始值，否则你将无法知道许多属性的初始值。

    ```
    1   watchUtils.init(view, "stationary", (value, oldValue) => console.log(`New/Initial value is "${value}" and Old value is "${oldValue}"`));
    ```

    这将为你提供属性的初始值（即默认值），以及像常规监听器一样的所有未来更新。如果你需要初始值来启动应用程序中的某个操作或在页面上显示一个值，但又不想等待值发生变化，这将非常有用。

# 使用 WatchUtils 和 Accessor

## 监听辅助工具

`watchUtils.once()`：顾名思义，当你只关心监听一次属性变化时，这个监听方法非常有用。它也是返回`PromiseWatchHandle`的监听辅助方法的一个子集。⁸ 这意味着你可以像对待任何 Promise 一样对待这个监听器，甚至可以在应用程序中链式调用此监听器的结果。

```javascript
watchUtils.once(view, "camera").then(({ value, oldValue, propertyName, target }) => {
  return geometryEngineAsync.union([oldValue.position, value.position]);
}).then(positionMultiPoints => {
  // store or continue using unioned points
});
```

如你所见，某些监听辅助方法能够返回 Promise 是非常有用的。

`watchUtils.pausable()`：我非常喜欢这个小工具。顾名思义，这是一个你可以暂停，然后根据需要恢复的监听器。

```javascript
const handler = watchUtils.pausable(view, "center", updateComponentMethod);
// resize component, no need to update during animated resize
handler.pause();
// resize of component is done, resume updates
handler.resume();
```

通过这个小示例，我可能有一个自定义组件，它会随着视图的`center`属性变化而更新。但是，我可能为调整此组件的大小或将其停靠到一个不完全可见的工具栏设置了动画。关键在于，我只想暂停此组件的更新，以便调用`handler.pause()`。然后，当组件处于我希望继续更新的状态时，我可以调用`handler.resume()`。我发现这个`pausable()`辅助方法是`watchUtils`辅助工具中最有用的一个。

我建议你查阅文档⁹，并在监听属性变化时遇到特殊情况时，记住这些辅助工具。

## 自动转换（Autocasting）

使用 Accessor 的另一个强大特性是，你可以定义能够自动转换为某个类或模块的属性。这允许你传入看起来像某种类型的数据，并将其转换为该类型。

```javascript
import Accessor from "esri/core/Accessor";
import Extent from "esri/geometry/Extent";

const Model = Accessor.createSubclass({
  properties: {
    extent: Extent
  }
});

export default Model;
```

现在，你可以将一个类似`extent`的对象传递给这个 Accessor，它会被转换成一个真正的`Extent`对象。

```javascript
const model = new Model({
  extent: {
    xmin: -122.68,
    ymin: 45.53,
    xmax: -122.45,
    ymax: 45.6
  }
});

model.watch("extent", (val) => console.log(val));
```

如你所见，这使得构建模块和类变得非常容易，这些模块和类允许你传入简单的数据对象，这些对象可以自动转换为更复杂的对象。

自动转换在 API 中变得非常有用，以至于你甚至可能忘记它的存在。一个很好的例子是当你创建一个`WebMap`时。

```javascript
const webmap = new WebMap({
  portalItem: {
    id: "e691172598f04ea8881cd2a4adaa45ba"
  }
});

const view = new MapView({
  map: webmap,  //the WebMap instance created above
  container: "viewDiv"
});
```

`WebMap`在第 3 章中有详细介绍。但在这个例子中，你只是将一个`PortalItem`的`id`值传递给了`WebMap`的构造函数。在内部，`WebMap`知道`portalItem`属性的类型是`PortalItem`。因此，通过传入一个具有`PortalItem`所需最小属性的对象，`WebMap`可以将该对象自动转换为正确的实例。

如果你一直在使用 3.x 版本的 API，那么简单地监听属性变化而不是监听事件发生可能是一个新概念，但我希望你能看到在 4.x 版本的 API 中监听属性变化的简单性和灵活性，并从使用此功能中获得一些真正的好处。

## 扩展 Accessor

从 ArcGIS API for JavaScript 的 4.2 版本开始，Accessor 文档现在包含了“实现 Accessor”部分。¹⁰ 该部分内容相当详尽，因此我们不重复其内容，而是来看几个基础点。

你已经看到了如何使用`Accessor.createSubclass()`自己实现一个 Accessor。关键在于提供一个`properties`对象，该对象将包含有关你实现的信息。你已经看到了如何预定义属性的类型。

```javascript
const Model = Accessor.createSubclass({
  properties: {
    extent: Extent
  }
});
```

但你还可以更进一步，拥有一些计算属性。

```javascript
const Model = Accessor.createSubclass({
  properties: {
    graphic: new Graphic(/* Graphic Properties */),
    bufferedGeometry: {
      dependsOn: ["graphic"],
      get: function() {
        return geometryEngine.buffer(this.graphic.geometry);
      }
    }
  }
});
```

在这个示例中，你想要一个基于`graphic`值的`bufferedGeometry`属性。在这种情况下，我不希望我的`Model`的用户在他们那边使用`GeometryEngine`，所以我可以简化它并为他们提供这个值。

### TypeScript 集成

API 的 4.2 版本还引入了改进的 TypeScript 集成，用于使用 Accessors。这种改进的集成还附带了一组装饰器¹¹，简化了实现过程。

这意味着你可以将之前的示例更新为如下所示：

```typescript
import Accessor from "esri/core/Accessor";
import Graphic from "esri/Graphic";
import Geometry from "esri/geometry/Geometry";
import { declared, subclass, property } from "esri/core/accessorSupport/decorators";

@subclass()
class Model extends declared(Accessor) {

  @property()
  graphic: Graphic = new Graphic();

  @property({
    dependsOn: ["graphic"]
  })
  get bufferedGeometry(): Geometry {
    return geometryEngine.buffer(this.graphic.geometry);
  }

}
```

这些装饰器非常有用，可以使实现 Accessor¹² 和定义属性更加明确。你可以查阅装饰器文档以获取更多详细信息。¹³

## 集合

ArcGIS API 4 for JavaScript 中的集合是类似数组的数据容器。它们的外观和行为都像数组，但不能使用 `for` 循环进行迭代。相反，您可以使用众多数组方法之一来处理集合。这些方法大多是典型的数组方法，其工作方式符合预期。有些方法是集合独有的，例如 `getItemAt()`。

在不直接复制文档的前提下，我想指出一些集合独有的方法。

*   `ofType`：允许您创建类型化集合

# 集合（Collections）的方法

*   `add`：工作方式类似于 `Array.prototype.push`
*   `addMany`：接受一个对象数组并将其添加到集合中
*   `clone`：顾名思义，创建集合的克隆
*   `remove`：从集合中移除传入的参数
*   `removeAll`：清空集合
*   `removeAt`：轻松地从集合中移除指定索引处的项
*   `removeMany`：移除给定数组中的所有项
*   `reorder`：将集合中的某项移动到新的索引位置
*   `toArray`：将集合导出为普通的原生数组

集合的另一个特性是能够监听 `change` 事件。

```javascript
collection.on("change", ({ added, moved, removed }) => {/*执行一些酷炫的操作*/});
```

`change` 事件返回一个包含 `added`、`moved` 和 `removed` 属性的对象。每个属性都是一个数组，其中包含集合中发生变化的项。这是一个极其有用的特性，是原生 JavaScript 数组所不具备的。您可以监视对集合所做的任何更改并做出相应处理。

## 承诺（Promises）

视图和图层的一个共同点是它们都是承诺。

承诺对于执行异步操作非常有用。您会在整个 API 中发现承诺的身影。这是因为有大量的异步操作在进行。

当您创建一个视图时，可以检查承诺方法 `then` 来查看视图何时加载完成，并开始检查属性。

```javascript
view.then(() => {
  view.watch("extent.xmin", (xmin) => console.log(xmin));
});
```

在视图加载并准备就绪后开始监听 Accessor 属性的变化也是一个好习惯。承诺非常有用，您可以对它们的结果进行链式调用。

```javascript
view.then(() => {
  return view.whenLayerView(myLayer);
})
.then((layerView) => {
  return watchUtils.whenFalseOnce(layerView, "updating");
})
.then(({ target: layerView }) => {
  return layerView.queryFeatures()
})
.then((features => {
  view.goTo(features);
}))
.otherwise(error => {
  // 在此处捕获 Promise 链中的错误
});
```

在这段代码片段中，您等待视图加载，从视图中获取一个 `LayerView`，查询视图中的要素，然后使用 `goTo` 方法将地图动画定位到这些要素。

在这个示例中，您使用了 `watchUtils` 模块来帮助检查属性变化，但您只关心 `LayerView` 何时完成更新，以便获取要素。我将在下一章关于 Accessor 的内容中更详细地介绍这个方法。

在整个 API 中使用承诺的能力对于处理 Web 地图应用中发生的多个异步操作非常有用。

## 总结

至此，您应该对 ArcGIS API for JavaScript 的核心基础原理有了相当清晰的认识。扎实掌握 Accessor、集合和承诺的工作原理将极大地提升您使用该 API 构建应用程序的能力。

*   Accessor 提供了一种轻松监听属性变化的方法，而无需监听事件。
*   如果您想知道集合是否已更新，集合对于存储通常可能放在数组中的数据非常有用。
*   承诺允许您处理异步数据，并且在视图的情况下，让您知道它们何时可以使用。

### 脚注

*   [`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Accessor.html`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Accessor.html)
*   [`http://javascriptplayground.com/blog/2013/12/es5-getters-setters/`](http://javascriptplayground.com/blog/2013/12/es5-getters-setters/)
*   [`https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)
*   [`https://dojotoolkit.org/reference-guide/1.10/dojo/Stateful.html`](https://dojotoolkit.org/reference-guide/1.10/dojo/Stateful.html)
*   [`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Accessor.html#~WatchHandle`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Accessor.html#~WatchHandle)
*   [`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-watchUtils.html`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-watchUtils.html)
*   [`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-watchUtils.html`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-watchUtils.html)
*   [`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-watchUtils.html#~PromisedWatchHandle`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-watchUtils.html#~PromisedWatchHandle)
*   [`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-watchUtils.html`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-watchUtils.html)
*   [`https://developers.arcgis.com/javascript/latest/guide/implementing-accessor/index.html`](https://developers.arcgis.com/javascript/latest/guide/implementing-accessor/index.html)
*   [`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-accessorSupport-decorators.html`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-accessorSupport-decorators.html)
*   [`https://developers.arcgis.com/javascript/latest/guide/implementing-accessor/index.html`](https://developers.arcgis.com/javascript/latest/guide/implementing-accessor/index.html)

[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-accessorSupport-decorators.html`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-accessorSupport-decorators.html)

[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html)

[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html)
16
[`https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
17
[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#getItemAt`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#getItemAt)
18
[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#.ofType`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#.ofType)
19
[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#add`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#add)
20
[`https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)
21
[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#addMany`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#addMany)
22
[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#clone`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#clone)
23
[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#remove`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#remove)
24
[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#removeAll`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#removeAll)
25
[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#removeAt`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#removeAt)
26
[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#removeMany`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#removeMany)
27
[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#reorder`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#reorder)
28
[`https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#toArray`](https://developers.arcgis.com/javascript/latest/api-reference/esri-core-Collection.html#toArray)
29
[`https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
30
[`https://developers.arcgis.com/javascript/latest/api-reference/esri-views-MapView.html#goTo`](https://developers.arcgis.com/javascript/latest/api-reference/esri-views-MapView.html#goTo)