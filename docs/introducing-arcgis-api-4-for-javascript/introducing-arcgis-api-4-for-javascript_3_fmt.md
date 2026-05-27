# 2. 入门

让我们停止空谈，开始编写代码。以下是你的第一个应用程序的简单 HTML 页面。本示例中的所有 `index.html` 页面都将以此为基础。你只需在过程中更新 JavaScript 代码即可。

```
5       \
6       ArcGIS API for JavaScript 4
```

这是 `dojoConfig.js` 文件：

```
1   var locationPath = window.location.pathname.replace(/\/[^\/]+$/, '/');
2   window.dojoConfig = {
3     deps: ['app/main'],
4     packages: [{
5       name: 'app',
6       location: locationPath + '/app',
7       main: 'main'
8     }]
9   };
```

这是第一个示例 JavaScript 文件 `app/main.js`：

```
1   import Map from "esri/Map";
2   import SceneView from "esri/views/SceneView";

4   const map = new Map({ basemap: "topo" });
5   const view = new SceneView({
6     container: "mainDiv",
7     map,
8     center: [-118.182, 33.913],
9     scale: 836023
10   });
```

这就是启动一个简单的 `SceneView` 所需的全部代码。现在，你拥有了一个可以操作的交互式 3D 地图。

## 关于 `dojoConfig` 呢？

让我们简要谈谈 `dojoConfig` 在你的开发环境中是如何工作的。对于本书中的大多数示例，我将假设你使用的是 ArcGIS API for JavaScript 的内容分发网络（CDN）版本。

这个 `locationPath` 是怎么回事？

```
1   var locationPath = window.location.pathname.replace(/\/[^\/]+$/, '/');
```

假设你的应用程序托管在 `http://localhost/apps/myapplication`。`location.pathname` 将是 `/apps/myapplication`。正则表达式 `/\/[^\/]+$/` 会将其转换为 `/apps/`。你要做的是为你的应用程序创建一个包，这基本上是一种将你的代码命名空间化到其自己的包中，以将其与依赖项区分开来的方法。

```
1   window.dojoConfig = {
2     ...
3     packages: [{
4       name: 'app',
5       location: locationPath + '/app',
6       main: 'main'
7     }]
8   };
```

这将创建一个名为 `app` 的包，并且你让 ArcGIS CDN 中的 Dojo 知道该应用程序位于你的主机上的 `/apps/app`，而不是 CDN 上。如果你不这样做，Dojo 会尝试在 CDN 上查找一个不存在的 `app` 包。同时，你还指定了 `app` 包有一个名为 `main` 的模块作为入口点。

```
1   window.dojoConfig = {
2     deps: ['app/main'],
3     ...
4   };
```

通过声明 `deps: ['app/main']` 作为项目的依赖项，`app/main` 模块将在应用程序启动时自动加载。实际上，你可以在此数组中列出任何需要在应用程序启动前加载的模块。

你可以在 [`https://dojotoolkit.org/documentation/tutorials/1.10/dojo_config/`](https://dojotoolkit.org/documentation/tutorials/1.10/dojo_config/) 阅读更多关于 `dojoConfig` 的信息。

## AMD 与包

我想强调，你应该使用 ES6 编写代码。这是为了简化你实际编写代码的方式；此外，至少对我来说，它让我忘记了我实际上是在为我的代码使用异步模块定义。¹ 你编写的 ES6 代码将使用诸如 `BabelJS`² 之类的编译器转换为兼容 ES5 的 AMD 模块。但归根结底，你的代码仍然是 AMD，因此你应该对它和包有所了解。

### AMD

当你编写如下代码时：

```
1   import Map from "esri/Map";
2   import SceneView from "esri/views/SceneView";

4   const map = new Map({ basemap: "topo" });
5   const view = new SceneView({
6     container: "mainDiv",
7     map,
8     center: [-118.182, 33.913],
9     scale: 836023
10   });
```

`Babel` 会将其编译成类似这样的代码：

```
1   define(["exports", "module", "esri/Map", "esri/views/SceneView"], function(exports\
2   ts, module, _esriMap, _esriviewsSceneView) {

4     function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : {\
5   'default': obj }; }

7     var _Map = _interopRequireDefault(_esriMap);
8     var _SceneView = _interopRequireDefault(_esriviewsSceneView);

10     var map = new Map"default";
11     var view = new SceneView"default";

18   });
```

不要让所有这些代码吓到你。代码量的增加与 ES6 模块³ 和 `import`⁴ 语句有关。当你编写 `import Map from "esri/Map";` 时，它假定源代码导出了一个默认的 `Object`，该对象实际上指向 `Map` 模块。然而，我们仍然生活在一个并非所有人都编写 ES6 代码的世界，而且模块系统尚未在任何浏览器中原生实现。因此，你仍然需要考虑一些混合搭配的情况。这就是上述输出代码的作用。

在 AMD 中，你使用 `define` 方法创建模块。第一个参数是一个数组，列出了所有其他依赖项。第二个参数是一个函数，返回该模块的实际代码。

不过，正如我之前所说，如果你用 ES6 编写代码，并让编译器创建你的 AMD 模块，你就不需要过多担心这些细节。你应该了解它们，但不要让它们成为你的负担。

更多细节，请参考 Dojo 关于 AMD 模块的文档。⁵

### 包

我注意到一个经常出现的困惑点，就是关于“包”的概念。别担心，我刚开始接触 AMD 时也对此感到困惑。目前，你无需过分担心包的问题，直到你决定要构建自己的应用程序时，才需要真正了解什么是包。

包本质上类似于 Java 中的命名空间。在 ArcGIS API for JavaScript 4 中，`esri` 是一个包，`dojo` 也是一个包。这意味着 `esri/Map` 是 `esri` 包中的一个模块。当你编写自己的应用程序代码时，通常会创建一个 `app` 包。这样你就可以在 `app` 包下引用自己的模块。

因此，你的源代码文件夹可能看起来像这样：

```
1   src
2     ├── app
3        ├── /widgets
4        ├── /services
```

如果你使用的是通过 Bower 发布的 ArcGIS API for JavaScript 4，那么理想情况下，所有其他包都应该放在同一个文件夹中。

```
1   src
2     ├── app
3     ├── dgrid
4     ├── dijit
5     ├── dojo
6     ├── dojox
7     ├── dstore
8     ├── moment
9     ├── esri
```

这让你可以轻松地组织代码，并且也能让 Dojo 构建工具知道你正在使用哪些包。

有时你可能需要使用基于通用模块定义（UMD）构建的库。在这种情况下，Dojo 构建系统会识别出你正在尝试加载一个看起来像 AMD 模块但又不完全是 AMD 模块的东西。你可以在构建配置文件的 `packages` 属性中告知 Dojo 构建系统如何加载这个包。

以 API 中使用的 `moment` 库为例。如果你打算使用通过 Bower 发布的 API 进行自定义本地构建，你需要对构建配置文件进行如下小调整：

```javascript
1   packages: [
2     'app',
3     'dijit',
4     'dojo',
5     'dojox',
6     'dstore',
7     'dgrid',
8     'esri', {
9       name: 'moment',
10       location: 'moment',
11       main: 'moment',
12       trees: [
13         // 忽略 .hidden、tests、min、src 和 templates 等目录
14         [".", ".", /(\/\.)|(~$)|(test|txt|src|min|templates)/]
15       ],
16       resourceTags: {
17         amd: function(filename, mid){
18           return /\.js$/.test(filename);
19         }
20       }
21     }
22   ]
```

在这个例子中，你通过定义 `name`、`location` 和 `main` 属性来定义一个 `moment` 包。这与之前定义 `app` 包的方式类似。不过，对于这个包，你还定义了一个 `resourceTags` 属性。该属性通过正则表达式告诉 Dojo 构建系统，所有 `.js` 文件都是 AMD 模块。你还添加了一个 `trees` 属性，它告诉 Dojo 构建系统忽略隐藏文件、测试目录、压缩文件以及你不想让 Dojo 构建系统尝试构建的其他文件。这些就是为 UMD 文件定义包所需的步骤，以便它们能够被正确地加载到 Dojo 构建系统中。

通常，这些属性是在包文件夹内的一个 JavaScript 文件中定义的。这个文件可以任意命名，但通常会被命名为类似 `app.profile.js` 的形式，内容如下：

```javascript
1   var profile = (function () {
2     return {
3       resourceTags: {
4         amd: function (filename, mid) {
5           return /\.js$/.test(filename);
6         }
7       }
8     };
9   })();
```

你还可以在此处添加其他选项，这些选项可以在文档中找到。

你可以通过 `package.json` 文件告知 Dojo 构建系统如何找到你的包配置文件。

```json
1   {
2       "name": "app",
3       "description": "My Application Package.",
4       "version": "1.0",
5       "dojoBuild": "app.profile.js"
6   }
```

Dojo 构建系统会查找 `package.json` 文件中的 `dojoBuild` 属性，以根据定义的设置来确定如何加载你的 AMD 包。

## 总结

在这一简短章节中，我介绍了如何设置一个基本应用程序以及如何定义 `dojoConfig` 对象。你了解了 AMD 加载器，以及如何定义 AMD 包，以及如何使用 Dojo 构建系统创建 API 的自定义构建。在深入理解包的概念之后，让我们继续学习 ArcGIS API 4 for JavaScript 的一些概念。

## 参考资料

- [https://github.com/amdjs/amdjs-api/blob/master/AMD.md](https://github.com/amdjs/amdjs-api/blob/master/AMD.md)
- [https://babeljs.io/](https://babeljs.io/)
- [http://jsmodules.io/](http://jsmodules.io/)
- [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)
- [https://dojotoolkit.org/documentation/tutorials/1.10/modules/index.html](https://dojotoolkit.org/documentation/tutorials/1.10/modules/index.html)
- [https://en.wikipedia.org/wiki/Java_package](https://en.wikipedia.org/wiki/Java_package)
- [http://bower.io/](http://bower.io/)
- [https://dojotoolkit.org/documentation/tutorials/1.10/build/](https://dojotoolkit.org/documentation/tutorials/1.10/build/)