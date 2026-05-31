# 能力

`Competencies`  
`Competencies`  
`use-competencies-single`

这将使“能力”字段变为一个下拉列表，仅允许选择一个值，如图 1-29 所示。

![../images/510611_1_En_1_Chapter/510611_1_En_1_Fig29_HTML.jpg](img/510611_1_En_1_Fig29_HTML.jpg)

*图 1-29 — 为发展目标添加单项能力*

在此字段中也可以使用行为，而非能力。然而，要实现此功能，您的能力必须包含行为。如果您使用的是 SuccessFactors 2.1 能力库，则其中已包含行为。您也可以通过“管理职位档案内容”向能力中添加行为。若要使用行为，请按如下方式设置 `field-format` 标签：

```
use-behaviors
```

使用此标签时，系统仅向用户显示包含行为的能力。用户在创建发展目标时，可以在能力下选择具体的行为。随后，能力字段将列出所选的行为。请注意，单项选择选项不适用于行为。

包含这些标签的字段定义如下代码片段所示：

```
Competencies
Competencies
Competencies
use-competencies-single
Competencies
```

如果在 Provisioning 中启用了写作助手，并且能力库包含与能力相关的简介提示，则您可以对任何文本区字段使用辅导顾问。辅导顾问的内容基于能力简介提示，由 `"field-show-coaching-advisor" = "true"` 定义。

以下是一个代码片段示例：

```
Measure of Success
Measure of Success
Measure of Success
Measure of Success
```

以下是通过在线编辑器中的“管理模板”添加的自定义字段示例：

```
Recommended Employee Actions
Recommended Employee Actions
Recommended Employee Actions
Recommended Employee Actions
```

需要说明的是，与目标计划类似，无法在表格字段中添加自定义字段。至此，我们已经了解了发展目标计划中可用的字段。接下来，我们将讨论内容权限。

