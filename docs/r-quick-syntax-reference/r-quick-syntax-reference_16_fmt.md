# 6. 打包函数

R 拥有超过 10，000 个包，其中大部分包含函数。函数是 R 的核心，赋予了 R 极大的灵活性。函数是 R 对象，其`mode`和`class`均为`function`。打包函数是作为 R 包一部分创建的函数。在计算机上，包存储在库中，并被安装到库中以供使用。

## 库

初始安装 R 时，以下包也会随同安装到硬盘的某个文件夹中：`base`、`boot`、`class`、`cluster`、`codetools`、`compiler`、`datasets`、`foreign`、`graphics`、`grDevices`、`grid`、`KernSmooth`、`lattice`、`MASS`、`Matrix`、`methods`、`mgcv`、`nlme`、`nnet`、`parallel`、`rpart`、`spatial`、`splines`、`stats`、`stats4`、`survival`、`tcltk`、`tools`和`utils`。

在 Windows 系统中，初始安装后安装的任何包都可以安装到不同的库（另一个文件夹）中。该文件夹在 R 安装时创建。在 OS X 系统中，所有已安装的包都位于同一个文件夹和库中。在 Linux 系统中，初始安装后安装的任何包都会安装到不同的库（另一个文件夹）中。该文件夹在安装第一个额外包时创建。

要查看已安装包的列表（包含每个包的描述和包文件夹名称），可在 R 提示符下输入`library(lib.loc = .Library)`。运行不带参数的`library()`函数会列出库中的包及其描述。要查看关于包的更多详细信息，可在 R 提示符下输入`installed.packages()`。在 R Studio 中，已安装的包会列在右下角窗口的“包”选项卡下。

有些 R 函数的运行需要依赖其他 R 函数。当 R 运行时，只有那些已从库加载到 R 中的包才是程序可访问的。如果尝试运行某个函数但必需的包尚未加载，R 会报错。错误信息中会包含缺失包的名称。如果某个包存在于计算机的某个库中，可以通过在 R 提示符下输入`library('package name')`来加载（使其可访问）该包，其中`'package name'`是包名。或者在 R Studio 中，在“包”选项卡下勾选该包左侧的复选框来加载它。

如果包不在任何一个库中，安装新包的过程很简单（参见第[1]章）。一旦安装完成，便可使用`library()`函数或 R Studio 中的“包”选项卡加载该包。（如果在函数内部调用，应使用`require()`代替`library()`。参见`library()`帮助页面。）在任何时候，在 R 提示符下输入`search()`都会给出当前工作空间中已加载包的列表。在 R Studio 中，已加载的包会显示勾选标记。

要查看某个包中的函数（和数据集），可在 R 提示符下输入`help(package='package name')`，其中`'package name'`是包名。请注意，必须已安装该包，`help(package='package name')`才能返回包的内容。在 R Studio 中，在“帮助”选项卡下输入包名即可访问该包中的所有对象。包中的某些文件可能是数据集，但对于大多数包而言，文件通常是函数。