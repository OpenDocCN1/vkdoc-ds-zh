# `ggplot(lm(mpg ˜ hp * qsec, data = mtcars), aes(hp, mpg, linetype = factor(qsec)),` 中出现错误：

## 无法**找到函数** "ggplot"

## 执行已终止

## * 检查 'tests' 中未声明的依赖项 ... 正常

## * 检查测试 ...

## 正在运行 'testthat.R'

## 正常

## * 检查临时目录中的残余文件 ... 正常

## * 完成

## 状态：1 个错误，1 个警告，1 个提示

## 详情请参见 'C:/.../Temp/.../AdvancedRPkg.Rcheck/00check.log'。

## -- R CMD check 结果 ------------------ AdvancedRPkg 0.0.0.9000 ----
## 持续时间：28 秒

## > 检查示例 ... 错误
## 'AdvancedRPkg-Ex.R' 中的示例运行失败
## **错误**最可能发生在：
## > base::assign(".ptime", proc.time(), pos = "CheckExEnv")
## > ### 名称: ggplot.lm
## > ### 标题: 绘制线性模型的**方法**
## > ### 别名: ggplot.lm
## >
## > ### **示例**
## >
## > ggplot(
## + lm(mpg ˜ hp * qsec, data = mtcars),
## + aes(hp, mpg, linetype = factor(qsec)),
## + vars = list(
## + hp = min(mtcars$hp):max(mtcars$hp),
## + qsec = round(mean(mtcars$qsec) + c(-1, 1) * sd(mtcars$qsec)), 1)) +
## + geom_ribbon(aes(ymin = LL, ymax = UL), alpha = .2) +
## + geom_line() +
## + theme_bw()
## 在 ggplot(lm(mpg ˜ hp * qsec, data = mtcars), aes(hp, mpg, linetype = factor(qsec)), 中出现错误：
## 无法**找到函数** "ggplot"
## 执行已终止
##
## > 检查 DESCRIPTION 元信息 ... 警告
## 依赖于 R **版本** '3.6.3' 但未指定补丁级别 0
##
## > 检查 R 代码中可能存在的问题 ... 提示
## ggplot.lm: 未找到 'formula' 的可见**全局函数**定义
## ggplot.lm: 未找到 'predict' 的可见**全局函数**定义
## ggplot.lm: 未找到变量 'fit' 的可见**全局**绑定
## ggplot.lm: 未找到 'qnorm' 的可见**全局函数**定义
## ggplot.lm: 未找到变量 'se.fit' 的可见**全局**绑定
## meanPlot: 未找到 'plot' 的可见**全局函数**定义
## meanPlot: 未找到 'points' 的可见**全局函数**定义
## show,textplot: 未找到 'head' 的可见**全局函数**定义
## 未定义的**全局**函数或变量：
## fit formula head **plot** points predict qnorm se.fit
## 考虑将以下内容添加到您的 NAMESPACE 文件中：
## importFrom("graphics", " **plot**", "points")
## importFrom("stats", "formula", "predict", "qnorm")
## importFrom("utils", "head")
##
## 1 个**错误** x | 1 个警告 x | 1 个提示 x

