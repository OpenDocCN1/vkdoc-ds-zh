# 第 13 章 每一个云都有闪亮的衬里

基本上就是这样。当我们的服务运行时，它会通知用户界面函数`plotOutput("piePlot")`更新图像。这个函数位于`mainPanel()`区域中。在这个简单的 Shiny 模型下，侧边栏用于接收用户输入，服务器端由 R 在后台处理输入，而主面板则是用户查看处理后的输出。仅此一次，我们展示了 Shiny 为我们创建的完整 HTML 代码，如图 13-1 所示。

```html
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
  <script type="application/shiny-singletons"></script>
  <script type="application/html-dependencies">
    json2[2014.02.04];jquery[3.4.1];shiny[1.4.0.2];bootstrap[3.4.1]
  </script>
  <script src="shared/json2-min.js"></script>
  <script src="shared/jquery.min.js"></script>
  <link href="shared/shiny.css" rel="stylesheet" />
  <script src="shared/shiny.min.js"></script>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <link href="shared/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
  <script src="shared/bootstrap/js/bootstrap.min.js"></script>
  <script src="shared/bootstrap/shim/html5shiv.min.js"></script>
  <script src="shared/bootstrap/shim/respond.min.js"></script>
  <title>用户控制的图表</title>
</head>
<body>
  <div class="container-fluid">
    <h2>用户控制的图表</h2>
    <div class="row">
      <div class="col-sm-4">
        <form class="well">
          <div class="form-group shiny-input-container">
            <label class="control-label" for="pie">饼图百分比</label>
            <input id="pie" type="number" class="form-control" value="50" min="0" max="100"/>
          </div>
          <div class="form-group shiny-input-container">
            <label class="control-label" for="pietext">文本输入</label>
            <input id="pietext" type="text" class="form-control" value="默认标题" placeholder="在此输入您的标题"/>
          </div>
          <div class="form-group shiny-input-container">
            <div class="checkbox">
              <label>
                <input id="pieChoice" type="checkbox"/>
                <span>我想要一个饼图。</span>
              </label>
            </div>
          </div>
        </form>
      </div>
      <div class="col-sm-8">
        <div id="piePlot" class="shiny-plot-output" style="width: 100% ; height: 400px"></div>
      </div>
    </div>
  </div>
</body>
</html>
```

现在，请回去仔细查看我们在这部分展示的第一个完整 Shiny 应用的代码块，并意识到这些代码创建了上述所有 HTML 和 JavaScript。在这个例子中，我们的 R 代码虽然并不特别令人印象深刻，但原则仍然是重要的。用户现在可以与数据交互，想象更有趣的应用并非不可能。

在接下来的几节中，我们将介绍一些其他有趣且有用的 Shiny 输入函数以及一些基本的响应式函数。最后，我们将所有内容上传到云端，以便您可以在网络上实时分享它们。

