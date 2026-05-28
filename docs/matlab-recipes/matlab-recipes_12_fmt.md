# 格式化 `axis` `off`

```
posText = [pos(1)+pf(3)-25 pos(2)+0.5*pf(4)-40 20 80];
axes('Parent', fig, 'box', 'on', 'units', 'pixels', 'outerposition', posText);
t2 = text(0,1,string,'fontsize',20,'color',[0.8 0.8 0.8]);
set(t2,'rotation',270,'edgecolor',[0.8 0.8 0.8],'linewidth',2)
axis off
% 恢复图形中的当前坐标轴
if ~isempty(aX)
    set(fig,'CurrentAxes',aX);
end
```

如果你想要更炫酷的效果，可以在图形前方绘制对象并为其设置透明度，但这些对象必须是填充或面片对象；文本无法设置透明度。

## 总结

本章回顾了 MATLAB 可视化的关键特性，从基础绘图到 3D 可视化，包括对象和光照。我们演示了如何获取图形和坐标轴的句柄、以编程方式设置属性，以及使用图形交互工具。创建实用的可视化程序是任何工具箱的关键组成部分。MATLAB 提供了出色的数据管理程序，包括能够管理大型数据网格的程序，以及多种着色选项。表 3-1 列出了本章中开发的代码。

### 表 3-1. 章节代码清单

| 文件 | 描述 |
| --- | --- |
| `AnnotatePlot` | 沿曲线等间距添加文本标注。 |
| `Boxpatch` | 使用面片生成立方体。 |
| `ColorDistribution` | 演示一组线条的颜色分布。 |
| `DraftMark` | 为图形添加草稿标记。 |
| `GridVisualization` | 在二维和三维网格上可视化数据。 |
| `PatchWithCamera` | 使用面片生成两个立方体，并将相机对准场景。 |
| `PatchWithLighting` | 为立方体面片添加光照。 |
| `PlotPage` | 在一个图形中创建包含多个自定义绘图的绘图页面。 |
| `PlottingWithDates` | 使用月份作为 x 轴标签进行绘图。 |
| `QuadPlot` | 使用子图创建四象限绘图页面。 |
| `ReadImage` | 用多种方式在图形中绘制 JPEG 图像。 |
| `SphereLighting` | 创建并点亮一个球体。 |
| `Watermark` | 为图形添加水印。 |