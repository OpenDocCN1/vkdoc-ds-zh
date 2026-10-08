# 1. 下载 R 语言并设置文件系统

使用`R`和`R Studio`的第一步是从互联网下载这两个程序。`R`必须首先下载。`R`和`R Studio`可针对现代操作系统`Windows`、`Mac OS X`和`Linux`进行下载。在本章中，你将学习如何下载和安装`R`及其30个基础包和`R Studio`，以及如何安装其他包和更新`R`。在`R Studio`中更新包的内容将在第2章中介绍。你还将学习如何在计算机文件系统中的各个文件夹内使用`R`。

## 下载 R 和 R Studio

你可以从`Comprehensive R Archive Network (CRAN)`网站下载`R`。`CRAN`会不时更新安装流程；但本书中的说明针对的是出版时的当前步骤。如果流程有变，`CRAN`会在其网站上提供说明。

通过访问网站[`https://www.r-project.org/`](https://www.r-project.org/)开始下载过程。在该网站上，点击链接以选择`CRAN`镜像。选择一个离你较近的镜像。点击镜像链接后打开的窗口顶部会列出`Windows`、`Mac OS X`和`Linux`系统当前版本的链接。选择适合你的版本。

### Windows

在点击`Windows`链接后打开的页面上，选择`base`链接（即顶部链接）。在下一个窗口中，点击给定`Windows`版本的`download`链接*。*（当前，该链接为`Download R 3.5.1 for Windows`。）如果计算机上尚未安装`R`，下载器将在`Documents`文件夹中创建一个默认文件夹来存放`R`文件。除非有理由更改文件夹名称或位置，否则接受默认设置。`R`将开始下载。

程序下载完成后，在你的文件系统中找到下载的文件。下载的文件会被放在`C://Users/User_folder/Downloads`目录下，其中`User_folder`是用户的文件夹，除非在之前的安装中指定了其他文件夹。点击下载的文件，它是一个`.exe`安装文件（当前是`R-3.5.1-win.exe`）。可能会弹出一个关于程序安全性的提示。该安装程序是安全的，因此请运行它。

安装向导将打开。安装过程会逐步经过几个页面。第一页，阅读`GNU`通用公共许可证；然后点击`Next`。对于其余页面，接受每页的默认设置即可，因此请在每个页面点击`Next`。

在附加选项页面，点击`Next`，程序将开始安装。安装完成后，点击`Finish`完成安装。至此，程序及30个基础包已安装完毕。计算机桌面上会出现一个`R`图标，在`Windows 10`中，还会出现在开始菜单中。要运行`R`，点击该图标即可。

### Mac OS X

在通过`Mac OS X`链接打开的页面上，首先阅读`R for Mac OS X`下的部分。`R`项目建议检查文件是否存在病毒及其他问题。

在`Latest release:`下方，即可选择当前版本的包。选择当前版本（`.pkg`链接，当前为`R-3.5.1.pkg`）将下载该包。下载完成后，在`Finder`中打开用户名下的下载文件夹。

在下载文件夹中，选择`R`版本的`.pkg`文件。打开该版本文件将会启动安装器。安装器打开后，点击`Continue`进入下一页面。阅读`CRAN`的消息；然后点击`Continue`。再次阅读`CRAN`的消息；然后点击`Continue`。

在下一页，你将看到许可协议。阅读许可协议后，点击`Agree`以下载`R`。在下一页，选择两个选项之一；然后点击`Continue.`（`Continue`按钮在选择之后才会亮起。）

在下一页，选择`Install`。安装程序会要求输入密码。输入密码后，安装将开始。安装完成后，点击`Close`。接下来，你可以选择保留安装或丢弃它。如果保留`R`，`R`将出现在应用程序文件夹和启动台中，并且30个基础包将被加载。通过打开启动台并选择`R`图标，或点击应用程序文件夹中的`R`，即可启动`R`。

### Linux

在`CRAN`站点，`CRAN`为`Linux`发行版`Debian`、`openSuse`和`Ubuntu`提供了`R`的源代码。`Debian`和`Ubuntu`发行版已于2018年更新。`openSuse`发行版则始于2012年。

开发者指出，对于大多数`Linux`发行版，`R`可通过包管理系统获取。请在`GNU R`下查找。如果使用包管理系统无法获得命令行版本的`R`，则可以直接从终端安装`R`。在[`http://cran.r-project.org/bin/linux/distribution`](http://cran.r-project.org/bin/linux/distribution)（其中`distribution`为`debian`、`suse`或`ubuntu`），你可以找到从终端命令提示符安装`R`的说明。

`CRAN`站点上关于`Red Hat`的链接无效。

### R Studio

在`R Studio`站点上，`R studio`免费提供`R Studio`的源代码，也提供收费版本。`R Studio`适用于`Windows`、`Mac OS X`以及`Linux`发行版`Debian`、`Red Hat`、`openSUSE`、`Ubuntu`和`Fedora`。要下载免费版的`R Studio`，请访问[`https://www.rstudio.com/products/rstudio/download/`](https://www.rstudio.com/products/rstudio/download/)并转到标题`Installers for Supported Platforms`。点击适合你操作系统的链接，下载并运行安装程序。按照适用于你操作系统的安装程序指示进行操作。这些指示与`R`的安装指示类似。

在`Mac OS X`系统上，每次计算机启动时都必须打开`Finder`中的`RStudio-1.1.456.dmg`文件，才能使`R Studio`在系统中可用。

对于`Linux`，`R Studio`可能已存在于包管理器中。请在`R Studio`下进行搜索。

## 安装和更新包

初始安装时，默认情况下`R`会附带30个包。用户通常希望利用`R`中可用的许多其他包的功能。安装和更新包的操作很直接。

对于任何操作系统，在`R`中，如果知道包的名称，在`R`命令提示符下输入

```
install.packages("package name")
```

其中`package name`是包的名称，即可安装该包。要更新包，在`R`命令提示符下输入

```
update.packages()
```

将找到可更新的包并进行更新。要查看计算机上已安装了哪些包，在`R`提示符下输入

```
installed.packages()
```

如果不知道包的名称（即使是已知名称），使用`Windows`和`Mac OS X`操作系统的安装器也很容易。有关`Linux`的说明，可以在`CRAN`网站[`http://cran.r-project.org`](http://cran.r-project.org)上找到。在这里你也可以找到适用于`Windows`和`Mac OS X`的说明。

在`R Studio`中安装和更新包要容易得多。如何在第2章描述`R Studio`窗口时给出。

### Windows

要安装一个不使用命令行的`R`包，首先打开`R`。在屏幕顶部的菜单栏中，选择`Packages`。此时会下拉一个菜单，选择`Install package(s)...`。此时会出现`CRAN`镜像窗口或`Packages`窗口。如果出现的是`CRAN`镜像窗口，请选择一个就近的镜像并点击`OK`，这将打开`Packages`窗口。

`Packages`窗口包含所有可用包的列表。向下滚动列表找到你想要安装的包并选中它们。点击`OK`开始安装。安装过程中，安装步骤会滚动显示在`R`控制台中。当`R`提示符返回到屏幕时，安装即完成。

要更新不使用命令行的包，请在菜单栏中选择`Packages`，然后选择`Update packages...`。此时会打开待更新的`Packages`窗口，其中包含所有已安装且有更新的包列表。如果没有更新，窗口将为空。选择要更新的包并点击`OK`按钮。如果弹出关于使用个人库的询问，选择`Yes`。包将开始更新。当`R`提示符返回到屏幕时，更新即完成。

### Mac OS X

# 在Mac OS X中安装R包

要在 Mac OS X 中安装包，首先打开 R。在屏幕顶部的下拉菜单栏中，选择`Packages & Data`。从下拉菜单中选择`Package Installer`，这会打开 R Package Installer。点击`Get List`获取完整的包列表，或使用`Package Search`选项搜索包。在任一选项下，从列表中选择要安装的包。

在包列表下方，有存放包的位置选项。将鼠标悬停在位置选项上可获取更多信息。通常，前两个选项之一是正确的。位置选项的右侧是`Install Selected`和`Update All`按钮。在点击`Install Selected`之前，请勾选`Install Dependencies`框，以确保安装所有必要的依赖包。点击`Install Selected`开始安装过程。所选包将被安装。

要更新包，请从屏幕顶部的菜单栏中选择`Packages & Data`。从下拉菜单中选择`Package Installer`，这会打开 R Package Installer。在安装窗口的右下角，选择`Update All`并按照指示操作。

# 更新 R

由于 CRAN 不提供 R 的自动更新，你必须手动更新。Windows 和 Mac OS X 的更新过程很简单。对于 Linux 发行版 Debian、Suse 和 Ubuntu，可以在[`http://cran.r-project/bin/linux/distribution`](http://cran.r-project/bin/linux/distribution)的`ReadMe`文件中找到说明，其中*distribution*指的是 Debian、Suse 或 Ubuntu。

## Windows

在 Windows 中更新 R 的第一步是打开 R，如果尚未安装`installr`包，请先安装它。接下来，使用`library`函数访问`installr`。在命令提示符下输入：

```
library(installr)
```

然后按下`enter`键。接着，要更新 R，请在命令提示符下输入：

```
updateR()
```

并按下`enter`键。R 将执行更新，或者显示信息表明程序已是最新，并返回`False`。

一旦`installr`被安装，无需再次安装`installr`。但每次运行 R 时都需要加载该库。

## Mac OS X

在 Mac OS X 中更新 R 的第一步是打开 R，并从页面顶部的下拉菜单栏中选择`R`。要运行更新程序，请在`R`下的下拉菜单中选择`Check for R Updates`，然后按照指示操作。

# 在独立文件夹中使用 R

在 Windows、Mac OS X 和 Linux 中，可以在不同的文件夹中维护独立的 R 工作空间镜像。这一特性对于在不同的项目中使用 R 非常方便。虽然在不同操作系统中打开特定文件夹中的 R 过程有所不同，但进入文件夹后，保存工作空间镜像的操作是直接了当的。当关闭 R 会话时，程序会询问用户是否要保存工作空间镜像。如果选择`Yes`，则`.RData`和`.Rhistory`（Mac OS X 中为`.Rapp.history`）文件将被保存到当前目录中。（对于 Mac OS X，这些文件是隐藏的，但它们确实存在。）

`.RData`文件包含会话开始时 R 中已有的对象，加上会话期间添加的对象，再减去会话期间删除的对象。`.Rhistory`（Mac OS X 中为`.Rapp.history`）文件包含在 R 控制台中输入的行的历史记录。在 Windows 中，默认保存最后 512 行。对于 Mac OS X 和 Linux，默认保存 250 行。如果历史记录被保存，这些行的访问权限将在会话之间延续。

## Windows

要在文件夹中初始设置 R，请在桌面上打开 R（点击桌面上的`R`图标，或在程序列表中点击`R`，或者在 Windows 10 的“开始”菜单中点击`R`）。选择屏幕顶部菜单栏中的`File`。从下拉菜单中选择`Change dir...`。此时会打开`Browse to folder`窗口。导航到你选择的文件夹。

退出 R 时，保存工作空间镜像，R 将在该文件夹中创建`.RData`和`.Rhistory`文件。`.RData`文件会有一个蓝色的`R`图标关联。将来，进入该文件夹并点击`R`图标即可打开 R，并且该文件夹中保存的历史记录和对象将会出现。

作为初始设置的一个注意事项：在更改文件夹时，桌面 R 中的任何对象仍然会保留在 R 中。你可以轻松移除这些对象。在命令提示符下输入`rm(list=ls())`，即可从文件夹中移除所有对象。

## Mac OS X

要在 Mac OS X 的不同文件夹中工作，有两种方法：拖放或使用终端。对于位于`Finder`的`Applications`菜单中的 R，如果 R 未打开，将`Finder`中`Documents`菜单里的文件夹拖到`R`应用程序上，即可在该文件夹中打开 R，并使用该文件夹的`.RData`和`.Rapp.history`文件。（可以将`R`应用程序的图标放到`Documents`文件夹中，以便更轻松地拖放。）

要使用终端打开 R，请打开终端（位于`Finder`的`Applications/Utilities`下），并输入：

```
open -a R folder
```

其中*folder*是文件夹的位置。请确保在名称中包含 Documents 文件夹，并对名称使用引号。R 将在该文件夹中打开，并使用该文件夹的`.RData`和`.Rapp.history`文件。

## Linux

要在 Linux 的某个文件夹中打开 R，请将目录切换至该文件夹，并在命令提示符下输入`R`。

## R Studio 中的项目

处理独立项目的另一种方法是在 R Studio 中打开新项目。每个项目都有其唯一的名称，并且可以使用 R Studio 中的菜单创建。项目可以通过点击其保存目录中的名称来访问。项目文件的扩展名为`.Rproj`。
