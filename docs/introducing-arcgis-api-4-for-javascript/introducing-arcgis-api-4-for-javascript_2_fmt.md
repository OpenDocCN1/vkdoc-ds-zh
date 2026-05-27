# ArcGIS API for JavaScript

## 引言

ArcGIS API for JavaScript 多年来不断发展，纳入了能够利用 ArcGIS Server 和 ArcGIS Online 更新的新功能。向 API 第 4 版的迁移是自其首次发布以来在技术和功能上最大的飞跃之一。此版本的 API 为开发者引入了一些新概念，并使开发者能够更轻松地将 3D 场景添加到其应用程序中。

### 如何获取 ArcGIS API

您可以在 [`https://developers.arcgis.com/javascript/latest`](https://developers.arcgis.com/javascript/latest) 找到有关该 API 的更多信息。

### 关键概念

ArcGIS API 4 for JavaScript 继承了早期版本 API 中的一些概念，并引入了一些新概念。这些内容将在本书中更详细地讨论，但此处简要说明如下：

*   **异步模块定义 (AMD)**：这是 Dojo 使用的模块系统，非常适合大型应用程序开发。
*   **地图、图层和视图**：地图及其图层和视图关系是关键。地图是一个数据源，可以链接到多个视图。
*   **访问器**：访问器是 API 的强大实现，提供了丰富的功能集。
*   **集合**：这些是类似数组的数据存储，也可以发出更改事件。
*   **Promise**：该 API 一直使用 Promise 进行异步操作，但在第 4 版中它们扮演着更重要的角色。
*   **微件与 UI**：API 中地图的微件和 UI 已针对第 4 版进行了完全重写和重新架构。
*   **Web 场景与本地场景**：场景是 JavaScript API 中一个令人兴奋的新增功能，因为它们将 3D 能力交到了开发者手中。

## 你需要了解的内容

我认为可以公平地说，如果你正在阅读本书，你应该具备一些基本的编程经验。如果你有 JavaScript 经验，那就更好了，因为本书不会涵盖 JavaScript 基础知识。你可以在网上找到大量资源来学习 JavaScript，我建议你这样做。

你不需要了解地理信息系统 (GIS)。我假设你知道地图是什么，仅此而已。

### AMD

就本书而言，你无需过多担心 ArcGIS API for JavaScript 中使用的 AMD。本书中的所有示例都将使用 `ES2015`，¹ 你可以使用 `Babel`² 和 `GruntJS`³ 将它们编译为 AMD。

你真正需要了解的是，你为 ArcGIS JavaScript API 应用程序编写的代码需要编译为 AMD，才能使用 Dojo 加载器。⁴

让我们开始吧！

¹ [`https://babeljs.io/docs/learn-es2015/`](https://babeljs.io/docs/learn-es2015/)
² [`https://babeljs.io/`](https://babeljs.io/)
³ [`http://gruntjs.com/`](http://gruntjs.com/)
⁴ [`http://dojotoolkit.org/reference-guide/1.10/loader/amd.html#loader-amd`](https://dojotoolkit.org/documentation/tutorials/1.10/build/)