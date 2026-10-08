# 7. 示例

用户驱动的应用程序只能建立在完全可移动性的基础上，因此前几章主要讨论的是设计各种完全不同的可移动对象。我们循序渐进，从最常用形状（矩形、圆形、多边形、环形等）的简单对象，过渡到可包含任意类型对象的复杂对象和组。我认为前几章的示例已经充分展示了如何将截然不同的对象转变为可移动对象，以及如何在新类型的应用程序中使用它们。几乎所有之前的示例都以展示所提算法的最重要特性及其实现方式为目的而开发。

本章不再讨论可移动性尚未涉及的方面，而是聚焦于具有明确用途的真实用户驱动应用程序的整体设计。以下示例最初是作为独立应用程序开发的，后来才被收录为本书配套程序的一部分。

## 计算器

我只有在极少数需要计算两个大数相除，且懒得用纸笔计算时，才会使用标准的 Windows 计算器。也许是因为我很少用这个程序，但每次使用时，我都很难找到印有正确数字的按钮，因为这些按钮的位置绝对不像我会作为设计师摆放的那样。我从小（并且至今仍）被教导从左到右、从上到下阅读和书写，考虑到从 0 到 9 的数字顺序几个世纪以来从未改变，而计算器的设计者也受教于同样的规则，我实在搞不懂为什么 Windows 计算器中的相同数字却是从底部开始向上排列的（图 7-1a）。这种布局背后肯定有某种伟大的想法，但我无法理解。每次使用这个程序，我都要花额外的时间寻找需要的按键。最终找到它们，得出结果，然后就会忘记这次搜索，直到下次再犯。

多年前，在我将普通控件纳入我的可移动性算法后，我一直在思考一个能展示这部分算法的好例子。这时我想到了计算器程序，它是一个绝佳的演示候选，因为它完全由控件构建，并且每个人都熟悉它。我的第一个计算器版本功能有限，但重现了原始程序的界面，并完美展示了所有元素的可移动性（图 7-1）。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig2_HTML.jpg](img/501148_1_En_7_Fig2_HTML.jpg)

图 7-1b

可移动性允许每位用户组织自己偏好的视图

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig1_HTML.jpg](img/501148_1_En_7_Fig1_HTML.jpg)

图 7-1a

原始计算器程序的默认视图

我本无意再用这个计算器做其他事，但几年后，在与一位同事交谈时，我听到了抱怨：这位同事视力不佳，使用标准计算器程序时遇到很大麻烦，因为即便数位专家共同努力，也未能找到任何方法来增大该应用程序使用的字体。于是，我坐下来开发了另一个同事能用的计算器。由于不是为自己开发，这个程序必须能作为一个具有运算和功能的普通计算器运行。当然，改变字体并非我在该程序中允许用户做的唯一事情；它是按照用户驱动应用程序的所有规则来设计的。

我的第二个计算器版本外观与图 7-2 所示相同；只是简短信息的文本略有不同，因为我的第二个计算器版本仍然使用普通控件设计。又过了几年，在下一个版本的计算器中，不再保留任何普通控件。图 7-2 展示的就是这个程序，也是我现在想讨论的程序。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig3_HTML.jpg](img/501148_1_En_7_Fig3_HTML.jpg)

图 7-2

最新计算器的默认视图

*   文件：`Form_Calculator.cs`

*   菜单位置：Applications ➤ Calculator

我无意设计一个能与 Windows 同类程序竞争的程序。这个新计算器是为特定个人使用的；此人从未使用过原始程序的存储操作，所以我的程序中没有用于存储操作的按钮。

在进一步说明之前，有一点需要注意：在下文中，术语`所有元素`指的是除信息元素之外的所有元素。

### 排版后内容

新应用程序的所有元素中，除了两个之外，其余都是 `Button_Text` 类的按钮。这些按钮被分为四组；在图 7-2 中，它们通过不同的文本颜色被轻易区分。

*   带有数字的按钮文本显示为蓝色。从数学角度来看，小数点和符号绝对不是数字，但它们在书写数字时被用作记法的一部分，因此在这个程序中，这两个按钮被归入`数字`组。

```
Button_Text btn_0, btn_1, btn_2, btn_3, btn_4, btn_5, btn_6, btn_7, btn_8, btn_9, btnSign, btnDot;                   // 数字
```

*   带有运算符号的按钮显示为红色。

```
Button_Text btnPlus, btnMinus, btnMultiply, btnDivide, btnDegree, btnEqual;
```

*   `函数`按钮的文本显示为紫色。我可以很容易地添加更多函数，但为我设计这个程序的人说这组函数已经够用了。你可以在此程序中添加更多函数，但请仔细检查代码，并在使用此组的所有位置进行修改。

```
Button_Text btnLn, btnLog, btnExp, btnSin, btnCos,
btnTg, btnInverse, btnSqrt;
```

*   三个绿色文本的按钮被归为一组`清除器`，因为它们用于删除部分或全部已准备的数据。

```
Button_Text btnBackspace, btnClearAll, btnClearEdit;
```

为了更好地展示结果，这里有两个辅助区域；一个用于显示当前数值，另一个用于显示整个表达式。

```
Label_GR labelValue, labelExpression;
```

计算器旨在实现其主要目的，但这是一个用户驱动的应用程序，具备移动、调整大小、调优、保存和恢复等所有功能。让我们从移动开始。

移动可以应用于单个对象、三个预定义组（数字、运算和函数）的元素以及临时组元素。元素的单独移动以标准方式组织：用左键按下它，然后移动到另一个位置。

数字、运算或函数组（图 7-3）可以通过菜单命令来组织；我稍后会介绍菜单。因此，组织好的“标准”组只包含该预定义集合中的元素，不包含其他任何元素，即使当调用该组时，有其它对象恰好位于框架内。因此，函数组始终恰好包含八个具有函数名称的按钮；图 7-3 中框架内的另外两个元素（一个带有数字，另一个带有等号）不属于此组。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig4_HTML.jpg](img/501148_1_En_7_Fig4_HTML.jpg)

图 7-3

函数组包含八个按钮

临时组可以包含任意元素集合。在任何空白位置按下左键，并在不释放按钮的情况下移动鼠标光标。在此移动过程中，您将看到一个矩形框架；该矩形的两个对角分别是初始按下点和当前鼠标位置。如果在释放鼠标时，该矩形内包含多于一个对象，则会组织一个由这些圆角对象组成的临时组。例如，图 7-4 中的临时组包含数字组中的八个按钮、运算组中的三个按钮、清除器组中的一个按钮以及一个标签。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig5_HTML.jpg](img/501148_1_En_7_Fig5_HTML.jpg)

图 7-4

临时组

任何组，无论是预定义组还是临时组，都属于 `Group_ArbitraryElements` 类，因此可以通过其内部的任何点进行移动。框架会根据内部元素的任何变化调整其大小和位置。我想说的是，在计算器中组织的任何组都类似于临时组，仅用于移动某些元素集合的目的；这就是为什么没有修改组的命令。

在 `Form_Calculator.cs` 文件中（图 7-2），排除信息区域后，共有 31 个元素。这 31 个对象被分配到两个 `List` 中。这些 `List` 的名称清楚地表明，其中一个包含当前存在的组中的所有元素；其余元素则进入另一个 `List`。

```
List elemsInGroup = new List ();
List elemsSingle = new List ();
```

元素在两个 `List` 之间的分配，要么在调用“设置”菜单中适当的命令时（图 7-5）进行，要么在视图内绘制矩形后释放鼠标时进行。在这两种情况下，都会调用 `SetGroup()` 方法，但参数不同。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig6_HTML.jpg](img/501148_1_En_7_Fig6_HTML.jpg)

图 7-5

“设置”菜单

当调用菜单命令并命令标记某个预定义组时，`SetGroup()` 方法会获取一个参数，即要框选的特定元素类型。作为初步步骤，所有元素会按预定顺序被包含到 `elemsSingle` 中，同时清空 `elemsInGroup`。

## 元素默认排序

```csharp
private void ElemsDefaultOrder ()
{
elemsSingle .Clear ();
elemsSingle .Add (new CalcElem (labelValue));
elemsSingle .Add (
new CalcElem (labelExpression));
... ...
elemsInGroup .Clear ();
}
```

现在，指定类型的所有元素都会从 `elemsSingle` 移动到 `elemsInGroup`。

## 按类型分组

```csharp
private void SetGroup (CalcElemType elem_type)
{
ElemsDefaultOrder ();
for (int i = elemsSingle .Count - 1; i >= 0; i--)
{
if (elemsSingle [i] .ElemType == elem_type)
{
elemsInGroup .Insert (0, elemsSingle [i]);
elemsSingle .RemoveAt (i);
}
}
... ...
```

当鼠标绘制一个矩形然后释放时，会调用另一个版本的 `SetGroup()` 方法。这种情况下，矩形会被用作参数。

```csharp
private void OnMouseUp (object sender, MouseEventArgs e)
{
ptMouse_Up = e .Location;
double fDist = Auxi_Geometry .Distance (ptMouse_Down, ptMouse_Up);
int iObj, iNode;
if (mover .Release (out iObj, out iNode))
{
... ...
}
else
{
if (e .Button == MouseButtons .Left)
{
if (bRectInView)
{
SetGroup (new Rectangle (Math .Min (ptMouse_Down .X, e .X),
Math .Min (ptMouse_Down .Y, e .Y),
Math .Abs (e .X - ptMouse_Down .X),
Math .Abs (e .Y - ptMouse_Down .Y)));
bRectInView = false;
}
}
... ...
```

所有元素都会被检查是否在此矩形内部；任何位于内部的元素都会被包含到 `elemsInGroup` 中。

## 按矩形区域分组

```csharp
private void SetGroup (RectangleF rc)
{
elemsInGroup .Clear ();
group = null;
ElemsDefaultOrder ();
if (MoreThanOneInside (rc))
{
for (int i = elemsSingle .Count - 1; i >= 0; i--)
{
if (rc .Contains (elemsSingle [i] .RectAround))
{
elemsInGroup .Insert (0, elemsSingle [i]);
elemsSingle .RemoveAt (i);
}
}
... ...
```

如果最终 `elemsInGroup` 中有多于一个元素，那么这些元素就构成一个组。

## 组的创建与消除

```csharp
private void SetGroup (CalcElemType elem_type)
{
... ...
RectangleF rcElems = ElemsArea_group ();
PointF ptLT =
new PointF (rcElems .Left - GroupParameters .DefaultFrameSpaces [0],
rcElems .Top - GroupParameters .DefaultFrameSpaces [1]);
group = new Group_ArbitraryElements (this, mover, ptLT, grparams, strTitle,
ElemsArea_group, DrawElems_group,
SynchroMove_group, IntoMover_group);
GroupMovability ();
}
```

一个组可以通过使用“Delete frame”命令（图 7-5）来消除，但组中拥有多个元素的条件还提供了另一种方法。只需绘制一个不包含任何元素或只包含单个元素的新矩形即可。在这些情况下，都不会组织成组，但如果此时有某个组显示出来，它将会消失。甚至不需要绘制矩形；只需在任意空白位置单击左键就足够了。

## 预定义组的标准定位

还有另一种重新排列元素位置的方法，但此方法仅适用于三个预定义的组。图 7-6 中的子菜单显示了可应用于 Numbers 组的三个命令；当选择 Operations 或 Functions 菜单时，会打开相同的子菜单。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig7_HTML.jpg](img/501148_1_En_7_Fig7_HTML.jpg)

Figure 7-6

`Standard positioning`菜单命令会打开一个辅助窗口，允许您为该预定义组选择一些标准元素定位。

`Settings ➤ Numbers ➤ Standard positioning`命令会打开`Form_BtnPlaces_Numbers.cs`文件（图 7-7a）；此窗口允许您为 Numbers 组选择标准元素定位。Functions 和 Operations 组也有类似的窗口来选择它们的标准视图，但这些组的元素更少，可供选择的组合也更少（图 7-7b 和 7-7c）。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig10_HTML.jpg](img/501148_1_En_7_Fig10_HTML.jpg)

Figure 7-7c

Operations 组标准定位的选择

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig9_HTML.jpg](img/501148_1_En_7_Fig9_HTML.jpg)

Figure 7-7b

Functions 组标准定位的选择

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig8_HTML.jpg](img/501148_1_En_7_Fig8_HTML.jpg)

Figure 7-7a

Numbers 组标准定位的选择

每个标准定位变体都显示为一个草图，并带有一个用于选择该变体的附加复选框。每个草图由一组代表实际控件的矩形组成。这些小矩形通过线条连接，这些线条显示了组中元素的顺序。否则，将无法区分元素按行或按列排列的相似变体。当通过单击`OK`按钮关闭`Form_BtnPlaces_Numbers.cs`文件时，Calculator 中 Numbers 组的按钮将根据所选变体进行排列。此时 Number 组是否有框架并不重要；标准定位始终有效。

每个用于选择标准定位的辅助窗口都包含一个小型组，其中包含两个`NumericUpDown_GR`元素；在图 7-7 中，这些组以不同颜色显示。更改这些元素内的值对草图视图没有影响，因此当这些值更改时，辅助窗口内不会有任何反应。但是，如果选择了任何标准定位并按下`OK`按钮，则 Calculator 中组的元素将根据所选情况放置，并且元素之间的距离由这两个值确定。我们继续讨论 Calculator，但之后，我将回到对图 7-7 中草图的讨论。

## 元素大小的批量调整

Calculator 中的任何元素都可以单独调整大小。现在，假设您想要更改 Numbers 组中按钮的大小；您尝试一个按钮并设置合适的大小。接下来您要做什么？您是希望这个按钮与组中的其他按钮保持不同，还是以相同的方式更改 Numbers 组中其他 11 个按钮的大小？您打算一个一个手动操作吗？有些人可能喜欢让按钮的尺寸根据其上的数字稳定增长，或者组织其他非凡的视图（在用户驱动的应用程序中，没有任何限制！），但我感觉大多数用户更倾向于为组内所有按钮设置相同的大小。我还认为，在一个大组中手动逐个标准化按钮不会是一种受欢迎的操作。

当然，有一种更好的方法：一个元素可以作为其所有兄弟元素的样本。变化仅在于选择传播的参数以及决定将所选参数传播到哪个按钮组。

图 7-8 显示了可以在 Numbers 组的任何按钮上调用的菜单。第一个命令打开一个用于调整此类按钮的辅助窗口（参见图 5-5）；第二个命令的子菜单允许您将参数传播到组中的其他按钮；最后一个命令允许您为两个组的按钮设置相同大小。Numbers 和 Operations 组的按钮最初具有相同大小；最后一个命令允许您保持这种大小相等性。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig11_HTML.jpg](img/501148_1_En_7_Fig11_HTML.jpg)

Figure 7-8

Numbers 组按钮上的菜单

几乎相同的菜单可以在 Operations 和 Functions 组的按钮上调用。类似的但更小的菜单可以在 Cleaners 组的按钮上调用。

通常，应用程序由不同类的元素构建而成，并通过分析被点击元素的类来选择相应的菜单。在这个 Calculator 中，仅通过类来选择元素是不够的，因为大多数元素都属于同一个 `Button_Text` 类，因此需要额外的 `ID` 检查。

```csharp
private void OnMouseUp (object sender, MouseEventArgs e)
{
ptMouse_Up = e .Location;
double fDist = Auxi_Geometry .Distance (ptMouse_Down, ptMouse_Up);
int iObj, iNode;
if (mover .Release (out iObj, out iNode))
{
GraphicalObject grobj = mover .ReleasedSource;
long id = grobj .ID;
if (e .Button == MouseButtons .Left)
{
... ...
}
else if (e .Button == MouseButtons .Right && fDist <= 3)
{
if (grobj is Button_Text)
{
if (id == btn_0 .ID || id == btn_1 .ID || id == btn_2 .ID ||
id == btn_3 .ID || id == btn_4 .ID || id == btn_5 .ID ||
id == btn_6 .ID || id == btn_7 .ID || id == btn_8 .ID ||
id == btn_9 .ID || id == btnSign .ID || id == btnDot .ID)
{
ContextMenuStrip = menuOnNumbers;
}
else if (id == btnPlus .ID || id == btnMinus .ID ||
id == btnMultiply .ID || id == btnDivide .ID ||
id == btnDegree .ID || id == btnEqual .ID)
{
ContextMenuStrip = menuOnOperations;
}
else if (id == btnLn .ID || id == btnLog .ID ||
id == btnExp .ID || id == btnSin .ID ||
id == btnCos .ID || id == btnTg .ID ||
id == btnInverse .ID || id == btnSqrt .ID)
{
ContextMenuStrip = menuOnFunctions;
}
else
{
ContextMenuStrip = menuOnCleaners;
}
}
... ...
```

## 可移动性功能

完全可移动性是改变我们使用应用程序方式的功能，但这种可移动性，如同任何其他功能一样，也受用户控制。假设在移动和调整对象大小后，您组织了一个您喜欢的 Calculator 视图，并且不希望因意外移动任何元素而破坏此视图。您可以通过将元素设置为不可移动来保留此视图。在 Calculator 中，可移动性通过空白位置的菜单同步更改所有元素。

```csharp
private void Click_miFixUnfixAll (object sender, EventArgs e)
{
bMovable = !bMovable;
Resizing resize = bMovable ? Resizing .Any : Resizing .None;
for (int i = 0; i < elemsSingle .Count; i++)
{
elemsSingle [i] .Movable = bMovable;
elemsSingle [i] .Resizing = resize;
}
if (group != null)
{
GroupMovability ();
}
info .Movable = bMovable;
info .Resizing = resize;
RenewMover ();
}
```

相同的可移动性声明应用于组内的内部元素、组本身以及组标题。

```csharp
private void GroupMovability ()
{
Resizing resize = bMovable ? Resizing .Any : Resizing .None;
for (int i = 0; i < elemsInGroup .Count; i++)
{
elemsInGroup [i] .Movable = bMovable;
elemsInGroup [i] .Resizing = resize;
}
group .Movable = bMovable;
group .TitleMovable = bMovable;
}
```

## 草图类与变体

现在让我们回到对草图的讨论，这些草图用于三种辅助表单（图 7-7）。这些表单中的所有元素都是可移动的，因此你可以通过任意方式定位这些草图来重新排列它们的视图。不必担心任何操作：表单的默认视图可以随时通过空白位置的菜单进行重置。

每当你开发用户驱动的应用程序时，都需要不时地设计具有一些有趣且非标准特性的可移动对象，因为需求本身就是非标准的。我认为，当你需要思考一些不平凡的问题时，这是编程中最有趣的部分。

这三种表单中的草图属于 `ButtonsSketch` 类。草图是一个图形对象，其大多数字段用于描述其几何形状。

```csharp
public class ButtonsSketch : GraphicalObject
{
    bool bSelected;                     // 草图选中指示器
    Rectangle m_rc;                     // 对象的整个区域
    Rectangle rcSelect;                 // 虚构复选框的矩形
    Point [] ptBtn;                     // 草图上所有“按钮”的位置
    int w = 16;                         // “按钮”的尺寸
    int h = 8;
    int spaceInside = 4;                // “按钮”之间的间距
    int spaceOnBorders = 6;             // 边缘的间距
    int sideSelectSquare = 14;
}
```

每个辅助表单中的草图展示了特定分组按钮的定位变体。每个标准分组都有固定数量的按钮，因此一个辅助表单中的所有草图都展示了相同数量按钮的定位变体；让我们将这个数量称为 N。图 7-9 展示了 N = 12 时的一些变体。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig12_HTML.jpg](img/501148_1_En_7_Fig12_HTML.jpg)

**图 7-9** N = 12 情况下的几个草图

- 每个按钮由一个小的深色矩形表示；其尺寸为 `16*8` 像素（`w * h`）。
- 草图中相邻小矩形之间的距离始终相同，等于 `4` 像素（`spaceInside`）。
- 草图有一个圆角边框；此边框与内部元素之间的距离为 `6` 像素（`spaceOnBorders`）。
- 要选择分组按钮所需的定位，必须标记所需的草图；这通过框架内的一个小复选框来完成。这个特殊复选框占据一个边长为 `14` 像素（`sideSelectSquare`）的矩形。
- 通过行和列来定位 N 个按钮可以有多种方式，但有两种边界变体：所有按钮在一行和所有按钮在一列。为了描述草图上“按钮”的位置，我使用了一个大小为 `N*N` 的矩阵。位置的编号从左上角开始为 `0`，沿上行向右；然后逐行向下；在每一行中，编号从左位置开始，向右递增。因此，第二行的左位置编号为 N；矩阵右下角的位置编号为最大的 `N*N-1`。图 7-9 中的所有草图都是为 `N = 12` 的情况设计的。位置数组是草图初始化的参数之一。接下来你可以看到图 7-9 左下角被选中草图的初始化代码。

```csharp
ButtonsSketch sketch_2rows = new ButtonsSketch (nCode, false,
    new Point (rect .Left, rect .Bottom + 20),
    new int [] { 0, 1, 2, 3, 4, 5, 12, 13, 14, 15, 16, 17 },
    brushBtns);
```

- 草图可能具有相同的行数和列数，但元素的顺序不同。例如，图 7-9 中的两个草图各有四行三列；然而，在左侧示例中，元素需要按行定位，而在另一个示例中，则需要按列定位。为了在视觉上区分这种情况，草图中行或列内的连续元素会用线条连接起来。

```csharp
public void Draw (Graphics grfx)
{
    Pen penConnect = new Pen (brushBtns .Color, 2);
    Auxi_Drawing .CurvedFrame (grfx, m_rc, penFrame);
    for (int i = 0; i < ptBtn .Length; i++)
    {
        if (i > 0)
        {
            if (ptBtn[i].X == ptBtn [i - 1].X || ptBtn[i].Y == ptBtn [i - 1].Y)
            {
                grfx .DrawLine (penConnect,
                    ptBtn [i] .X + w / 2, ptBtn [i] .Y + h / 2,
                    ptBtn [i - 1] .X + w / 2, ptBtn [i - 1] .Y + h / 2);
            }
        }
    }
    ControlPaint .DrawCheckBox (grfx, rcSelect,
        bSelected ? ButtonState .Checked : ButtonState .Normal);
}
```

草图可通过其任何内部点（复选框区域除外）进行移动，因此 `ButtonsSketch` 的覆盖区域由两个矩形节点组成。通常，覆盖复选框矩形（`rcSelect`）的较小节点必须位于负责移动整个对象的较大节点之前。

```csharp
public override void DefineCover ()
{
    CoverNode [] nodes = new CoverNode [] {
        new CoverNode (0, rcSelect, Cursors .Hand),
        new CoverNode (1, m_rc, Cursors .SizeAll) };
    cover = new Cover (nodes);
}
```

仅当按下较大的节点（编号 1）时，才会执行草图移动操作。

```csharp
public override bool MoveNode (...)
{
    bool bRet = false;
    if (catcher == MouseButtons .Left)
    {
        if (i == 1)
        {
            Move (dx, dy);
            bRet = true;
        }
    }
    return (bRet);
}
```

草图是可移动但不可调整大小的。当移动草图时，所有基本点的值会同步改变。

```csharp
public override void Move (int dx, int dy)
{
    m_rc .X += dx;
    m_rc .Y += dy;
    for (int i = 0; i < ptBtn .Length; i++)
    {
        ptBtn [i] .X += dx;
        ptBtn [i] .Y += dy;
    }
    rcSelect .X += dx;
    rcSelect .Y += dy;
}
```

这些就是可以在 `ButtonsSketch` 类中找到的所有有趣之处。与该类相关的另一个重要事项在使用草图的表单中会提及，例如在 `Form_BtnPlaces_Numbers.cs` 中。通过点击显示为复选框的特殊区域来选择所需的草图；这个小的方形区域由 `ButtonsSketch` 类的节点零覆盖。任何时候，一个表单中只能标记一个草图，因此通过一组单选按钮进行选择会更正确，但由于在较早的示例中使用了相同的小方块选择方式，我决定不改变视图。

```csharp
private void OnMouseUp (object sender, MouseEventArgs e)
{
    ptMouse_Up = e .Location;
    double fDist = Auxi_Geometry .Distance (ptMouse_Down, ptMouse_Up);
    int iObject, iNode;
    if (mover .Release (out iObject, out iNode))
    {
        GraphicalObject grobj = mover .ReleasedSource;
        if (e .Button == MouseButtons .Left)
        {
            if (grobj is ButtonsSketch && iNode == 0)
            {
                ButtonsSketch sketch = grobj as ButtonsSketch;
                for (int i = 0; i < sketches .Count; i++)
                {
                    sketches [i] .Selected = false;
                }
                sketch .Selected = true;
                viewSelected = (Calculator_FunctionsGroupView) (sketch .Code);
                places = sketch .Places;
                numericHor .Enabled = true;
                numericVer .Enabled = true;
                btnOK .Enabled = true;
                Invalidate ();
            }
            ...
```

多年前，我设计了第一版 `Calculator`，旨在演示如何将一个完全基于普通控件的程序转变为用户驱动型应用。现在我们有了一个外观相似、功能相同，但仅用图形元素设计的应用。作为用户驱动型应用，这个版本确实好得多，但……我是否必须再找一个使用普通控件的演示程序呢？

## 家谱

本节的主要示例是一个我自己使用的真实应用。自 2013 年初我在一篇文章中首次写到这个示例以来，它便吸引了世界各地程序员的关注。还有其他一些领域（电路设计是其中之一），其设计与家谱类似，因此这些领域的专家也对当前示例产生了兴趣。

在我们到达本节的最终程序——真正的 `Family Tree` 应用之前，还有一整套简单的示例来逐步展示开发过程。在为这个应用编写代码时，我再次想到了霍格本家族的成员，他们能用原始元素构建出极其精巧的装置。^(⁹) 在这个 `Family Tree` 应用中，我只使用直线、矩形和最简洁的注释。在本节结束时，你将看到在如此原始的基础上能开发出怎样的成果。

`Family Tree` 程序是一个标准的用户驱动型应用，用户可以修改其中的所有内容，并且所有这些更改都会被保存和恢复。初步示例中的某些元素可能不会被保存和恢复，但这仅仅是因为我希望辅助示例始终能为我的讲解呈现最佳视图。

你可以轻松地拿起一张纸，开始绘制自己的家谱。多年前，我在祖母的指导下做过这件事，那些旧纸张我至今仍保存着。许多家庭中的很多人都会在某个时刻做同样的事，并且几乎每个人都对家谱应有的样子有自己的理解。家谱可以在许多历史书籍中找到，那些专业绘制的家谱与简单的钢笔或铅笔草图相比，仅在一些细微之处有所不同。任何家谱草图看起来都像图 7-10 所示。不过，这张草图展示的是一个代表三代人的小型简单家谱。为了简化草图和讨论，我省略了通常显示的每个人的出生和死亡信息；你将在后续示例中看到这些信息。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig13_HTML.jpg](img/501148_1_En_7_Fig13_HTML.jpg)

**图 7-10** 一个三代家谱的示例

# 家族树应用中的图形表示与“Bus”系统

即使是一个简单的草图（图 7-10），也能充分说明需要在 `Family Tree` 应用中实现的规则。每个人用一个小的矩形表示，大量信息通过矩形之间的连接关系来展示。通常，一对夫妇被表示为两个紧邻的矩形，它们之间有一条短的直线（*总线*）。如果一对夫妇有孩子，那么从那条中间线上会向下延伸出另一条总线（图 7-11），但下一代的视图取决于孩子的数量。如果只有一个孩子，那么这条线会直接向下延伸到代表这个人的矩形（图 7-12）。如果有多个孩子，那么两代之间会有一条中间总线；这条总线向下连接到每个孩子，并向上连接到其父母之间的线。例如，图 7-13 顶部显示的夫妇有三个孩子。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig16_HTML.jpg](img/501148_1_En_7_Fig16_HTML.jpg)

**图 7-13** 有三个孩子的夫妇

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig15_HTML.jpg](img/501148_1_En_7_Fig15_HTML.jpg)

**图 7-12** 有一个孩子的夫妇

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig14_HTML.jpg](img/501148_1_En_7_Fig14_HTML.jpg)

**图 7-11** 有孩子的夫妇

当绘制仅包含直系亲属的部分时，您通常了解所有必要的信息，不会有任何问题。当尝试绘制家族谱系图的某些外围部分时，可能会出现信息缺失，您要么绘制一些没有任何信息的空白矩形，要么按照已知信息绘制，即使这违反了简单的生物学规则（图 7-14）。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig17_HTML.jpg](img/501148_1_En_7_Fig17_HTML.jpg)

图 7-14

这种情况从生物学角度来说不正确，但没有更多信息

`FamilyTree`程序必须包含简化添加某些标准部分（配偶、父母、一个孩子、多个孩子）过程的命令。作为开发人员，您必须考虑最常需要的变体，并提供一个命令系统来响应此类请求，但没有哪个命令系统能涵盖所有可能的亲属关系变体。这是一种经典情况，用户驱动型应用远优于任何具有固定变体系统的程序，这也是用户驱动型应用可能是唯一真正优秀方案的例子之一。

让我们再看一下图 7-10。如前所述，有矩形和线条，在当前程序中称为 *bus*。有些`bus`连接矩形，有些`bus`连接其他`bus`，还有`bus`连接`bus`和矩形。灵活的`bus`系统允许您绘制任何所需内容，因此我们通过开发一些灵活的`bus`来开始`FamilyTree`应用程序的工作。

```csharp
public class Bus : GraphicalObject
{
    Pen m_pen;
    bool bMarkJoints = true;
    bool bMarkEnds = true;
    Color clrEnds = Color.Red;
    Color clrJoints = Color.White;
    bool bRegisteredWithMover = true;
    bool bUseWarningColor = true;
    Pen penWarn = new Pen(Color.Magenta, 2);
    List<PointF> m_pts = new List<PointF>();
    List<object> headsConnected = new List<object>();
    List<object> tailsConnected = new List<object>();
}
```

任何`bus`都由一组直线段组成，这些线段一个接一个地连接成一条链；每个`bus`中至少有一个线段，但线段数量没有上限。每个线段由两个点描述，前一个线段的终点是下一个线段的起点。因此，`bus`的最小点数是两个，线段数总是比点数少一个。`bus`可以通过点的`List`或仅通过两个点进行初始化；在后一种情况下，`bus`由单个线段组成。

```csharp
public Bus(Form frm, Mover mvr, List<PointF> points, Pen pen)
{
    ... ...
    m_pen = new Pen(pen.Color, pen.Width);
    m_pen.DashStyle = pen.DashStyle;
    m_pts = points;
    Movable = false;
}
```

`bus`的覆盖物（cover）由`List`中所有点上的小圆形节点和另一组覆盖线段的条状节点组成。

```csharp
public override void DefineCover()
{
    int rad = Math.Max(4, Convert.ToInt32(Width / 2));
    int nCircles = pts.Count;
    int nStrips = pts.Count - 1;
    CoverNode[] nodes = new CoverNode[nCircles + nStrips];
    for (int i = 0; i < nCircles; i++)
    {
        nodes[i] = new CoverNode(i, m_pts[i], rad);
    }
    for (int i = 0; i < nStrips; i++)
    {
        nodes[nCircles + i] = new CoverNode(nCircles + i, m_pts[i],
        m_pts[i + 1], rad, Behaviour.Frozen);
    }
    cover = new Cover(nodes);
    if (Movable)
    {
        cover.SetBehaviour(NodeShape.Strip, Behaviour.Moveable);
        cover.SetCursor(Cursors.SizeAll);
    }
}
```

通过移动圆形节点，可以改变线段的长度和角度；这样，`bus`的配置就改变了。

现在，是时候开始我们逐步的设计，并从一个初步示例过渡到另一个示例了。所有这些辅助示例分为三组。第一组示例中只使用自由的`bus`。第二组中的示例演示了连接的`bus`。在第三组中，使用了`Person`对象。

## 自由 Buses

*   文件：`Form_FreeBuses.cs`

*   菜单位置：应用程序 ➤ FamilyTree 设计逐步 ➤ 自由 buses

第一个初步示例演示了两个`bus`；其中一个由单个线段组成，而另一个有四个线段（图 7-15）。`bus`可以通过一对点或一个点的`List`进行初始化；当前示例中演示了两种情况。第二个`bus`的点由`PointsForLine()`方法提供；这些是来自表单区域的随机点，但有一个限制。`bus`是在信息区域之后构建的，因此`PointsForLine()`方法保证了在初始化时，没有`bus`特殊点隐藏在信息区域后面。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig19_HTML.jpg](img/501148_1_En_7_Fig19_HTML.jpg)

图 7-15b

警告已打开

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig18_HTML.jpg](img/501148_1_En_7_Fig18_HTML.jpg)

图 7-15a

为查看自由`bus`的颜色，警告已关闭

```csharp
private void DefaultBuses()
{
    bus_2 = new Bus(this, mover, new PointF(100, 220), new PointF(300, 400), new Pen(Color.Green, 3));
    bus_5 = new Bus(this, mover, PointsForLine(5), new Pen(Color.Blue, 3));
    bus_2.UseWarningColor =
    bus_5.UseWarningColor = checkboxWarning.Checked;
}
```

## 警告颜色与基本结构

这两个`bus`使用两种不同的画笔初始化，但为了查看自由`bus`的颜色，必须关闭特殊警告（图 7-15a）。未连接到任何东西的`bus`，甚至带有一个自由端的`bus`，在家族谱系图中都是异常情况，此类`bus`会使用特殊的警告颜色显示。几个初步示例包含一个复选框，允许您打开/关闭此警告，并查看那些在初始化时声明的颜色下的自由`bus`。每个`bus`的警告颜色使用通过`Bus.UseWarningColor`属性进行调节。稍后在本节中，将演示`bus`的调整表单；此表单包含一个复选框，用于调节警告颜色的使用。在这样一个初步示例中，强制切换到正常颜色可能有用，图 7-15a 中的`bus`视图比图 7-15b 中的更好，但在实际应用中，这样的警告是有帮助的，我建议不要将其关闭。

`bus`是一个相对简单的对象。其主要部分是一条带有所有三个可视化参数（颜色、宽度和线条样式）的线条，这些参数打包到一个字段（`m_pen`）中。另外两种颜色用于标记`bus`端点（`clrEnds`）和线段之间的连接点（`clrJoints`），但这些特殊标记的出现是有条件的。所有特殊点（端点和连接点）上的标记仅对不可移动的`bus`显示，即便如此，还有两个布尔参数——`bMarkEnds`和`bMarkJoints`——来调节这些标记的出现与否。`Bus.Draw()`方法首先检查一整套条件，在这些条件下，线条必须以警告颜色显示。我们将在后面的示例中探讨所有这些条件。

```csharp
public void Draw(Graphics grfx)
{
    if (((bHeadConnected != bTailConnected) ||
        (bHeadConnected == false && bTailConnected == false &&
        headsConnected.Count == 0 && tailsConnected.Count == 0 &&
        bRegisteredWithMover)) && bUseWarningColor)
    {
        grfx.DrawLines(penWarn, m_pts.ToArray());
    }
    else
    {
        grfx.DrawLines(m_pen, m_pts.ToArray());
    }
    if (!Movable)
    {
        if (bMarkJoints)
        {
            for (int i = 1; i < m_pts.Count - 1; i++)
            {
                Auxi_Drawing.FillCircle(grfx, m_pts[i], 3, clrJoints);
            }
        }
        if (bMarkEnds)
        {
            Auxi_Drawing.FillCircle(grfx, m_pts[0], 3, clrEnds);
            Auxi_Drawing.FillCircle(grfx, m_pts[m_pts.Count - 1], 3,
            clrEnds);
        }
    }
}
```

在当前示例中，可视化参数和母线段数均不可更改，但所有这些内容将在后续示例中进行调整。

## 可移动性与节点操作

使用两种额外颜色标记所有可移动点，使得总线配置的更改更加容易。但在某些情况下，根本不需要此类更改；此时，可以显示不带任何额外标记的总线。当用户已经熟悉如何更改总线时，这些额外标记也不再需要。在“家谱”应用程序中，有一个简单的方法可以开启/关闭它们，用户可以在任何时候进行操作。

任何新的`Bus`都会作为不可移动对象进行构造；`Bus`构造函数已在前面介绍过。可移动性的更改将在本节后文中讨论，而在当前示例中，总线始终是不可移动的。

总线作为一个整体对象是不可移动的，但所有特殊点（端点和连接点）都可以移动。任何移动都始于对相应类的`Press()`方法的调用。

```csharp
private void OnMouseDown (object sender, MouseEventArgs e)
{
    ptMouse_Down = e.Location;
    if (mover.Catch(e.Location, e.Button))
    {
        GraphicalObject grobj = mover.CaughtSource;
        int iNode = mover.CaughtNode;
        if (grobj is Bus)
        {
            (grobj as Bus).Press(e.Location, e.Button, iNode);
        }
        ... ...
    }
}
```

如果某个小圆形节点被左键按下，则会出现一种完全标准的反应——光标位置会调整到该节点的中心点。

```csharp
public void Press (Point ptMouse, MouseButtons catcher, int iNode)
{
    if (catcher == MouseButtons.Left && iNode < m_pts.Count)
    {
        AdjustCursorPosition(m_pts[iNode]);
    }
}
```

任何被捕获节点的移动都由`Bus.MoveNode()`方法描述；在当前情况下，我们只对该方法中应用于不可移动总线的部分感兴趣。

```csharp
public override bool MoveNode (...)
{
    if (catcher == MouseButtons.Left)
    {
        if (Movable)
        {
            ... ...
        }
        else
        {
            if (iNode < m_pts.Count)
            {
                m_pts[iNode] = ptMouse;
                InformConnections(PositionUpdate.Soft, iNode - 1, iNode);
                bRet = true;
            }
        }
    }
}
```

现在讨论连接还为时过早，但您可以从这段代码中看到，在移动圆形节点的整个过程中，与该节点关联的特殊点始终跟随光标。

```csharp
m_pts[iNode] = ptMouse;
```

## 添加连接点

第一个示例允许您移动特殊点，但除此之外不能对总线进行任何其他操作。下一个示例允许您向现有总线添加新的连接点。

*   文件：`Form_FreeBuses_AddingJoints.cs`
*   菜单位置：应用程序 ➤ FamilyTree 逐步设计 ➤ 自由总线；添加连接点

在本示例中，有两种方法可以更改总线配置（图 7-16）：移动其中一个特殊点（端点和连接点），或者在某个线段内部左键点击，并在不松开按钮的情况下，继续移动在点击点处出现的新连接点。这种添加连接点的方式在`Form_Polyline.cs`中已经演示过，但由于时间已久，我想再提醒一下这个过程。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig20_HTML.jpg](img/501148_1_En_7_Fig20_HTML.jpg)

图 7-16 彩色标记显示了所有特殊点

总线可以按可移动和不可移动两种方式使用。可移动总线是指在任何点按下后即可在屏幕上移动而无需更改配置的总线。如果总线是可移动的，则无法通过同样的左键按下来添加新的连接点。家谱既需要可移动总线，也需要不可移动总线。一些总线只能用作不可移动（这将在后面解释）；其他总线的可移动性可以开启/关闭，这由用户通过菜单命令进行控制。当前示例中的两条总线始终是不可移动的，但为了使`OnMouseDown()`方法的代码与所有后续示例保持一致，仍然会对总线的可移动性进行检查。（我想强调一下，在当前示例中这是多余的。）

新的连接点是在按下总线线段的那一刻组织的，因此所有逻辑都在`OnMouseDown()`方法的代码中。我认为在查看代码之前先看一些注释会很有帮助。

当在一个`不可移动总线`的带状节点上按下时，会出现以下步骤序列：

1.  线段编号（`iSegment`）可以通过按下的节点编号（`iNode`）轻松计算得出。
2.  该线段上最近的点（`pt`）由`Bus.NearestPointOnSegment()`方法计算得出。
3.  添加连接点将导致总线覆盖区域发生变化。此后，同一个总线将被另一个节点捕获，因此首先必须通过`mover`释放该总线。

# 连接点的创建与节点线可移动性

4.  新的连接点通过`Bus.InsertPoint()`方法在计算出的点`pt`处创建。由于连接点数量已发生变化，覆盖区域被重新定义；这由`InsertPoint()`方法内部完成。

5.  总线再次被`mover`捕获，但此时光标位于新圆形节点的区域内。在`Bus`覆盖区域中，所有圆形节点都排在带状节点之前，因此总线在同一位置不是被带状节点捕获，而是被这个圆形节点捕获。

```csharp
private void OnMouseDown (object sender, MouseEventArgs e)
{
    ptMouse_Down = e.Location;
    if (mover.Catch(e.Location, e.Button))
    {
        GraphicalObject grobj = mover.CaughtSource;
        int iNode = mover.CaughtNode;
        if (grobj is Bus)
        {
            Bus bus = grobj as Bus;
            bus.Press(e.Location, e.Button, iNode);
            if (e.Button == MouseButtons.Left &&
                mover.CaughtNodeShape == NodeShape.Strip && !bus.Movable)
            {
                int iSegment = iNode - bus.PointsNumber;
                PointF pt = bus.NearestPointOnSegment(e.Location, iSegment);
                mover.Release();
                bus.InsertPoint(iSegment + 1, pt);
                mover.Catch(e.Location, e.Button);
                Invalidate();
            }
        }
        ... ...
    }
}
```

通过这种方式，一个新的连接点被添加到一个不可移动的总线上。下一个示例演示了在按下可移动和不可移动总线时的反应。

*   文件：`Form_FreeBuses_ChangingMovability.cs`

*   菜单位置：应用程序 ➤ FamilyTree 逐步设计 ➤ 自由总线；更改可移动性

图 7-17 对图 7-13 进行了略微修改，为不同类型的总线添加了标记。即使是这样一棵仅包含几个人的简单家谱，也展示了用于不同类型连接的总线；在图 7-17 中，它们用不同字母进行了标记。家谱中的总线用于连接不同的元素，因此带有自由端的总线是一个例外。

| `Case` **A** | 在当前案例中，这是一个夫妻之间的节点线，但通常此类节点线用于两人之间。它的两端必须始终连接到代表这两个人的矩形上，因此这个节点线不能自由移动到任何其他位置。在图中，这些节点线是直线，每条都可以变成带有多个拐点的折线，但在任何时刻，它都必须显示这两人之间的链接。 |
| `Case` **B** | 这种短节点线将一条长节点线与同层节点相连。代表同层节点的矩形可以任意放置在屏幕上；连接到其中任何一个的节点线都可能更长，并且可以变成折线，但每条节点线的两端都连接到某些对象（一端连接节点线，另一端连接矩形），因此这样的节点线不能自由在屏幕上移动。 |
| `Case` **C** | 这种类型的节点线用于连接两条节点线，因此它的两端也都放置在其他元素上，并且此节点线不能自由移动。 |
| `Case` **S** | 这是唯一一种两端不固定在其他对象上的节点线；此节点线的字母 *S* 取自单词 ***s**pecial*（特殊）。这种节点线的两端不固定，因此可以在不改变外观的情况下自由移动。这是连接同层节点的节点线；当你移动这条节点线时，所有连接都会随之移动。在图 7-17 中，这条节点线与属于两代人的矩形（父母及其子女之间）保持等距。如果有人更喜欢将此节点线靠近某一代人的矩形，可以在不破坏家谱的情况下完成。这将是设计的一个良好特性，但随之而来的问题是实现方式，因为到目前为止，在任何节点线上按下鼠标只能添加新拐点并移动该拐点。 |

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig21_HTML.jpg](img/501148_1_En_7_Fig21_HTML.jpg)

图 7-17

这个小树只有一条两端未放置在其他元素上的节点线。该节点线上标有字母 `S`（表示特殊）

在本书的所有示例中，使用左键移动整个对象或对象的任何部分，我打算遵循此规则。在上一个示例 `Form_FreeBuses_AddingJoints.cs` 中，在线段内部的任意点按下左键会在按下点添加一个拐点，并开始移动这个新拐点的过程。然而，我们遇到一种情况：根据我们的意愿，按下左键必须启动添加并移动新拐点，或者在不改变其配置的情况下移动整个节点线。计算机无法读取用户的思想来判定该用户打算在某一时刻执行哪种操作，因此用户必须执行一些预备操作来判定。这个预备操作就是更改节点线的可移动性。

## 节点线的可移动性

让我们不把节点线看作是一系列特殊点以及连接这些点的直线段（图 7-18），而是看作一组节点。这些节点不可见，但很容易想象它们的几何形状。图 7-18 中的每条线段都是一个带状节点的轴，每个特殊点是一个圆形节点的中心，因此所有节点的组合区域与线条视图相同，但比我们在图中看到的更宽。尽管所有特殊点对节点线的工作都很重要，但它们所有圆形节点的组合区域只占覆盖面积的极小部分。将节点线的可移动性视为其线段的可移动性是有道理的，实际情况也确实如此。同时，特殊点的可移动性始终与线段的可移动性相反。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig22_HTML.jpg](img/501148_1_En_7_Fig22_HTML.jpg)

图 7-18

节点线的可移动性可以通过菜单命令更改。端点以及拐点仅对不可移动的节点线显示

任何节点线初始化时都是不可移动的。

```csharp
public Bus (Form frm, Mover mvr, List points, Pen pen)
{
... ...
Movable = false;
```

根据此节点线不可移动的声明，带状节点必须是不可移动的，而圆形节点必须是可移动的。默认情况下，所有节点都是可移动的，因此圆形节点在初始化时无需任何更改，而带状节点则需要通过一个额外的参数来指定其行为，从而被固定（不可移动）。

```csharp
public override void DefineCover ()
{
... ...
for (int i = 0; i < nCircles; i++)
{
nodes [i] = new CoverNode (i, m_pts [i], rad);
}
for (int i = 0; i < nStrips; i++)
{
nodes [nCircles + i] = new CoverNode (nCircles + i, m_pts [i],
m_pts [i + 1], rad, Behaviour .Frozen);
}
... ...
```

这种声明的结果在之前的示例中已经展示过。

*   你可以按下任何一个圆形节点（任何拐点或端点）并在屏幕上移动它。

*   你可以按下任何一个带状节点，因为移动器能感知到所有被固定的节点，但你无法移动它。相反，会在按下的点处组织一个新拐点，并且像任何其他拐点一样，它是可移动的；这一点在之前的示例中已经解释过。这种添加新拐点的操作是在 `OnMouseDown()` 方法内部完成的，但前提是按下的节点线是不可移动的（参见三页前的早期代码）。

如果节点线从不可移动变为可移动，会发生什么？节点线可移动性的切换不会改变覆盖中节点的数量、形状或顺序，但它会改变带状节点的行为。对于可移动的节点线，在 `Bus.DefineCover()` 方法的末尾有一个特殊的附加处理。

```csharp
public override void DefineCover ()
{
... ...
if (Movable)
{
cover .SetBehaviour (NodeShape .Strip, Behaviour .Moveable);
cover .SetCursor (Cursors .SizeAll);
}
}
```

对于可移动的节点线，用左键按下哪个节点或哪个点都无关紧要，因为 `MoveNode()` 方法只是简单地调用 `Move()` 方法。

```csharp
public override bool MoveNode (...)
{
bool bRet = false;
if (catcher == MouseButtons .Left)
{
if (Movable)
{
Move (dx, dy);
... ...
```

`Move()` 方法以相同的方式更改所有特殊点；整个节点线被移动而不改变其配置。

```csharp
public override void Move (int dx, int dy)
{
SizeF size = new SizeF (dx, dy);
for (int i = 0; i < m_pts .Count; i++)
{
m_pts [i] += size;
}
}
```

在当前示例（图 7-18）中，节点线的可移动性通过小型上下文菜单的单个命令来更改。

```csharp
private void Click_miMovable (object sender, EventArgs e)
{
busPressed .Movable = !busPressed .Movable;
Invalidate ();
}
```

对左键按下的反应对于可移动和不可移动的节点线来说完全不同，如果可移动和不可移动的节点线看起来一模一样，用户会感到困惑。为了避免这种混淆，节点线的外观略微取决于其可移动性。对于不可移动的节点线，其拐点和端点可以用特殊标记显示（图 7-19a）；这使得按下特殊点更容易。可移动的节点线上没有特殊标记（图 7-19b）；整个节点线用一种颜色绘制。可见特殊点的缺失表明没有可改变配置的点，因此在任何点按下的反应都是相同的。

![`../images/501148_1_En_7_Chapter/501148_1_En_7_Fig24_HTML.jpg`](img/501148_1_En_7_Fig24_HTML.jpg)

图 7-19b

可移动节点线上的菜单

![`../images/501148_1_En_7_Chapter/501148_1_En_7_Fig23_HTML.jpg`](img/501148_1_En_7_Fig23_HTML.jpg)

图 7-19a

不可移动节点线上的菜单

# 总线连接与配置

前面几页展示了 `Bus.Draw()` 方法，但当时尚未讨论总线的可移动性，因此这里专门抽取出该方法中用于绘制所有特殊点的部分。如果存在这些标记，它们会在总线绘制完成后被绘制出来，并且无论总线是以正常颜色还是警告颜色显示，这些特殊标记的颜色都保持不变。

```csharp
public void Draw (Graphics grfx)
{
... ...
if (!Movable)
{
if (bMarkJoints)
{
... ...
}
if (bMarkEnds)
{
... ...
}
}
}
```

在真正的族谱应用程序中，可能需要更多对总线视图和配置的更改；这些可能的更改将在下一个示例中演示。

- 文件：`Form_FreeBuses_AllChanges.cs`
- 菜单位置：应用程序 ➤ 族谱设计逐步指南 ➤ 自由总线；所有更改

这个新示例的视图与前一个相同。主要区别在于，通过两个菜单的命令可以实现所有可能的总线更改，这两个菜单可以在总线的不同部分调用。当你点击接点或线段时，你会看到这些菜单，但在工作程序的任何时刻，你只能看到其中一个菜单。无法同时看到它们，因此图 7-20 有点人为构造的意味，但我认为这种视图能让所有可能性更加清晰。像往常一样，要调用的菜单由窗体的 `OnMouseUp()` 方法决定。当需要在同一对象的不同部分调用不同的菜单时，菜单的选择可以基于节点编号和形状；当前示例中两种方式都用到了。在这个过程中，会确定被按下的特殊点（`iPressedPoint`）或线段（`iPressedSegment`）的编号；这些编号对于某些菜单命令是必需的。

![图 7-20 线段和接点的不同菜单](img/501148_1_En_7_Fig25_HTML.jpg)

**图 7-20** 线段和接点的不同菜单

```csharp
private void OnMouseUp (object sender,
MouseEventArgs e)
{
int iWasObject, iWasNode;
NodeShape shapeNode;
ptMouse_Up = e .Location;
double fDist = Auxi_Geometry .Distance (ptMouse_Down, ptMouse_Up);
if (mover .Release (out iWasObject, out iWasNode, out shapeNode))
{
GraphicalObject grobj = mover .ReleasedSource;
if (e .Button == MouseButtons .Left)
{
... ...
}
else if (e .Button == MouseButtons .Right && fDist <= 3)
{
if (grobj is Bus)
{
int nPoints = busPressed .PointsNumber;
if (shapeNode == NodeShape .Strip)
{
iPressedSegment = iWasNode - nPoints;
ContextMenuStrip = menuOnSegment;
}
else
{
if (0 < iWasNode && iWasNode < nPoints - 1)
{
iPressedPoint = iWasNode;
ContextMenuStrip = menuOnJoint;
}
... ...
```

接点菜单（`menuOnJoint`）包含一个用于删除被按下接点的命令；执行此命令后，可以无需手动移动接点即可拉直总线。在此示例中，允许对可移动和不可移动总线删除接点，但对于将此命令应用于可移动总线的正确性存在一些疑问。当总线被声明为可移动时，就无法通过鼠标点击线段来添加新接点；也无法单独移动现有的特殊点，因为总线可以通过任何点来移动。因此，总线的两个特性——可移动性和重新配置的可能性——在某种程度上是相互关联的，对于它们之间的关联程度可能存在不同看法。

- 如果你仅将总线可移动性的改变视为移动整个对象的机会，那么擦除此类总线接点的可能性是正确的。
- 如果你认为对于可移动总线，必须阻止所有改变其配置的可能性，那么对于可移动总线，此命令必须被禁用。

如果你决定阻止可移动总线的配置更改，则需要在两个不同的地方修改代码，因为接点菜单只允许删除被按下的接点，而线段菜单则允许删除被按下总线的所有接点。

在线段上调用的菜单包含多个命令（这是图 7-20 中较大的那个菜单）。

![图 7-21 总线调整窗体](img/501148_1_En_7_Fig26_HTML.jpg)

**图 7-21** 总线调整窗体

| 命令 | 描述 |
| :--- | :--- |
| *修改* | 调用一个附加的调整窗体来修改被按下总线的视图。此 `Form_Tuning_Bus.cs`（图 7-21）允许您执行以下操作：更改总线的颜色、宽度和样式 (`DashStyle`)，调节端点与接点处特殊标记的外观，更改特殊标记的颜色，调节警告颜色的使用。 |
| *可移动（总线）* | 允许您切换被按下总线的可移动性。可移动性的改变会略微改变总线的视图，如上一个示例所述。 |
| *删除所有接点（总线内部）* | 擦除所有接点，使总线仅由端点之间的单个线段组成。 |
| *水平（线段）* | 将按下的线段转换为水平线。 |
| *垂直（线段）* | 将按下的线段转换为垂直线。 |

最后两个命令的工作方式类似，并且使用限制也相似。

- 要将线段严格转换为水平或垂直，必须移动线段的一个端点。总线的两个端点看起来相似，无法从视觉上确定点的顺序从哪个端点开始，但所有特殊点都按某种顺序存储在 `List<PointF> m_pts` 中，并且这些命令会移动编号较大的点。这对于除最后一个线段之外的任何线段都是正确的，对于最后一个线段，移动的是编号较小的点。
- 在当前初步示例中，我们处理的是自由总线。稍后，相同的命令将用于真实树状图中的线段，此时总线连接到某些对象。这些命令不得影响连接点；因此，将这些命令应用于任何总线的最后一个线段的逻辑是不同的。这也意味着这两个命令不能用于仅由单个线段构成的总线。
- 如果按下的线段近乎水平，并且您命令将其转换为垂直线，则其端点将获得相同的 X 坐标，新线段将变得非常短。如果您对水平线段应用相同的命令，它将变成一个点并从视图中消失。为避免此类情况，仅当转换后的新线段长度不小于 10 像素时，才允许使用这些命令。

到目前为止，我们一直在查看未连接任何东西的自由总线。在真实的族谱图中，我们并没有完全自由的总线；图 7-10 显示，总线要么相互连接，要么可以作为代表人物的矩形之间的链接。我们稍后将讨论那些特殊的矩形，现在让我们考虑一下连接总线的情况。

### 连接的总线

总线并非通过任意点相互连接。相反，组织任何总线连接只有一种方式，并且关于此连接如何工作有严格的规定。连接两条总线的唯一方法，是将一条总线的端点移动并以某种方式固定在另一条总线上。从此刻起，第一条总线的连接端只能沿着第二条总线（将其作为轨道）移动。无论第二条总线是直线还是折线，第一条总线的连接端都可以在第二条总线的两个端点之间全程移动。

`BusConnection` 类的一个元素用于连接两条总线。

```csharp
public class BusConnection : GraphicalObject
{
    PointF ptCon;           // 连接点
    int m_radius;           // 节点半径
    Bus m_busOfEnd;         // 通过其端点连接的总线
    EndType end_type;       // 端点类型
    Bus m_busRail;          // 端点所连接的总线（轨道）
    int iSegment;           // 轨道线段
    double coefOnSegment;   // 该线段上的定位系数
}
```

`BusConnection` 类的对象在将要连接到另一条总线的那个总线的端点处组织。要组织一个新的连接，需要两条总线的相关信息。这些信息包括：连接点（`ptCon`）、连接点周围敏感区域的半径（`m_radius`）、通过其端点连接的总线（`m_busOfEnd`）、指定连接端类型的附加值（`end_type`），以及第一条总线所连接且连接端可沿其移动的总线（`m_busRail`）。

# BusConnection 类详解

## 类定义与构造函数

```csharp
public BusConnection (Form frm, Mover mvr, PointF pt, int rad, Bus bus_End,
    EndType endType, Bus bus_Rail)
{
    ... ...
    m_radius = Math .Max (Math .Abs (rad), 3);
    m_busOfEnd = bus_End;
    end_type = endType;
    m_busRail = bus_Rail;
    ... ...
    ptCon = Auxi_Geometry .Polyline_NearestPoint (pt, m_busRail .Points,
        out dist, out iSegment, out coefOnSegment);
}
```

从视觉上看，任何总线两端是无法区分的，但任何总线都是一个由线段按严格顺序连接的点序列。每个 `Bus` 对象将其特殊点保存为一个 `List`，这些点根据它们在 `List` 中的顺序进行连接。`List` 中的第一个点是总线头，最后一个点是总线尾。

## EndType 枚举

```csharp
public enum EndType { Head, Tail };
```

## 连接点的计算

当组织总线连接时，建议的连接点作为参数（`pt`）传递。这个点可以是精确计算的点、不那么精确计算的点、甚至是一个任意点。实际的连接点（`ptCon`）被计算为总线上最接近建议点（`pt`）的点。`Auxi_Geometry.Polyline_NearestPoint()` 方法不仅返回连接将要在其上组织的精确点（`ptCon`），还返回该点所属的总线线段编号（`iSegment`）以及该点在此线段上的定位系数（`coefOnSegment`）。总线的所有特殊点从头部开始编号为 0，并向尾部递增，因此每个线段的端点有两个连续的编号。沿线段的定位系数取值在 [0, 1] 范围内，其中 0 与编号较小的线段端点关联，1 与另一个端点关联。

## 覆盖层定义

总线连接的几何形状由一个点表示，因此该对象的覆盖层极其简单明了：覆盖层由一个小的圆形节点组成。

```csharp
public override void DefineCover ()
{
    cover = new Cover (new CoverNode (0, ptCon, m_radius));
}
```

## 类的特性

`BusConnection` 类有一个近乎独特的特性：这些对象非常有用，但是它们是不可见的。没有可见性参数，也没有 `Draw()` 方法。我所有的工作都集中在移动屏幕上的对象上，所以这完全关乎改变显示在屏幕上的对象的位置和大小。提到的这个特性近乎独特，到目前为止只有一个类具有此特性：在讨论使用矩形区域绘图时，我解释了在绘图区域角落使用不可见元素的方法。

## 示例：两条连接的总线

我们已经有一套四个小例子来观察自由总线；现在你将看到类似的一套四个例子，来探索总线连接的所有特性。

*   文件：`Form_ConnectedBuses_Two.cs`
*   菜单位置：应用程序 ➤ 家谱设计逐步教程 ➤ 两条连接的总线

这个例子的名称告诉我们，此表单中有两条总线。让我们逐步检查总线之间的连接设计。对于这个和接下来的几个例子，我倾向于这种便于我解释的总线视图。同时，我不想严格确定总线点，因为所讨论的连接机制的工作原理与这些点无关，因此这些点是准随机计算的。为此，示例（表单）的整个区域被 `SubRects()` 方法划分为行和列。

```csharp
void SubRects (int nRows, int nCols)
```

单元格（矩形）的编号从左上角开始，沿第一行向右，然后沿下一行从左到右，依此类推，直到右下角。通过 `RandomPointInSubrect()` 方法获取任何单元格内的随机点。

```csharp
PointF RandomPointInSubrect (int iRect)
```

为了组织准随机的总线定位，你需要声明一组单元格编号，该总线的点必须位于这些单元格内。

```csharp
private List Points_QuaziRandom (int [] nums)
{
    List points = new List ();
    for (int i = 0; i < nums .Length; i++)
    {
        points .Add (RandomPointInSubrect (nums [i]));
    }
    return (points);
}
```

现在我们可以继续我们的例子。首先，在表单的上四分之一部分组织绿色总线（`busGreen`）。然后，蓝色总线（`busBlue`）被放置在表单中间的某个位置，从上到下延伸（图 7-22）。

![图 7-22 两条连接的总线](img/501148_1_En_7_Fig27_HTML.jpg)

图 7-22 两条连接的总线

```csharp
public DefaultView ()
{
    ... ...
    SubRects (4, 5);
    busGreen = new Bus (this, mover,
        Points_QuaziRandom (new int [] { 0, 1, 3, 4 }),
        new Pen (Color .Green, 3));
    busBlue = new Bus (this, mover,
        Points_QuaziRandom (new int [] { 2, 7, 12, 18 }),
        new Pen (Color .Blue, 3));
```

蓝色总线必须通过其头部（即 `points [0]`）连接到绿色总线，因此需要对该点进行一些修正。计算绿色总线上离它最近的点（`pt`），蓝色总线的起点将采用这个值。

```csharp
    PointF pt = Auxi_Geometry .Polyline_NearestPoint (busBlue .Points [0],
        busGreen .Points);
    busBlue .ChangePoint (0, pt);
```

蓝色总线的头点现在位于绿色总线上，但两条总线互不知情。如果不做其他处理，它们的行为就像完全独立的对象；这样的例子在之前几页已经演示过。要实现真正的连接，需要在蓝色总线的头点组织一个 `BusConnection` 对象，并且绿色总线通过 `Bus.AddConnection()` 方法接收关于此连接的信息。

```csharp
    con = new BusConnection (this, mover, pt, busBlue, EndType.Head, busGreen);
    busGreen .AddConnection (con);
    ... ...
}
```

# BusConnection 工作原理

蓝色巴士的终点精确地位于绿色巴士上。巴士之间存在视觉连接；当使用鼠标光标按下连接点时，预期该点可被移动。移动器没有视觉，并根据队列中的顺序选择要移动的对象，因此这个`BusConnection`对象必须在移动器队列中注册在两条巴士之前。

```csharp
private void RenewMover()
{
    mover.Clear();
    busGreen.IntoMover(mover, 0);
    busBlue.IntoMover(mover, 0);
    con.IntoMover(mover, 0);
    // ...
}
```

现在，让我们看看这个连接如何工作的细节。

`BusConnection`对象在移动器队列中位于巴士之前，因此当使用左键按下连接点时，被按下和捕获的不是任何巴士，而是这个`BusConnection`对象。发生这种情况时，会调用`BusConnection.Press()`方法。

```csharp
private void OnMouseDown(object sender, MouseEventArgs e)
{
    ptMouse_Down = e.Location;
    if (mover.Catch(e.Location, e.Button))
    {
        GraphicalObject grobj = mover.CaughtSource;
        int iNode = mover.CaughtNode;
        if (grobj is BusConnection)
        {
            (grobj as BusConnection).Press(e.Location, e.Button, iNode);
        }
        // ...
    }
}
```

这个`Press()`方法中使用三个参数仅仅是为了统一性。对于由单个小圆形节点组成的覆盖层，按下的点和按下的节点号是冗余的。当`BusConnection`元素被左键按下时，鼠标光标位置会以标准方式调整到中心点。

```csharp
public void Press(Point ptMouse, MouseButtons catcher, int iNode)
{
    if (catcher == MouseButtons.Left)
    {
        AdjustCursorPosition(ptCon);
    }
}
```

现在`BusConnection`对象被移动器捕获，因此捕获对象的进一步移动由`BusConnection.MoveNode()`方法描述。

```csharp
public override bool MoveNode(...)
{
    bool bRet = false;
    if (catcher == MouseButtons.Left)
    {
        Location = ptMouse;
        AdjustCursorPosition(ptCon);
    }
    return (bRet);
}
```

从视觉上看，就好像按下的鼠标仅沿绿色巴士移动，并且连接点始终跟随鼠标光标。实际上，其工作原理正好相反：鼠标光标移动到一个点（`ptMouse`），该点极有可能在绿色巴士之外的某个位置，但连接点自身保持位于绿色巴士上，并将光标返回到连接点。将连接点保持在巴士上是使用`Location`属性简单实现的。

```csharp
Location = ptMouse;
```

因此，我们必须查看`BusConnection.Location`属性的内部。

```csharp
public PointF Location
{
    get { return (ptCon); }
    set
    {
        ptCon = Auxi_Geometry.Polyline_NearestPoint(value, m_busRail.Points,
            out dist, out iSegment, out coefOnSegment);
        DefineCover();
        if (end_type == EndType.Head)
        {
            m_busOfEnd.HeadPoint = ptCon;
        }
        else
        {
            m_busOfEnd.TailPoint = ptCon;
        }
    }
}
```

光标移动到某个新点，这个点（`value`）用于计算巴士上最近的点（`ptCon`）。蓝色巴士的被连接端点现在移动到该点。任何巴士的两个端点——头部和尾部——在视觉上是无法区分的，但`BusConnection`对象的`end_type`字段指定了被覆盖的端点，因此将蓝色巴士的正确端点移动到巴士连接的新点没有问题。当`BusConnection.Location`属性的工作完成后，`MoveNode()`方法通过通常的`AdjustCursorPosition()`方法，命令将光标位置调整到连接点的新位置。

连接点的新位置由`Auxi_Geometry.Polyline_NearestPoint()`方法确定，该方法使用一组特殊的巴士点。通过这种方式，连接点仅沿绿色巴士移动，而蓝色巴士的被连接端点随此点一起移动。当绿色巴士的特殊点集合固定时，这能完美工作，但当绿色巴士的配置以某种方式改变时会发生什么？绿色巴士的端点和连接点可以随时移动。在这些变化过程中，蓝色巴士的端点如何保持与绿色巴士的连接？

我已经提到过，在蓝色巴士端点处的连接建立后，有关此连接的信息被传递给了它所连接的巴士——绿色巴士。

```csharp
busGreen.AddConnection(con);
```

任何巴士都会跟踪其所有“侧向”连接。对于其他巴士通过头部和尾部建立的连接，有两个独立的`List`。

```csharp
public class Bus : GraphicalObject
{
    List headsConnected = new List();
    List tailsConnected = new List();
```

`Bus.AddConnection()`方法将一个新元素添加到相应的`List`中。

```csharp
public void AddConnection(BusConnection cnct)
{
    if (cnct.ConnectionType == EndType.Head)
    {
        headsConnected.Add(cnct);
    }
    else
    {
        tailsConnected.Add(cnct);
    }
}
```

当绿色巴士被组织起来时，它的四个点从左到右定义；它们在点`List`中的编号如图 7-23 所示。绿色巴士由三段组成；蓝色巴士连接在点 1 和点 2 之间的第 1 段上。现在，让我们按下并移动绿色巴士的某个特殊点。当巴士的任何一个节点被按下并移动时，就会调用`Bus.MoveNode()`方法。目前我们只关心该方法中负责移动特殊点的部分。所有特殊点都被圆形节点覆盖，这些节点是巴士覆盖层中的第一个，因此移动的圆形节点的编号等于相应特殊点的编号。我想提醒您，只有当巴士本身不可移动时，特殊点才能被移动。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig28_HTML.jpg](img/501148_1_En_7_Fig28_HTML.jpg)

**图 7-23** 绿色巴士的点从左到右编号

```csharp
public override bool MoveNode(...)
{
    bool bRet = false;
    if (catcher == MouseButtons.Left)
    {
        if (Movable)
        {
            // ...
        }
        else
        {
            if (iNode < m_pts.Count)
            {
                m_pts[iNode] = ptMouse;
                InformConnections(PositionUpdate.Soft, iNode - 1, iNode);
                bRet = true;
            }
            // ...
```

当任何巴士移动或重新配置时，该巴士上的部分或全部连接必须更新其位置。`PositionUpdate`枚举描述了两种不同类型的更新。

*   想象一种情况（这是真实的，但我们尚未讨论这种情况）：在巴士配置未发生任何变化的情况下移动巴士，所有连接必须保持其相对位置。这意味着每个连接仍然位于同一段上，并且沿该段的定位系数相同。因此，仅因为巴士移动而改变连接的绝对屏幕坐标；这种位置变化被描述为`PositionUpdate.Soft`。

*   另一种情况发生在，例如，当命令删除巴士的所有连接点并将其转变为一条直线时。结果是，所有连接必须定位在这条新巴士上，并且无法保持连接位于同一段且具有相同的定位系数。这两个值必须重新计算，这种情况被描述为`PositionUpdate.Hard`。

```csharp
public enum PositionUpdate { Soft, Hard };
```

- 当整个总线被移动时，其所有连接都必须重新定位。当端点被移动时，仅影响一个区段。当任意关节被移动时，仅该关节两侧的区段发生改变，且只需重新定位这些区段上的连接；邻近关节之外的所有其他连接完全不受影响。更新的类型及受影响区段的范围会被发送至`InformConnections()`方法。

```csharp
public void InformConnections(PositionUpdate updateType,
    int iSeg_0, int iSeg_1)
{
    foreach (BusConnection connection in headsConnected)
    {
        connection.RailChanged(updateType, iSeg_0, iSeg_1);
    }
    foreach (BusConnection connection in tailsConnected)
    {
        connection.RailChanged(updateType, iSeg_0, iSeg_1);
    }
}
```

传递给`Bus.InformConnections()`方法的参数包括调整类型（`updateType`）以及受影响区段的范围（从`iSeg_0`到`iSeg_1`）。在对每个连接应用`BusConnection.RailChanged()`方法时，会使用相同的参数集。该方法需根据其所在总线的变化计算连接的新位置；仅当特定连接位于指定范围内的区段上时，才需要进行计算。若需移动连接，其新位置（`ptNew`）取决于更新的类型。

- 对于`PositionUpdate.Soft`，区段编号（`iSegment`）及该区段上的定位系数（`coefOnSegment`）保持不变；新位置通过使用这些不变的值计算得出。

- 对于`PositionUpdate.Hard`，新位置通过查找整个总线上的最近点来计算；此计算仅需总线的点。在此过程中，新的区段编号及该区段上的定位系数也会被计算出来。

```csharp
public void RailChanged(PositionUpdate updateType, int iSeg_0, int iSeg_1)
{
    if (iSeg_0 <= iSegment && iSegment <= iSeg_1)
    {
        PointF ptNew;
        if (updateType == PositionUpdate.Soft)
        {
            ptNew = Auxi_Geometry.PointOnLine(m_busRail.Points[iSegment],
                m_busRail.Points[iSegment + 1], coefOnSegment);
        }
        else
        {
            ptNew = Auxi_Geometry.Polyline_NearestPoint(ptCon,
                m_busRail.Points, out dist, out iSegment, out coefOnSegment);
        }
        Location = ptNew;
    }
}
```

计算得到的位置（`ptNew`）被传递给`BusConnection.Location`属性，该属性会完成两件事：将连接重新定位到新位置，然后将此值发送至所连接总线的`HeadPoint`或`TailPoint`属性；具体使用哪个属性取决于该总线是通过头还是尾进行连接。（`BusConnection.Location`属性的代码已在前面几页中展示。）

在我们的案例中，蓝色总线通过头部连接到绿色总线，因此移动后的连接会将新的点值发送至所连接总线的`Bus.HeadPoint`属性，随后该总线的头部端点发生改变。

```csharp
public PointF HeadPoint
{
    get { return (m_pts[0]); }
    set { ChangePoint(0, value); }
}
```

您会看到，并非直接修改`m_pts[0]`，而是调用了`Bus.ChangePoint()`方法，因为仅改变所连接总线的头部端点可能不够。如果第二辆总线上存在一些连接（假设其他总线也连接到蓝色总线），那么`Bus.ChangePoint()`方法不仅会重新定位总线的第一个点，还会扰动其所有连接；这种扰动最终会在某处停止，但在此过程中，所有涉及的连接和总线端点都会占据新的位置。

```csharp
public void ChangePoint(int iPoint, PointF pt)
{
    if (0 <= iPoint && iPoint < m_pts.Count)
    {
        m_pts.RemoveAt(iPoint);
        m_pts.Insert(iPoint, pt);
        DefineCover();
        InformConnections(PositionUpdate.Soft, iPoint - 1, iPoint);
    }
}
```

至此，我们已全面了解当绿色总线的某个特殊点被捕获并移动时会发生什么。对于位于移动特殊点附近或远处的连接，会产生不同的后果。为避免在文本中来回跳跃，我在此复制了图 7-23，该图显示了绿色总线特殊点的编号。现在，我们来详细观察移动点 1 和点 3 两种情况。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Figa_HTML.jpg](img/501148_1_En_7_Figa_HTML.jpg)

当移动绿色总线左侧第二个点（编号 1）时，会发生以下情况：

1.  鼠标移动到某个位置（`ptMouse`）。通过`Bus.MoveNode()`方法，捕获的关节（`iNode == 1`）被移动到与鼠标相同的位置。绿色总线的所有连接被告知总线的两个区段已发生改变。

```csharp
m_pts[1] = ptMouse;
InformConnections(PositionUpdate.Soft, 0, 1);
```

2.  绿色总线只有一个连接，且使用相同的参数调用了该连接的`BusConnection.RailChanged()`方法。

```csharp
connection.RailChanged(PositionUpdate.Soft, 0, 1);
```

3.  我们的连接恰好位于指定的区段范围内（`iSegment == 1`），因此使用软更新计算连接的新位置，然后将该新位置传递给`BusConnection.Location`属性。

```csharp
ptNew = Auxi_Geometry.PointOnLine(busRail.Points[1],
    busRail.Points[2], coefOnSegment);
Location = ptNew;
```

4.  `BusConnection.Location`属性更改连接点的实际位置，并将此新位置发送给蓝色总线，用于`Bus.HeadPoint`属性。

```csharp
ptCon = Auxi_Geometry.Polyline_NearestPoint(value, busRail.Points,
    out dist, out iSegment, out coefOnSegment);
m_busOfEnd.HeadPoint = ptCon;
```

5.  `Bus.HeadPoint`属性调用`Bus.ChangePoint()`方法来更改第一个点的位置。

```csharp
set { ChangePoint(0, value); }
```

6.  `Bus.ChangePoint()`方法更改蓝色总线头部端点的位置，由于蓝色总线上没有连接，扰动至此结束。

当移动绿色总线的右侧点（编号 3）时，事件链更短。

1.  鼠标移动到某个位置（`ptMouse`）。通过`Bus.MoveNode()`方法，捕获的总线点（`iNode == 3`）被移动到与鼠标相同的位置。绿色总线的所有连接被告知总线的两个区段已发生改变。

# 总线连接与InformConnections方法

## 1. 端点移动与连接更新

```csharp
pts[3] = ptMouse;
InformConnections(PositionUpdate.Soft, 2, 3);
```

## 2. 单一连接的总线

绿色总线只有一个连接，且使用相同的参数调用了该连接的`BusConnection.RailChanged()`方法。

```csharp
connection.RailChanged(PositionUpdate.Soft, 2, 3);
```

## 3. 连接位于范围之外

绿色总线上的连接位于指定范围之外（`iSegment == 1`），因此无需执行任何操作。在图 7-23 中，绿色总线右侧点的移动对所示的连接没有影响。

我们已经看到，每当总线的端点或某个关节被移动时，都必须调用`Bus.InformConnections()`方法。接下来的示例将展示必须调用此方法的几种情况，并且存在一些简单的逻辑来确定此调用所需的参数集。

### 排版后的文本

如果某个特殊点被鼠标移动，则总线上任何连接的相对位置不会改变；这意味着使用`PositionUpdate.Soft`参数。作为上述移动的结果，影响不超过两个连续线段，因此两个连续数字代表受影响线段的范围。对于端点，只有一个线段被改变，但为了代码的统一性，线段范围仍然由两个连续数字表示。其中一个所述的线段不存在，但这完全不是问题，因为没有连接可以定位在不存在的线段上，因此对于这个不存在的线段不需要（也不会）采取任何操作。

如果关节数量发生变化，则始终使用`PositionUpdate.Hard`参数，并且所有线段都必须包含在受影响线段的范围内。

属于第二种变体的情况之一是当按下不可移动总线的任何线段时添加新关节；在这种情况下，对`Bus.InformConnections()`方法的调用包含在`OnMouseDown()`方法中。

-   文件：`Form_ConnectedBuses_Three.cs`
-   菜单位置：`Applications ➤ FamilyTree design step by step ➤ Three connected buses`

```csharp
private void OnMouseDown (object sender, MouseEventArgs e)
{
ptMouse_Down = e .Location;
if (mover .Catch (e .Location, e .Button))
{
GraphicalObject grobj = mover .CaughtSource;
int iNode = mover .CaughtNode;
... ...
else if (grobj is Bus)
{
Bus bus = grobj as Bus;
bus .Press (e .Location, e .Button, iNode);
if (e .Button == MouseButtons .Left &&
mover .CaughtNodeShape == NodeShape .Strip && !bus .Movable)
{
... ...
bus .InformConnections (PositionUpdate .Hard,
0, bus .PointsNumber - 1);
... ...
```

在下一个示例中，有三条总线，其中一条两端都连接到其他总线（图 7-24）。此示例允许您检查总线两端连接的正确性，而其他所有内容几乎相同。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig29_HTML.jpg](img/501148_1_En_7_Fig29_HTML.jpg)

图 7-24 三条连接的总线

从此示例开始，直到开发真正的 `Family Tree` 应用程序，将会有大量的总线连接。在某些情况下，总线只有一端连接到某个对象；在其他情况下，总线两端都连接。`ConnectedBus()`方法在任何情况下都很有用。此方法构造一条新总线，其两端连接到一个或两个其他总线。不仅组织了一条新总线，新的连接（一个或两个）也被包含在现有的总线连接`List`中。我提到一个或两个新连接，因为此方法可以在新总线两端都连接或仅一端连接时使用。本可以在上一个示例中演示此方法，但我决定将其使用推迟到本示例。

此示例的总线以准随机方式计算：绿色总线（`busFrom`）出现在窗体的上半部分，蓝色总线（`busTo`）出现在下半部分，然后棕色总线（`busLink`）在中间某处连接它们。

```csharp
List connections = new List ();
public DefaultView ()
{
... ...
busFrom = new Bus (this, mover,
Points_QuaziRandom (new int [] { 0, 1, 3, 4 }),
new Pen (Color .Green, 3));
busTo = new Bus (this, mover,
Points_QuaziRandom (new int [] { 15, 17, 19 }),
new Pen (Color .Blue, 3));
busLink = ConnectedBus (this, mover, new Pen (Color .Brown, 3),
busFrom, busTo,
Points_QuaziRandom (new int [] { 2, 7, 13, 18 }),
out connections);
... ...
```

`ConnectedBus()`方法获取提议的点`List`，但第一个和最后一个点会根据新连接必须位于的总线的几何形状进行调整。`busLink`两端必须连接到的两条总线在调用`ConnectedBus()`方法之前已经存在。家谱通常是通过一次添加一个新元素来构建的，因此新总线一端连接而另一端稍后使用是很正常的。如果使用此方法来组织单端连接，则对于另一端的总线，使用`null`参数代替。

```csharp
public Bus ConnectedBus (Form frm, Mover mvr, Pen pn,
Bus busHeadConnected, Bus busTailConnected,
List points, out List cons)
{
List newcons = new List ();
int iLast = points .Count - 1;
if (busHeadConnected != null)
{
points [0] = Auxi_Geometry .Polyline_NearestPoint (points [0],
busHeadConnected .Points);
}
if (busTailConnected != null)
{
points [iLast] = Auxi_Geometry .Polyline_NearestPoint (points [iLast], busTailConnected .Points);
}
Bus bs = new Bus (frm, mvr, points, pn);
if (busHeadConnected != null)
{
BusConnection conHead = new BusConnection (frm, mvr, points [0],
Math .Max (busHeadConnected .NodeRadius, bs .NodeRadius), bs, EndType .Head, busHeadConnected);
busHeadConnected .AddConnection (conHead);
newcons .Add (conHead);
}
if (busTailConnected != null)
{
BusConnection conTail = new BusConnection (frm, mvr, points [iLast],
Math .Max (busTailConnected .NodeRadius, bs .NodeRadius), bs, EndType .Tail,
busTailConnected);
busTailConnected .AddConnection (conTail);
newcons .Add (conTail);
}
cons = newcons;
return (bs);
}
```

您可以用总线测试另一件有趣的事情。重新配置一条总线，例如绿色的那条，使其变成一个闭环。这很容易做到，因为所有特殊点都是可移动的，并且可以添加任意数量的新关节。在所有更改结束时，将绿色总线的一个端点移动到另一个端点之上。如果您将绿色总线变成一个闭环，那么棕色总线的连接端点可以毫无问题地绕此闭环移动。您甚至可以将绿色总线的两个端点放置在彼此附近，存在一个小间隙不是问题：棕色总线的可移动端点可以轻松跨越绿色总线中的小间隙。如果绿色总线被重新配置为自身交叉，那么棕色总线的端点可以从一个线段跳到另一个线段。您可以重新配置用作轨道的总线，使其不同的线段彼此相距不远；沿着此轨道移动的另一条总线的端点可以轻松跨越间隙并从一条线段移动到另一条线段。这种不仅沿轨道移动，甚至跨越某些间隙的移动，在其他沿轨道移动的应用程序中可能是个问题，因此该问题及其解决方案在我的另一本书 [2] 中进行了讨论（例如，请参阅`Form_SpotOnCommentedWay.cs`）。对于`Family Tree`应用程序，无需添加任何特殊检查来防止这种从总线的一个线段到另一个线段的跳跃。

我无法想象任何情况下有人需要以自身交叉的方式更改家谱的总线，但闭环形式的总线则不同。闭环形式的总线不仅仅是一个有趣的练习。稍后您将看到它是一个有用的元素。

-   文件：`Form_ConnectedBuses_ChangingMovability.cs`
-   菜单位置：`Applications ➤ FamilyTree design step by step ➤ Connected buses; changing movability`

在下一个示例中，三辆相同的总线以相同方式连接，但我添加了一个包含单条命令的小菜单，用于更改被按压总线的可移动性。该命令并非适用于所有总线，因此它对绿色和蓝色总线有效，但对棕色总线无效。当然，这种排除并非基于总线颜色。前面已经解释过，只有两端自由的总线才能被声明为可移动，因此棕色总线不允许更改可移动性。

在初始化时，三辆总线均不可移动。当你用左键按压任意总线的任意段时，光标下方会出现一个新节点。你继续移动这个新节点，如果被按压的总线是绿色或蓝色的，那么棕色总线的连接点只有恰好处在该节点的相邻段上时才会移动。

现在在绿色总线上调用上下文菜单，并将其变为可移动。绿色总线的视图会稍作改变以指示其可移动性；可移动总线所有特殊点上彩色标记的消失（图 7-25）已在类似自由总线的示例中展示过。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig30_HTML.jpg](img/501148_1_En_7_Fig30_HTML.jpg)

图 7-25 两辆总线（蓝色和棕色）不可移动，而绿色总线可移动

当总线可移动性切换时，不仅总线视图发生变化，其覆盖层也随之改变。所有节点的数量、顺序和形状均保持不变，但带状节点的行为会发生变化。

```csharp
public override void DefineCover ()
{
... ...
if (Movable)
{
cover .SetBehaviour (NodeShape .Strip, Behaviour .Moveable);
cover .SetCursor (Cursors .SizeAll);
}
}
```

可移动总线在特殊点上没有标记，任何总线点都可以用来启动对整个总线的完全相同的移动。在本书的多个类中，我已经演示过，如果在某些情况下不需要圆形节点，那么它们会被简单地压缩到大小为零。为什么不在`Bus`类中这样做呢？有些情况下，这些圆形节点必须从移动过程中排除，但必须在它们上面调用某些菜单。为了被移动器检测到，节点必须具有非零的大小。

当用左键按压可移动总线时，`MoveNode()`方法会直接调用`Move()`方法。在讨论自由总线时已经提到过这一点，但现在讨论`MoveNode()`方法中的另一条命令还为时过早。

```csharp
public override bool MoveNode (...)
{
bool bRet = false;
if (catcher == MouseButtons .Left)
{
if (Movable)
{
Move (dx, dy);
InformConnections (PositionUpdate .Soft, 0, pts .Count - 1);
}
... ...
```

当前示例中的绿色总线变为可移动状态并被移动时，必须通知其所有连接点关于总线的变化。在整个移动过程中，绿色总线的配置不会改变；其所有段长度相同，因此所有可能的连接到该总线的点必须保持它们的位置。这是`PositionUpdate.Soft`更新类型的经典变体，必须包含所有段，你看到的就是从`MoveNode()`方法内部调用的`InformConnections()`方法。

-   文件：`Form_ConnectedBuses_AllChanges.cs`
-   菜单位置：应用程序 ➤ 族谱逐步设计 ➤ 连接总线；所有更改

此示例演示了可应用于连接总线的所有可能更改；这些更改与`Form_FreeBuses_AllChanges.cs`（图 7-20）中为自由总线演示的更改相同。此示例中可以使用三个菜单；其中两个在总线段和节点上调用（图 7-26）。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig31_HTML.jpg](img/501148_1_En_7_Fig31_HTML.jpg)

图 7-26 带有所有可能更改的连接总线

我认为现在用于族谱应用程序的总线已经开发并测试完毕。是时候看看任何族谱中的其他重要元素了——具体来说，就是代表人物的矩形。

### 家族谱系中的人物

家族谱系中的任何人物都由`Person`类的一个对象表示。在屏幕上，该对象显示为一个包含某些信息的矩形区域（图 7-27）。该矩形是可移动和可调整大小的（您还期待别的什么吗？）；人物的信息通过与此矩形关联的三个评论组织起来。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig32_HTML.jpg](img/501148_1_En_7_Fig32_HTML.jpg)

图 7-27 `Person`对象的标准视图

```csharp
public class Person : GraphicalObject
{
PointF [] ptsCorner;
SolidBrush m_brush;
Pen penBorder = new Pen (Color .DarkGray);
CommentToRect cmntName, cmntBirth, cmntDeath;
Bus busOnBorder;
static int minSide = 20;
```

其中一个评论`cmntName`用于显示人物的姓名。此评论的文本不能为空。即使您不知道人物的姓名，也可以称其为“未知”或任何其他名称，但您不能在没有此评论的情况下组织一个`Person`对象。另外两个评论是非强制的。如果一个人还活着，通常不知道其去世信息，因此在这种情况下不需要`cmntDeath`。对于几代以前的亲属，不知道其出生日期也并不罕见。可以输入带有问号的某个日期（例如年份），或者干脆省略`cmntBirth`。在开始讨论家族谱系程序时，我使用了图 7-10 到 7-14，其中每个`Person`对象只包含`cmntName`部分，而另外两个评论是空的。

姓名、出生和死亡数据只是本程序及其他类似程序中使用的标准信息片段。这三个字段仅是字符串，用户可以在其中输入任何所需的文本。

`Person`类的对象可以通过其内部任意点移动，并通过其边界上任意点调整大小。该类的对象使用了与之前演示的其他几个矩形类相同类型的覆盖层（例如，见图 5-3），因此在角上有四个圆形节点，沿边上有四个条状节点，以及一个覆盖整个对象区域的矩形节点。`Person`对象的覆盖层基于矩形的四个角点；`ptsCorner[]`数组中点的编号从左上角开始，按顺时针方向进行。`Person`对象始终可以在所有方向上调整大小，并且覆盖节点永远不会被压缩。边上的条状节点默认宽度为六个像素，而圆形节点则比默认大小略大。

# Person 对象的实现与调优

## DefineCover 方法

```csharp
public override void DefineCover ()
{
float rCorner = 4;
CoverNode [] nodes = new CoverNode [] {
new CoverNode (0, ptsCorner [0], rCorner, Cursors .SizeNWSE),
new CoverNode (1, ptsCorner [1], rCorner, Cursors .SizeNESW),
new CoverNode (2, ptsCorner [2], rCorner, Cursors .SizeNWSE),
new CoverNode (3, ptsCorner [3], rCorner, Cursors .SizeNESW),
new CoverNode (4, ptsCorner [0], ptsCorner [3], Cursors .SizeWE),
new CoverNode (5, ptsCorner [1], ptsCorner [2], Cursors .SizeWE),
new CoverNode (6, ptsCorner [0], ptsCorner [1], Cursors .SizeNS),
new CoverNode (7, ptsCorner [3], ptsCorner [2], Cursors .SizeNS),
new CoverNode (8, ptsCorner) };
cover = new Cover (nodes);
}
```

## Person 对象的运动特性

这是一个经典的复杂对象，其各个部分参与独立、关联和同步的运动。

-   矩形是主导元素；当其移动时，所有关联的评论都会同步移动。
-   当矩形调整大小时，其关联的评论保持其相对位置。
-   任何评论都可以独立移动和旋转。

与任何复杂对象一样，`Person` 对象必须通过 `Person.IntoMover()` 方法在移动器队列中注册。无论评论相对于矩形的相对位置如何，它们都必须可见且可访问，因此它们必须比矩形更早注册。

## IntoMover 方法

```csharp
new public void IntoMover (Mover mv, int iPos)
{
mv .Insert (iPos, this);
mv .Insert (iPos, cmntName);
if (BirthInfoToShow)
{
mv .Insert (iPos, cmntBirth);
}
if (DeathInfoToShow)
{
mv .Insert (iPos, cmntDeath);
}
}
```

绘制各部分的顺序总是与它们在移动器队列中的放置顺序相反，因此我们必须先绘制矩形，然后再绘制所有需要显示的评论。

-   文件：`Form_Person.cs`
-   菜单位置：应用程序 ➤ 家族谱系逐步设计 ➤ Person

## Draw 方法

```csharp
public void Draw (Graphics grfx)
{
grfx .FillRectangle (m_brush, Area);
grfx .DrawRectangle (penBorder, Rectangle .Round (Area));
cmntName .Draw (grfx);
if (BirthInfoToShow)
{
cmntBirth .Draw (grfx);
}
if (DeathInfoToShow)
{
cmntDeath .Draw (grfx);
}
}
```

## Person 对象的初始化与视图

此示例包含一个 `Person` 对象，并演示了其调优（图 7-28）。`Person` 类具有多个构造函数；当前示例使用了一个包含全部参数集的构造函数。

```csharp
private void DefaultPerson (PointF pt)
{
m_person = new Person (this, mover, new RectangleF (pt .X, pt .Y, 210, 60),
Color .Cyan, "James", "3-Oct-1916", "23-Feb-1995");
}
```

由 `DefaultPerson()` 方法组织的 `Person` 对象具有如图 7-27 所示的视图。矩形的青色由初始化时的参数之一决定，而所有三个评论都显示为水平字符串，并使用相同的字体和颜色。图 7-28 中可见的所有进一步变换都是借助调优表单完成的。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig33_HTML.jpg](img/501148_1_En_7_Fig33_HTML.jpg)

**图 7-28** `Form_Person.cs` 包含一个 `Person` 对象

## 用户交互与调优表单

在任何用户驱动的应用程序中，所有可见性参数都由用户决定。如果屏幕上有一个对象，那么必须有一种简单的方法来更改其视图。可以通过菜单命令打开针对 `Person` 对象的特殊调优表单。通常，每个对象类都有自己的菜单和自己的调优表单；通过点击对象的类可以轻松决定打开所需的菜单或调优表单。当按下 `Person` 对象内部的任何空闲位置时，移动器会通知鼠标按压确实触及到了 `Person` 对象，然后可以调用其菜单（以及最终的调优表单）。当按下 `Person` 对象的任何评论时，移动器会检测到对 `CommentToRect` 对象的按压。对于任何评论，只能更改字体和颜色。我们稍后会介绍到的真正的家族谱系应用程序，会对按下评论或按下 `Person` 区域内的空白位置提供不同的响应。然而，`Person` 对象的调优表单（图 7-29）包含了所有三个评论的调优，因此在此示例及其他几个辅助示例中，在 `Person` 区域或其任何评论内的空白位置右键单击时，会出现相同的反应。

```csharp
private void OnMouseUp (object sender, MouseEventArgs e)
{
ptMouse_Up = e .Location;
double fDist = Auxi_Geometry .Distance (ptMouse_Down, ptMouse_Up);
if (mover .Release ())
{
GraphicalObject grobj = mover .ReleasedSource;
if (e .Button == MouseButtons .Right && fDist <= 3)
{
if (grobj is Person || grobj is CommentToRect)
{
ContextMenuStrip = menuOnPerson;
}
... ...
```

调谐表单（图 7-29）允许你更改矩形的背景颜色，并修改全部三条注释。每条注释的字体和颜色都可以单独设置。出生与死亡信息的显示方式并无严格规定；它们只是字符串，你可以在其中输入任何内容。这样，调谐表单在“家谱”应用中可以正常工作，但在当前示例中存在一个限制。我不希望允许更改这个特定 `Person` 对象的信息，因此调谐表单初始化的最后一个参数会阻止对全部三条注释的编辑。三个文本框的视图清楚地表明编辑已被禁止。

```csharp
private void Click_miModify_Person (object sender, EventArgs e)
{
Form_Tuning_Person frm = new Form_Tuning_Person (
PointToScreen (ptMouse_Up), m_person, false);
... ...
```

## Bus 与 Person 的协同工作

在之前的示例中，我们对 `Bus` 对象进行了大量操作，并了解了如何移动和更改它们。借助 `BusConnection` 对象，可以将总线组织成一个连接总线系统，该系统将用于“家谱”应用。我们还有 `Person` 类，它允许显示家谱中任何人的信息。现在，我们需要弄清楚 `Bus` 和 `Person` 对象如何协同工作。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig34_HTML.jpg](img/501148_1_En_7_Fig34_HTML.jpg)

**图 7-29** Person 对象的调谐表单

在 `Person` 类中，有一个尚未提及的有趣字段。

```csharp
Bus busOnBorder;
```

`Bus.IntoMover()` 方法的代码在前面几页已经展示过。其中没有提及 `busOnBorder`，因此移动器(mover)完全不了解这个元素。如果一个元素没有在移动器中注册，那么这个元素就是不可移动的。同样的总线也没有在 `Bus.Draw()` 方法中出现（该方法也已在前面几页展示过），因此这条总线是不可见的。这个既不可移动又不可见的总线的目的是什么？它到底在哪里使用？

当任何 `Person` 对象被初始化时，其 `busOnBorder` 字段也会被初始化；新总线的点由 `Auxi_Geometry.ClosedPolyline()` 方法准备。

```
public Person (Form frm, Mover mvr, RectangleF rect, Color clrRect , Font fnt, string strName, string strBirth, string strDeath)
{
... ...
float w = Math .Max (minSide, rect .Width);
float h = Math .Max (minSide, rect .Height);
ptsCorner = new PointF [4] {rect .Location,
new PointF (rect .X + w, rect .Y),
new PointF (rect .X + w, rect .Y + h),
new PointF (rect .X, rect .Y + h) };
busOnBorder = new Bus (m_form, null,
Auxi_Geometry .ClosedPolyline (ptsCorner), penBorder);
busOnBorder .RegisteredWithMover = false;
... ...
```

因此，`busOnBorder`被组织成一个闭环，这条不可见的总线沿着矩形的边界延伸。这条总线初始化最显著的特点是传递给`RegisteredWithMover`属性的值。

```
busOnBorder .RegisteredWithMover = false;
```

该属性的名称和传递的值清楚地表明，这条总线不会在移动器中注册。下一个示例展示了这条不可移动且不可见的总线在家谱设计中的作用。

- 文件：`Form_PersonPlusBus.cs`
- 菜单位置：应用程序 ➤ 家谱设计逐步教程 ➤ 人物与连接总线

新示例中再次只有一个`Person`对象，但视图中也有一条总线（图 7-30）。这条总线的一个端点位于`Person`对象的边界上；如果你用左键按住这个点并尝试将其移动到任何地方，你会立刻发现它只能沿着矩形边界移动，无法移动到其他位置。你是否觉得似曾相识？这是否类似于将总线的端点沿着另一条与其连接的总线移动？

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig35_HTML.jpg](img/501148_1_En_7_Fig35_HTML.jpg)

**图 7-30** 带有连接总线的 Person 对象

是的，这正是总线连接到`Person`对象时发生的情况：实际上，这条总线连接的是另一条总线——一条沿着矩形边界延伸的不可见`busOnBorder`。参与连接的两条总线中有一条是不可见的，这完全无关紧要；总线与`Person`对象之间的连接组织方式，与总线之间的连接方式相同，例如在`Form_ConnectedBuses_Two.cs`中。

尽管当前示例中的元素集是最小的——一个`Person`对象和一条可见总线——但这是第一个针对三种元素类型使用三个`List`的示例，而真正的“家谱”应用正是基于这三种元素类型设计的。在接下来的示例中，无论使用多少个元素，你都将看到完全相同的三个`List`。

```
public partial class Form_PersonPlusBus : Form
{
List people = new List ();
List buses = new List ();
List connections = new List ();
```

当`Form_PersonPlusBus.cs`启动时，所有三个列表都被清空，然后`Person`对象被设计出来。

```
private void DefaultView ()
{
people .Clear ();
buses .Clear ();
connections .Clear ();
Person person = new Person (this, mover, new RectangleF (100, 50, 140, 60), Color .Cyan, “Unknown”);
AddPerson (person);
... ...
```

当任何`Person`、`Bus`或`BusConnection`类的新对象被组织起来时，它会被添加到相应的`List`末尾。对于一个新的`Person`对象，两个列表都会增加元素。

```
private void AddPerson (Person prsn)
{
... ...
people .Add (prsn);
AddBus (prsn .Bus);
}
private void AddBus (Bus busNew)
{
... ...
buses .Add (busNew);
}
```

`Person`对象已经组织好，现在是时候组织一条总线了，你可以在图 7-30 中看到它。在其他参数中，`Bus`构造函数接收一个`List`点。此示例中的总线将不是一条自由总线，而是一条连接到`Person`对象边界上不可见总线的总线。新总线端点的预设位置极大概率需要调整，然后必须在计算出的点上组织`BusConnection`对象。所有这些操作都在`BusFromPerson()`方法中完成，该方法直接返回新的总线，并通过参数返回新的连接。

```
private void DefaultView ()
{
... ...
List points = new List ();
points .Add (new PointF (220, 130));
points .Add (new PointF (270, 170));
points .Add (new PointF (410, 90));
BusConnection con;
Bus bus = BusFromPerson (this, mover, new Pen (Color .Blue, 3), person, points, out con);
AddBus (bus);
AddConnection (con);
... ...
private Bus BusFromPerson (Form frm, Mover mvr, Pen pn, Person person,
List points, out BusConnection con)
{
Bus busFrom = person .Bus;
points [0] = Auxi_Geometry .Polyline_NearestPoint (points [0],
busFrom .Points);
Bus bs = new Bus (frm, mvr, points, pn);
con = new BusConnection (frm, mvr, points [0], bs, EndType .Head, busFrom);
busFrom .AddConnection (con);
return (bs);
}
```

所有新元素都被包含到相应的列表中，我们得到如下列表：

| • `List<Person> people` | 包含一个元素 |
| • `List<Bus> buses` | 包含两个元素；其中一个如图 7-30 所示，另一个不可见，沿着`Person`对象的边界延伸 |
| • `List<BusConnection> connections` | 包含一个元素 |

移动器可以处理所有三种已提及类别的元素，因此所有这些元素都必须在移动器中注册，但必须按照正确的顺序注册：`BusConnection`元素必须先于所有`Bus`元素，而`Bus`元素必须先于所有`Person`元素。

此示例中的`RenewMover()`方法将同样用于另外几个示例以及真正的“家谱”应用。

```
private void RenewMover ()
{
    mover .Clear ();
    foreach (Person person in people)
    {
        person .IntoMover (mover, 0);
    }
    foreach (Bus bus in buses)
    {
        bus .IntoMover (mover, 0);
    }
    foreach (BusConnection con in connections)
    {
        con .IntoMover (mover, 0);
    }
    ... ...
```

在 `Form_PersonPlusBus.cs` 中有两辆巴士：一辆在图 7-30 中完全可见，另一辆不可见，且沿着 `Person` 对象的边界移动。两辆巴士都包含在 `List` 公交车列表中，而 `RenewMover()` 方法的代码显示，对于列表中的每辆巴士，都会调用 `Bus.IntoMover()` 方法。同时，我之前提过，无论如何，不可见的 `busOnBorder` 都不应注册到 mover 中。这是如何实现的呢？

当任何 `Person` 对象被初始化时，其 `busOnBorder` 的一个参数会获得一个特殊值。

```csharp
busOnBorder .RegisteredWithMover = false;
```

当调用 `Bus.IntoMover()` 方法时，它会检查这个值，并且仅注册该字段值为合适的巴士。

```csharp
new public void IntoMover (Mover mv, int iPos)
{
    if (iPos < 0 || mv .Count < iPos || !bRegisteredWithMover)
    {
        return;
    }
    mv .Insert (iPos, this);
}
```

因此，尽管巴士列表中有两辆巴士，但只有其中一辆——即可见的那辆——注册到了 mover 中。由于位于 `Person` 对象边界上的不可见巴士未注册到 mover，mover 并不知道这辆巴士的存在，也无法对它进行任何操作。因此，无法向这辆巴士添加任何连接点，也无法用鼠标将其移动到其关联矩形边界上原本位置之外的任何地方。

用鼠标无法移动 `busOnBorder`，但当 `Person` 对象被移动或调整大小时会发生什么？在几页之前，我提到过，任何 `Person` 对象都是一个复杂对象，其主导元素（矩形）与附属评论之间遵循标准的关系规则。当时并未提及边界上的不可见巴士，但它是另一个附属元素，因此必须遵循相同的规则。当通过 `Person.Move()` 和 `Person.MoveNode()` 方法移动或调整矩形大小时，所有附属元素都必须被告知这些变化。这是通过 `Person.InformRelatedElements()` 方法完成的。

```csharp
private void InformRelatedElements ()
{
    cmntName .ParentRect = Area;
    cmntBirth .ParentRect = Area;
    cmntDeath .ParentRect = Area;
    busOnBorder .Points = Auxi_Geometry .ClosedPolyline (ptsCorner);
}
```

正如您从这段代码中看到的，无论区域如何变化，`busOnBorder` 元素始终沿着区域边界延伸。我们在之前的示例中已经讨论过，连接点如何始终保持在巴士上，无论巴士如何变化。当一辆可见巴士连接到一辆不可见巴士时，同样的机制也在起作用，因此在矩形区域的任何变化过程中，连接点都保持其在边界上的位置，因此在视觉上，巴士始终连接到矩形的边界。

关于 `Form_PersonPlusBus.cs` 还有一点补充。连接的巴士初始化为蓝色，如下所示：

```csharp
private void DefaultView ()
{
    ... ...
    Bus bus = BusFromPerson (this, mover, new Pen (Color .Blue, 3), person, points, out con);
```

然而，它显示为品红色（图 7-30），并且在 `Form_PersonPlusBus.cs` 中没有更改颜色的代码行。是什么导致了这种颜色变化？

图 7-30 中的巴士一端连接到 `Person` 对象，另一端是空闲的。对于真正的家谱来说，这不是正常情况；您无法想象一辆连接着某种事物与虚无的巴士；这是荒谬的。如果在真实家谱中出现这种情况，说明是一个未完成的构建，用户必须被告知这种异常情况。吸引用户注意异常情况的最佳方法是更改元素的颜色；这将是一个信号，表明用户需要执行某些操作。因此，`Bus` 对象中有一个特殊的 `penWarn` 字段。默认情况下，在这种异常情况下，巴士的这支笔颜色为品红色。

```csharp
Pen penWarn = new Pen (Color .Magenta, 2);
```

在某些情况下，巴士会使用这支特殊的笔来绘制，而非其真实颜色。

```csharp
public void Draw (Graphics grfx)
{
    if (((bHeadConnected != bTailConnected) ||
    (bHeadConnected == false && bTailConnected == false &&
    headsConnected .Count == 0 && tailsConnected .Count == 0 &&
    bRegisteredWithMover)) && bUseWarningColor)
    {
        grfx .DrawLines (penWarn, pts .ToArray ());
    }
    else
    {
        grfx .DrawLines (m_pen, pts .ToArray ());
    }
    ... ...
```

这种特殊警告颜色的使用可以很容易地通过 `Bus.UseWarningColor` 属性来调节。在第一个关于连接巴士的示例中，我希望两辆巴士都显示其自然（声明）的颜色，因此蓝色巴士的颜色警告被关闭。

*   文件：`Form_Spouses.cs`
*   菜单位置：Applications ➤ FamilyTree design step by step ➤ Spouses

```csharp
public Form_ConnectedBuses_Two ()
{
    ... ...
    busBlue = new Bus (...);
    ... ...
    busBlue .UseWarningColor = false;
    ... ...
```

这个示例使我们更接近家谱设计的目标，它代表了一个简单的树结构，由三个 `Person` 对象和它们之间的两辆巴士组成（图 7-31）。无论树结构多么复杂，只需为三个类各维护三个 `List` 就足够了，并且对这些 `List` 的所有操作都可以通过这样一个简单的家谱进行验证和演示。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig36_HTML.jpg](img/501148_1_En_7_Fig36_HTML.jpg)

图 7-31

一个原始的家谱

```csharp
List people = new List ();
List buses = new List ();
List connections = new List ();
```

对于这样一个小型树结构，更简单的方法是先组织所有需要的 `Person` 对象，然后用巴士将它们连接起来。

```csharp
private void DefaultView ()
{
    ... ...
    people .Clear ();
    buses .Clear ();
    connections .Clear ();
    float w = 180;
    float h = 60;
    Person prsnWife_1 = new Person (this, mover, new RectangleF (80, 60, w, h), Color .Cyan, fntSelected, "Sura-Leya", "1865", "1894");
    Person prsnHusband = new Person (this, mover,
        new RectangleF (320, 60, w, h), Color .Yellow,
        fntSelected, "Joseph", "1862", "1939");
    Person prsnWife_2 = new Person (this, mover,
        new RectangleF (600, 100, w, h), Color .Cyan, fntSelected, "Gesya", "1881", "1957");
    AddPerson (prsnWife_1);
    AddPerson (prsnHusband);
    AddPerson (prsnWife_2);
    ... ...
```

两个 `Person` 对象之间的直接连接在家谱设计中经常使用，因此使用一个专门的 `LinkPeople()` 方法在这种情况中会很有用。

```csharp
Bus bus = LinkPeople (this, mover, new Pen (Color .Blue, 3),
    prsnWife_1, Side .E, 0.4, prsnHusband, Side .W, 0.4);
AddBus (bus);
bus = LinkPeople (this, mover, new Pen (Color .Blue, 3),
    prsnHusband, Side .E, 0.5, prsnWife_2, Side .W, 0.5);
AddBus (bus);
... ...
```

由 `LinkPeople()` 方法准备的巴士由单个线段组成，因此图 7-31 显示的并不是默认视图；第二辆巴士是用鼠标按过的，并且新的连接点被移动到了新位置。

## `LinkPeople()` 方法详解

`LinkPeople()` 方法不仅准备了两条 `Person` 对象之间的直线连接，还在新总线的两端创建了两个新的 `BusConnection` 元素，这些元素必须放置在矩形的两侧。每个连接点的初始位置由矩形边以及沿该边的定位系数决定。该系数取值在 `[0, 1]` 范围内。对于水平边，系数从左到右增加；对于垂直边，系数从上到下增加。

```csharp
private Bus LinkPeople (Form frm, Mover mvr, Pen pn,
Person personFrom, Side side_From, double coef_From,
Person personTo, Side side_To, double coef_To)
{
Bus busFrom = personFrom .Bus;
PointF ptA = Auxi_Geometry .PointOnRectangleBorder (personFrom .Area,
side_From, coef_From);
ptA = Auxi_Geometry .Polyline_NearestPoint (ptA, busFrom .Points);
Bus busTo = personTo .Bus;
PointF ptB = Auxi_Geometry .PointOnRectangleBorder (personTo .Area,
side_To, coef_To);
ptB = Auxi_Geometry .Polyline_NearestPoint (ptB, busTo .Points);
Bus bs = new Bus (frm, mvr, ptA, ptB, pn);
List conecs = new List ();
conecs .Add (new BusConnection (frm, mvr, ptA, bs, EndType.Head, busFrom));
conecs .Add (new BusConnection (frm, mvr, ptB, bs, EndType .Tail, busTo));
busFrom .AddConnection (conecs [0]);
busTo .AddConnection (conecs [1]);
connections .AddRange (conecs);
return (bs);
}
```

## `Person` 对象的调优

该示例中没有总线调优，而 `Person` 对象的调优则借助已演示过的调优表单（图 7-29）完成。调优通过菜单命令启动；无论按下的是 `Person` 对象内部的空白区域还是其某个注释，调用的菜单都是相同的，但在任何情况下，都必须标识出该 `Person` 对象。当按下空白区域时，表示直接点击了 `Person` 对象（`personPressed`）。当按下注释时，则表示点击了某个 `CommentToRect` 对象。该注释是某个 `Person` 对象的下属，因此注释保留了其主导对象的 ID。通过快速搜索所有 `Person` 对象即可返回所需的对象。

```csharp
private void OnMouseUp (object sender, MouseEventArgs e)
{
... ...
if (mover .Release (out iWasObject, out iWasNode, out shapeNode))
{
GraphicalObject grobj = mover .ReleasedSource;
if (e .Button == MouseButtons .Right && fDist <= 3)
{
... ...
else if (grobj is CommentToRect)
{
long idPerson = grobj .ParentID;
foreach (Person prsn in people)
{
if (prsn .ID == idPerson)
{
personPressed = prsn;
break;
}
}
ContextMenuStrip = menuOnPerson;
... ...
```

## 两代人家谱示例

保存和恢复家谱的过程不依赖于家谱的大小，下一个示例将对此进行说明。

*   文件：`Form_TwoGenerations.cs`
*   菜单位置：应用 ➤ 家族树逐步设计 ➤ 两代人

这是设计完整家族树应用之前的最后一个预备示例。实际上，这并不是一个用于演示某种特性的示例，而是一个真实的（尽管规模较小）家谱，它只有一个限制：构建完这个小家谱后，没有添加新人的命令。图 7-32 显示只有来自两代人的四个人。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig37_HTML.jpg](img/501148_1_En_7_Fig37_HTML.jpg)

图 7-32 一个两代人的小家谱

尽管这个家谱很小，但它代表了所有可能类型的总线。总线的分类可以基于它们到不同对象的连接。为了更好地解释，我使用不同颜色来区分不同类型的总线。在图 7-17 中，相同的四种总线类型用字母 *A*、*B*、*C* 和 *S* 标记。在当前示例中，没有使用这种字母标记，但新解释中的定义顺序与旧解释相同。

*   **两个 `Person` 对象之间的总线**：在图 7-32 中，它是蓝色的。视觉上它是在两个人之间，但实际上，这条总线连接到这些 `Person` 对象边界上的不可见总线。
*   **总线与 `Person` 对象之间的总线**：在图 7-32 中，有两条这种类型的绿色总线。实际上，每条都在两条总线之间，但其中一条是不可见的。
*   **两条总线之间的总线**：在图 7-32 中，它是灰色的。
*   **两端无连接的总线**：在图 7-32 中，它是棕色的。

任何家谱，即使像这样简单，也可以用不同的方式构建。这个家谱的设计从两个代表父母的 `Person` 对象以及它们之间的总线开始；这条总线借助上一个示例中使用的 `LinkPeople()` 方法构建。

```csharp
private void DefaultView ()
{
float w = 150;
float h = 60;
Person father = new Person (this, mover, new RectangleF (140, 60, w, h),
Color .Cyan, fntSelected,
"James", "1916", "1995");
AddPerson (father);
Person mother = new Person (this, mover, new RectangleF (380, 60, w, h),
Color .LightPink, fntSelected,
"Joan", "1919", "1999");
AddPerson (mother);
Bus busParents = LinkPeople (this, mover, new Pen (Color .Blue, 3),
father, Side .E, 0.5, mother, Side .W, 0.5);
AddBus (busParents);
... ...
```

下一步，设计另外两个代表子女的 `Person` 对象。它们上方的一条总线（棕色）被初始化，但所有到这条总线的链接将稍后添加。

```csharp
float cyChildren = father .Bottom + 100;
Person son = new Person (this, mover,
new RectangleF (father .Left - w / 2, cyChildren, w, h),
Color .Cyan, fntSelected, "James", "Feb-1943", "");
Person daughter = new Person (this, mover,
new RectangleF (mother .Right - w / 2, son .Top, w, h),
Color .LightPink, fntSelected, "Rosemary",
"May-1947", "");
AddPerson (son);
AddPerson (daughter);
float cyAbove = (father .Bottom + son .Top) / 2;
float cxL = Auxi_Geometry .Middle (son .Area) .X;
float cxR = Auxi_Geometry .Middle (daughter .Area) .X;
Bus busAbove = new Bus (this, mover, new PointF (cxL - 40, cyAbove),
new PointF (cxR + 40, cyAbove),
new Pen (Color .Brown, 3));
AddBus (busAbove);
```

现在是时候将“子女”与它们上方的总线（棕色）连接起来了。每条绿色总线都由`LinkBusPerson()`方法组织。从方法名称可以看出，新总线充当了现有总线与`Person`对象之间的链接。

```
AddBus (LinkBusPerson (this, mover, new Pen (Color .Green, 3), busAbove, new PointF (cxL, cyAbove), son, Side .N, 0.5));
AddBus (LinkBusPerson (this, mover, new Pen (Color .Green, 3), busAbove, new PointF (cxR, cyAbove), daughter, Side .N, 0.5));
```

新总线通过其头部连接到现有总线（此处为棕色）。现有总线上的连接点作为参数（`ptFrom`）传入，但这只是一个优选的连接点，实际连接点将在方法内部计算。新总线通过其尾部连接到`Person`对象；连接点（`ptTo`）由矩形边和沿该边的定位系数描述；这与`LinkPeople()`方法中的方式相同。

```csharp
private Bus LinkBusPerson (Form frm, Mover mvr, Pen pn,
Bus busFrom, PointF ptFrom,
Person personTo, Side side_To, double coef_To)
{
ptFrom = Auxi_Geometry .Polyline_NearestPoint (ptFrom, busFrom .Points);
Bus busTo = personTo .Bus;
PointF ptTo = Auxi_Geometry .PointOnRectangleBorder (personTo .Area,
side_To, coef_To);
ptTo = Auxi_Geometry .Polyline_NearestPoint (ptTo, busTo .Points);
Bus bs = new Bus (frm, mvr, ptFrom, ptTo, pn);
List conecs = new List ();
conecs .Add (new BusConnection (frm, mvr, ptFrom, bs, EndType .Head,
busFrom));
conecs .Add (new BusConnection (frm, mvr, ptTo, bs, EndType .Tail, busTo));
busFrom .AddConnection (conecs [0]);
busTo .AddConnection (conecs [1]);
connections .AddRange (conecs);
return (bs);
}
```

设计这个小型家谱树的最后一步是构建两代人之间的连线——即灰色总线。

```csharp
private void DefaultView ()
{
... ...
PointF pt = Auxi_Geometry .Middle (busParents .HeadPoint,
busParents .TailPoint);
AddBus (LinkBusBus (this, mover, new Pen (Color .DarkGray, 3), busParents, pt, busAbove, new PointF (pt .X, busAbove .TailPoint .Y)));
... ...
```

两条总线之间的连线由`LinkBusBus()`方法组织。传递给该方法的两个点同样是优选位置，而实际的连接点会在方法内部计算得出。

```csharp
private Bus LinkBusBus (Form frm, Mover mvr, Pen pn,
Bus busFrom, PointF ptFrom, Bus busTo, PointF ptTo)
{
ptFrom = Auxi_Geometry .Polyline_NearestPoint (ptFrom, busFrom .Points);
ptTo = Auxi_Geometry .Polyline_NearestPoint (ptTo, busTo .Points);
Bus bs = new Bus (frm, mvr, ptFrom, ptTo, pn);
List conecs = new List ();
conecs .Add (new BusConnection (frm, mvr, ptFrom, bs, EndType .Head,
busFrom));
conecs .Add (new BusConnection (frm, mvr, ptTo, bs, EndType .Tail, busTo));
busFrom .AddConnection (conecs [0]);
busTo .AddConnection (conecs [1]);
connections .AddRange (conecs);
return (bs);
}
```

当这个小家谱树的设计完成后，会得到以下元素集合：

| `List<Person> people` | 包含四个元素。 |
| --- | --- |
| `List<Bus> buses` | 包含九个元素。其中五个以不同颜色显示，另外四个总线不可见，沿着`Person`对象的边界分布。 |
| `List<BusConnection> connections` | 包含八个元素。它们都以红色小点显示。 |

再次强调，`mover`队列中元素的顺序由`RenewMover()`方法定义。

```csharp
private void RenewMover ()
{
mover .Clear ();
foreach (Person person in people)
{
person .IntoMover (mover, 0);
}
foreach (Bus bus in buses)
{
bus .IntoMover (mover, 0);
}
foreach (BusConnection con in connections)
{
con .IntoMover (mover, 0);
}
... ...
```

如上述代码所示，首先放入所有`BusConnection`元素，然后是所有`Bus`元素，最后是所有`Person`元素。`BusConnection`元素必须位于队列头部；否则，其他元素可能会遮挡连接点，导致无法移动连接点。`Bus`和`Person`对象的顺序并不那么重要，可以更改。`mover`队列中的元素顺序与其绘制顺序相关，因此这完全取决于当总线穿过`Person`对象的矩形区域时，你希望看到怎样的视觉效果。如果你希望总线显示在任何`Person`对象之上，并且能够按下总线并将其移开，那么当前的编码顺序是正确的。如果你希望总线位于`Person`对象下方，则需要同时修改`RenewMover()`和`OnPaint()`方法。

棕色总线是这个家谱树中唯一可以声明为可移动的总线（通过其上下文菜单的命令），因为它是唯一两端都空闲的总线。如果你将棕色总线声明为可移动，那么你就可以移动它，例如，使其更靠近上方或下方的`Person`对象，并且在移动过程中，该总线上所有的连接点（共有三个）将保持其相对位置。

此示例中出现了两个新特性。在这个小树中它们并不太需要，但在真正的大型家谱树中将很有帮助。

第一个特性是将家谱树保存到二进制文件中，并从该文件中恢复家谱树；这是通过一个小型文件菜单的命令完成的。

第二个新特性是能够在屏幕上移动整个家谱树。对于这样小的树来说，这个特性完全不需要，但在真正的大型家谱树中它非常有用。首先，没有规则规定你实际家谱树的构建顺序。你可以从最遥远的已知祖先开始，一代一代向下构建，或者将你自己置于屏幕中央，并向各个方向开始添加亲属及其连线。无论如何，很可能即使是最大的屏幕也不够用，你将需要滚动屏幕。这只需在任意空白处按下左键，并向所需方向拖动即可实现。这个工具很简单。

有一个布尔字段用于指示当前是否正在移动整个家谱树。

```csharp
bool bMoveAll = false;
```

当在任意空白处按下左键时，该字段的值会改变，并记录当前鼠标位置（`ptMouse_prev`）。

```csharp
private void OnMouseDown (object sender, MouseEventArgs e)
{
ptMouse_Down = e .Location;
if (mover .Catch (e .Location, e .Button))
{
... ...
}
else
{
if (e .Button == MouseButtons .Left)
{
bMoveAll = true;
ptMouse_prev = e .Location;
}
}
... ...
```

# 家谱应用程序设计与实现

当 `mover` 未捕捉到任何对象，但布尔参数 `bMoveAll` 指示所有元素正在移动时，会计算当前鼠标位置与前一个位置之间的距离；这一对数值（`dx, dy`）将用于同步移动所有元素。

```csharp
private void OnMouseMove (object sender, MouseEventArgs e)
{
    if (mover .Move (e .Location))
    {
        Invalidate ();
    }
    else
    {
        if (bMoveAll)
        {
            if (ptMouse_prev != e .Location)
            {
                int dx = e .X - ptMouse_prev .X;
                int dy = e .Y - ptMouse_prev .Y;
                foreach (Person person in people)
                {
                    person .Move (dx, dy);
                }
                foreach (Bus bus in buses)
                {
                    bus .Move (dx, dy);
                }
                foreach (BusConnection con in connections)
                {
                    con .Move (dx, dy);
                }
                ptMouse_prev = e .Location;
                Invalidate ();
            }
        }
        ... ...
```

当鼠标释放且未释放任何对象时，`bMoveAll` 字段的值会设置为 `false`。

```csharp
private void OnMouseUp (object sender, MouseEventArgs e)
{
    ... ...
    if (mover .Release (out iWasObject, out iWasNode, out shapeNode))
    {
        ... ...
    }
    else
    {
        bMoveAll = false;
        ... ...
```

## 整合所有部分

- 文件：`Form_FamilyTree.cs`
- 菜单位置：应用程序 ➤ 家谱

现在是将所有内容整合在一起的时候了，添加一些命令来简化真实家谱的设计，并最终开发一个家谱应用程序。此应用程序不会使用任何在前期示例中未讨论过的新类。任何复杂程度的树都可以通过 `Person`、`Bus` 和 `BusConnection` 对象来构建。这三个类的工作原理已在前面详细讨论过，在家庭树应用程序中，您会发现许多熟悉的内容。还会出现一些尚未使用过的新事物，但新颖之处不在于细节。该程序的主要区别在于设计家谱的过程。

在启动任何一个前期示例时，您会立即看到一些内容。可能有一辆或多辆公共汽车、一个 `Person` 对象、或几个带有一些连接的对象，但总能看到一些东西，所有这些小示例的主要目的是演示和解释后续如何使用已有的对象。当您启动家谱应用程序时，除了一个带有简短信息的矩形外，什么都没有。您可以将这些信息压缩到很小的尺寸，并将其留在角落。表单区域是空的！没有人事先知道特定用户要构建什么样的家谱，除了这个用户，没有人知道他想从家谱的哪个部分开始。

假设您决定以标准（传统）方式绘制您的家谱。绘制没有标准顺序。您可以从已知的最年长祖先开始，然后一代一代地往下画。您可以从您的父母开始，然后向两个方向延伸。您可以从最年轻的一代开始，然后向过去追溯。每个人通常用一个包含名字的椭圆或矩形来表示。已知的出生和死亡日期（如果已经发生，则包括死亡日期）放在名字旁边，而这个小块区域的边框明确表明名字和日期属于同一个人。

人与人之间的关系通过适当放置这些框和大量线条来显示。通常，时间线从上到下。配偶通常并排放置，他们的矩形用一条短线连接。孩子通常放在父母下方的同一水平位置。最年长的兄弟姐妹位于该行的最左端（对于书写方向从左到右的国家来说，这是正确的）。为了显示世代之间的关系，孩子通过一条线连接到他们的父母之间，但连接后续世代的方式可以有不同。您可以画一条新线从父母的连线向下延伸，然后在线的下端添加一个包含名字的矩形。如果您知道一对夫妇有三个孩子，那么很可能您会先并排画三个矩形，然后将每个矩形连接到上层的线上。这不是规则，但如果您这样做，整个图形会更精确。这也意味着您的图形可以由几个部分组成，通过添加一些线条将它们合并成一个完整的家谱。

有时您会发现遗漏了某些部分，没有空间在正确的位置放置新矩形了；那么您会将它们画在旁边，并用虚线来表示关系。当您决定绘制家谱的某个旁支时，也可能发生同样的情况。

一张纸会不够用。您会再拿一张纸，并做上特殊标记，以显示这些纸应该如何相互放置。表示人与人之间关系的线条会从一张纸延伸到另一张纸，并绕过那些恰好挡路的矩形。

我提到了在纸上绘制家谱的正常方式，但如果您想为同样的目的编写一个程序，那么这个程序必须在屏幕上提供同样的灵活性来绘制家谱。会存在一些限制，但必须将这些限制的影响最小化。

构建任何家谱都使用三个类的元素；所有这些元素都包含在三个 `List` 中。

```csharp
List m_people = new List ();
List m_buses = new List ();
List m_connections = new List ();
```

`Bus` 和 `BusConnection` 对象以类似的方式被添加到相应的列表中。每个对象都有一个唯一的 ID，因此通过这个 ID 进行简单检查，以防止同一个对象被重复包含。

```csharp
private void AddBus (Bus busNew)
{
    foreach (Bus bus in m_buses)
    {
        if (busNew .ID == bus .ID)
        {
            return;
        }
    }
    m_buses .Add (busNew);
}
private void AddConnection (BusConnection conNew)
{
    foreach (BusConnection con in m_connections)
    {
        if (conNew .ID == con .ID)
        {
            return;
        }
    }
    m_connections .Add (conNew);
}
```

添加 `Person` 对象的方式类似，但需要更多的操作。每个 `Person` 对象都包含一个不可见的 `Bus`，因此需要检查两个元素的 ID 唯一性，然后增加两个 `List`。

```csharp
private void AddPerson (Person prsn)
{
    long idPerson = prsn .ID;
    long idBus = prsn .Bus .ID;
    foreach (Person person in m_people)
    {
        if (idPerson == person .ID)
        {
            MessageBox .Show (...);
            return;
        }
    }
    foreach (Bus bus in m_buses)
    {
        if (idBus == bus .ID)
        {
            MessageBox .Show (...);
            return;
        }
    }
    m_people .Add (prsn);
    m_buses .Add (prsn .Bus);
}
```

家谱程序的用户对 `Bus` 或连接一无所知；他们更愿意用孩子、父母和配偶这些概念来思考构建的树，但任何添加单个亲属或一组亲属的命令都可以轻松追溯到这些 `AddBus()`、`AddConnection()` 和 `AddPerson()` 方法。这些方法用于收集提供程序功能的数据；用户处理的是同一数据在屏幕上的表示，并非所有的数据元素都可视化了。

- 所有 `Person` 对象都是可见的。
- `Bus` 对象分为可见和不可见。表示家谱中人物关系的 `Bus` 是可见的。每个 `Person` 对象都有一个不可见的 `Bus` 作为其下属元素。
- 所有 `BusConnection` 对象都是不可见的。

元素因使用许多不同的命令而出现在屏幕上。所有屏幕元素都可以修改，但首次出现时，任何新元素都会获得标准视图。此标准视图也可以由用户设置，但初始时程序以如下方式定义两个标准。

```csharp
public Form_FamilyTree ()
{
    ... ...
    busStandard = new Bus (this, mover, ..., new Pen (Color .DarkGray, 2));
    personStandard = new Person (this, mover, new RectangleF (0, 0, 140, 50), Color .Cyan, “姓名”, “出生”, “死亡”);
    ... ...
```

本章后面将讨论这些命令并用一些图进行说明；这些图中的元素大多显示在其提到的标准视图中。

当正在构建家谱且需要向已有元素添加新元素时，会在某个现有元素上调用菜单，新部分必须链接到该元素。也可以设计一个不相关的新部分，然后再将其链接到现有的家谱。

## 设计新版家谱

# 设计新版家谱

设计新版家谱始于在空白区域调出菜单（图 7-33）。该菜单的命令分为三组。第一组包括所有能在屏幕上放置新元素的命令；其中一项命令还包含子菜单。菜单会在鼠标右键点击的位置打开；由某个命令生成的新元素会锚定在该位置。第二组命令允许您更改现有家谱中所有总线的可视化参数。任何总线或`Person`元素的视图，均可通过在该元素上调出专用调整表单来更改。用户可以通过为所有未来元素设置首选的可视化参数来简化工作；所有新元素都将根据这些设置构建。我将在本节末尾附近详细说明这些设置；现在，我们先来看看对图 7-33 所示菜单中命令的响应。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig39_HTML.jpg](img/501148_1_En_7_Fig39_HTML.jpg)

图 7-34 带有调整表单的新 `Person` 对象

| *新建个人* | 屏幕上出现一个新的 `Person` 对象；其左上角位于鼠标点击点。用户至少需要为此人定义姓名，并决定是否显示出生和死亡的相关附加信息。因此，系统会自动为该新对象调用一个辅助调整表单（图 7-34）。视图中可能存在大量 `Person` 对象，因此调整表单的初始定位有助于告知用户当前正在修改的元素。 |
|---|---|

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig38_HTML.jpg](img/501148_1_En_7_Fig38_HTML.jpg)

图 7-33 要开始构建家谱，请在空白区域调出菜单

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig40_HTML.jpg](img/501148_1_En_7_Fig40_HTML.jpg)

图 7-35 屏幕上的新夫妻

| *新建夫妻* | 新夫妻由一对 `Person` 对象表示，它们彼此靠近放置，并由一条短直总线连接（图 7-35）。左侧元素的左上角位于鼠标点击点。两个新元素相同且具有标准视图。用户可以选择先修改哪一个，并通过元素上的菜单调出调整表单。 |
|---|---|

```csharp
private void Click_miNewPerson (object sender, EventArgs e)
{
    Person person = new Person (this, mover, ptMouse_Up, m_personStandard);
    AddPerson (person);
    RenewMover ();
    ModifyPerson (person, Auxi_Geometry .LocationByCoefficients (person .Area, 0.5, 0.95));
}
```

```csharp
private void Click_miNewCouple (object sender, EventArgs e)
{
    NewCouple(ptMouse_Up);
    RenewMover();
}
```

`NewCouple()` 方法生成两个 `Person` 对象，它们通过一条短总线相连；该总线由 `LinkPersonPerson()` 方法组织。

```csharp
private Bus NewCouple (PointF ptLT)
{
    Person personL = new Person (this, mover, ptLT, personStandard);
    AddPerson (personL);
    Person personR = new Person (this, mover,
        new PointF (personL .Right + m_nDistSpouses, ptLT .Y), personStandard);
    AddPerson (personR);
    Bus link = LinkPersonPerson (this, mover, busStandard, personL, Side .E, 0.5, personR, Side .W, 0.5);
    return (link);
}
```

有几种情况需要组织这对对象并将其连接到现有元素；图 7-39 演示了其中一种情况。由于从新夫妻到现有家谱的连接通常是从这两个新 `Person` 元素之间的短总线出发，因此 `NewCouple()` 方法会返回此总线。

从视觉上看，总线位于两个 `Person` 对象之间，但实际上它是连接这两个 `Person` 对象内部不可见总线的总线，因此 `LinkPersonPerson()` 方法只是 `LinkBusBus()` 方法的一个封装。

```csharp
private Bus LinkPersonPerson (Form frm, Mover mvr, Bus busSample,
    Person personFrom, Side side_From, double coef_From,
    Person personTo, Side side_To, double coef_To)
{
    Bus busFrom = personFrom .Bus;
    PointF ptFrom = Auxi_Geometry .PointOnRectangleBorder (personFrom .Area, side_From, coef_From);
    Bus busTo = personTo .Bus;
    PointF ptTo = Auxi_Geometry .PointOnRectangleBorder (personTo .Area,
        side_To, coef_To);
    return (LinkBusBus (frm, mvr, busSample, busFrom, ptFrom, busTo, ptTo));
}
```

一个几乎相同的 `LinkBusBus()` 方法已在之前的示例中使用过，其代码在 12 页前展示过。这两个方法的参数有一个区别：在先前的版本中，新总线的画笔作为参数之一，而当前版本则使用了总线样本。

```csharp
private Bus LinkBusBus (Form frm, Mover mvr, Bus busSample,
    Bus busFrom, PointF ptFrom, Bus busTo, PointF ptTo)
{
    ptFrom = Auxi_Geometry .Polyline_NearestPoint (ptFrom, busFrom .Points);
    ptTo = Auxi_Geometry .Polyline_NearestPoint (ptTo, busTo .Points);
    Bus bs = new Bus (frm, mvr, ptFrom, ptTo, busSample);
    BusConnection conFrom = new BusConnection (frm, mvr, ptFrom, bs,
        EndType .Head, busFrom);
    BusConnection conTo = new BusConnection (frm, mvr, ptTo, bs,
        EndType .Tail, busTo);
    busFrom .AddConnection (conFrom);
    busTo .AddConnection (conTo);
    m_connections .Add (conFrom);
    m_connections .Add (conTo);
    AddBus (bs);
    return (bs);
}
```

在使用“新建夫妻”命令的情况下，`LinkPersonPerson()` 方法被用作 `LinkBusBus()` 方法的封装。稍后您会看到一些命令，允许您在现有总线和 `Person` 对象之间设置新总线。根据新总线的端点位置，会使用 `LinkBusPerson()` 和 `LinkPersonBus()` 方法；两者都是 `LinkBusBus()` 方法的封装。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig41_HTML.jpg](img/501148_1_En_7_Fig41_HTML.jpg)

图 7-36 四个兄弟姐妹

| *新建兄弟姐妹* | 此菜单命令包含一个子菜单，允许您在屏幕上放置 2 到 6 个兄弟姐妹；图 7-36 显示了四个兄弟姐妹的变体。如果需要超过六个兄弟姐妹，最佳方法是使用此命令在屏幕上放置六个兄弟姐妹，然后通过另一个菜单中的命令添加更多兄弟姐妹；我将在本节后面介绍这一点。`NewSiblings()` 方法的返回值是兄弟姐妹上方的总线（`busAbove`）。左侧元素上方长总线的转折点即为鼠标点击点。 |
|---|---|

在之前的示例 `Form_TwoGenerations.cs`（图 7-32）中，我已经解释了添加多个兄弟姐妹的步骤。那例子中只有两个兄弟姐妹，但这并不重要。主要区别可以通过比较这两幅图看出。尽管两图在尺寸上差异不大——仅消失了两处小点——但它们的消失凸显了构建此类元素组方式上的重大变化。在旧版本中，组织了一条由单个线段组成的水平长总线；然后所有兄弟姐妹都通过短总线以相同的方式连接到这条总线上。

我特意使用图 7-36（包含两个以上兄弟姐妹）来演示，在新版本中并非所有兄弟姐妹都以相同方式连接；内部兄弟姐妹与两端兄弟姐妹的连接存在明显差异。在大型家谱的设计过程中，我开始感觉到使用旧版本进行后续树操作时有些不便，因此我更改了将兄弟姐妹纳入家谱的流程。

`NewSiblings()`方法用于处理 2 到 6 个同级节点。首先构造一个由三段组成的长总线，两端带有两个短正交线段的长线是同一总线（`busAbove`）的组成部分。

```csharp
private Bus NewSiblings (PointF ptL_busAbove, int nSiblings)
{
    nSiblings = Math .Min (Math .Max (2, nSiblings), 6);
    float cyLineAbove = ptL_busAbove .Y;
    float cySiblings = cyLineAbove + m_nDistGenerations / 2;
    float lenLineAbove =
        (nSiblings - 1) * (m_personStandard .Width + m_nDistSiblings);
    PointF ptL_above = ptL_busAbove;
    PointF ptR_above = new PointF (ptL_above .X + lenLineAbove, cyLineAbove);
    List<PointF> pts = new List<PointF> ();
    pts .Add (new PointF (ptL_above .X, cySiblings));
    pts .Add (ptL_above);
    pts .Add (ptR_above);
    pts .Add (new PointF (ptR_above .X, cySiblings));
    Bus busAbove = new Bus (this, mover, pts, m_busStandard);
    AddBus (busAbove);
    ... ...
}
```

然后，两个同级节点——第一个（`childFirst`）和最后一个（`childLast`）——连接在此总线的两端。

```csharp
float cxM_person;
PointF ptOnBus;
Person childFirst = new Person (this, mover,
    new PointF (ptL_above .X - m_personStandard .Width / 2, cySiblings), m_personStandard);
childFirst .Name_Comment .Text = "Sibling-1";
AddPerson (childFirst);
Person childLast = new Person (this, mover,
    new PointF (ptR_above .X - m_personStandard .Width / 2, cySiblings), m_personStandard);
childLast .Name_Comment .Text = "Sibling-" + nSiblings .ToString ();
AddPerson (childLast);
LinkBusBus_broken_bus (this, mover, m_busStandard,
    childFirst .Bus, busAbove .HeadPoint,
    childLast .Bus, busAbove .TailPoint, busAbove);
```

如果同级节点超过两个，则它们以标准（旧）方式通过更短的总线连接到长总线。由于长总线的长度已根据所需同级节点数量预先计算，因此它们不会发生重叠问题。

| `新总线` | 这是一个简单但有趣的命令，它在屏幕上生成一个极小的对象，无需插图即可说明。这个新对象是一条两端自由的短总线。 |

```csharp
for (int i = 2; i < nSiblings; i++)
{
    cxM_person = ptL_above .X + Convert .ToSingle (
        lenLineAbove * ((i - 1) * 1.0 / (nSiblings - 1)));
    Person child = new Person (this, mover,
        new PointF (cxM_person - m_personStandard .Width / 2, cySiblings), m_personStandard);
    child .Name_Comment .Text = "Sibling-" + i .ToString ();
    AddPerson (child);
    ptOnBus = new PointF (cxM_person, cyLineAbove);
    LinkBusPerson (this, mover, m_busStandard, busAbove, ptOnBus,
        child, Side .N, 0.5);
}
RenewMover ();
return (busAbove);
}
```

新总线的长度很可能只有 20 像素，尽管环境（或其他对象）可能迫使它变得更长。这种长度（和角度）的不确定性源于两端必须自由的要求。`Click_miNewBus()`方法的代码显示没有组织任何连接，因此无论端点的长度和位置如何，新总线都不会连接到任何对象。但我希望新总线在视觉上是自由的，这可能会带来一些问题。包含“New bus”命令的菜单在空白位置被调用，新总线的头部放置在同一位置，因此该端点肯定是自由的。当使用此命令时，没有人能预测所有其他元素的定位，但我希望新总线的另一端也位于空闲位置，因此通过`AnotherEndOfNewBus()`方法来搜索适合另一个端点的点。

```csharp
private void Click_miNewBus (object sender, EventArgs e)
{
    Bus bus = new Bus (this, mover, ptMouse_Up,
        AnotherEndOfNewBus (ptMouse_Up), m_busStandard);
    AddBus (bus);
    RenewMover ();
}
```

在本书开头的第 1 章中，我曾提到移动器不仅可以检查光标所在位置，还可以检查其工作的窗体上任何位置是否存在对象。这种检查很少需要，并且在所有其他示例中都没有使用，但在族谱应用中，这种检查在少数情况下很有用，而新总线端点的自由定位就是其中之一。

该方法首先尝试设计一条最小长度的垂直总线，并使用`Mover.PointInfoAll()`方法检查提议端点处是否存在任何对象。如果该点已被占用，则检查同一半径圆周上的另一个点。如果该半径圆周上的所有位置都被占用，则增加半径并重复相同的检查。最终会找到一个空闲点，并组织新总线。

对于同一问题，还可以有其他解决方案。例如，可以将自由总线设计得异常宽，使其视图即使在另一端放置在某些对象上时也能引起注意，但我更倾向于安装搜索空闲位置的方法。

新总线很短且不连接到任何东西，但这条自由总线允许你在族谱中组织任何需要的链接；这可以是两总线之间的链接、两个`Person`对象之间的链接，或总线与个人之间的链接。为此，你需要按下总线的端点，将其移动到要连接的对象上，然后释放总线端点。由于最后一步是释放对象，因此我们需要查看`OnMouseUp()`方法的代码。我将对以下代码片段进行一些有用的解释。

释放的总线端点可以连接到可见总线，也可以连接到`Person`对象边界上的不可见总线；在后一种情况下，它看起来像是连接到个人。这两种情况的代码仅在一些微小细节上有所不同，因此查看总线在总线上释放的情况就足够了。释放节点的编号（`iWasNode`）和形状（`shapeNode`）用于进一步分析，因此使用了相应版本的`Mover.Release()`方法。

```csharp
private void OnMouseUp (object sender, MouseEventArgs e)
{
    ptMouse_Up = e .Location;
    double dist = Auxi_Geometry .Distance (ptMouse_Down, ptMouse_Up);
    int iWasObject, iWasNode;
    NodeShape shapeNode;
    if (mover .Release (out iWasObject, out iWasNode, out shapeNode))
    {
        GraphicalObject grobj = mover .ReleasedSource;
        long id = grobj .ID;
        if (e .Button == MouseButtons .Left)
        {
```

释放的元素必须是不可移动总线的圆形节点。

```csharp
            if (grobj is Bus && shapeNode == NodeShape .Circle && !grobj .Movable)
            {
```

总线只能通过头部或尾部连接。圆形节点是总线覆盖层中的第一个节点，这些节点从头部到尾部编号；头部的节点编号为 0。释放的总线端类型（`end_type`）也由节点编号确定。

```csharp
                if (iWasNode == 0 || iWasNode == busPressed .TailPointNumber)
                {
                    EndType end_type = (iWasNode == 0) ? EndType .Head
                                                      : EndType .Tail;
```

释放总线与某个对象的连接取决于释放点处这些对象的情况。关于任何点处对象的完整信息可以通过移动器的`Mover.PointInfoAll()`方法获得。此方法返回一个`List`对象，其中包含在指定点找到的所有对象；此`List`中对象的顺序与它们在移动器队列中的顺序相同。

```csharp
                    List<MoverPointInfo> infoAll = mover.PointInfoAll (ptMouse_Up);
                    for (int j = 0; j < infoAll .Count; j++)
                    {
                        MoverPointInfo info = infoAll [j];
                        GraphicalObject objFound = mover [info .ObjectNum] .Source;
```

### 总线连接逻辑

当总线释放在某个对象（`objFound`）上时，将总线连接到自身是毫无意义的。我认为没有人真的需要组织这样的连接，但总有一些人喜欢用匪夷所思的情况来测试任何应用程序，而且也存在对象意外释放在错误位置的可能性。

```
if (id != objFound.ID)         // 总线不能连接到自身
{
```

当释放的总线必须连接到下方对象时，存在两种情况：它可以是另一条总线，也可以是一个 `Person` 对象。在第二种情况下，连接是通过矩形对象边界下方的不可见总线来组织的。这两种情况仅在少数细节上有所不同，因此只需研究其中一种情况的细节即可；我们考虑将总线释放在另一条可见总线上的情况。我们需要检查并避免尝试将释放的总线端连接到其另一端已连接的同一总线的情况。同时，两条总线相互连接也是禁止的，因此不允许出现两条总线形成回路的情况。

```
if (objFound is Bus)
{
    Bus busFound = objFound as Bus;
    if ((iWasNode == 0 && busPressed.TailConnected &&
        busPressed.ID_OnTail == busFound.ID) ||
        (iWasNode == busPressed.TailPointNumber &&
        busPressed.HeadConnected &&
        busPressed.ID_OnHead == busFound.ID) ||
        id == busFound.ID_OnHead ||
        id == busFound.ID_OnTail)
    {
        // 两端不能连接到同一条总线
        // 两条总线不能相互连接
    }
    else
    {
```

看起来我们成功地避开了所有陷阱，释放的总线端可以连接到释放点找到的另一条总线。此操作需要一些计算，并更改两个对象的几个参数。

- 计算精确的连接点（`ptOnBus`）。
- 释放的总线端（头端或尾端）必须采用此计算出的位置。
- 在计算出的点处组织新的连接（`con`）。
- 这是一个到找到的总线的新连接。每条总线都保存着所有连接的信息，因此必须使用 `Bus.AddConnection()` 方法更新找到的总线中的信息。
- 新连接被包含在完整的连接 `List` 中。
- 屏幕上会出现新的可移动对象，因此必须更新移动器队列。

```
    PointF ptOnBus =
    Auxi_Geometry.Polyline_NearestPoint (
    ptMouse_Up, busFound.Points);
    if (end_type == EndType.Head)
    {
        busPressed.HeadPoint = ptOnBus;
    }
    else
    {
        busPressed.TailPoint = ptOnBus;
    }
    BusConnection con = new BusConnection (this, mover, ptOnBus, busPressed,
    end_type, busFound);
    busFound.AddConnection (con);
    m_connections.Add (con);
    RenewMover ();
    }
    break;
}
else if (objFound is Person)
    ... ...
```

家谱应用程序提供了多种设计树状图的命令。这些命令的命名方式使其更易于从人际关系角度理解（例如 `new couple` 或 `new siblings`），但实际上，组织的元素内部并没有关于这些声明关系的任何信息，也没有构造元素所使用命令的痕迹。只有以不同方式相互链接的 `Bus`、`Person` 和 `BusConnection` 元素。同一个树状图可以通过多种不同的方式构建，并且三个列表（`m_people`、`m_buses` 和 `m_connections`）中元素的顺序可能不同。无论你是喜欢使用将连接元素组放置在屏幕上的命令，还是喜欢使用每次添加一条总线或一个人的其他命令，这都无关紧要。

例如，前面几页讨论过的 `New couple` 命令可以用以下命令和操作序列替代：

1.  在一个点使用 `New person` 命令，然后在稍偏一点的地方再次使用相同命令；屏幕上将出现两个独立的 `Person` 对象。
2.  使用 `New bus` 命令在屏幕上放置一条新总线；初始化时，此总线未连接到任何东西。
3.  移动新总线的一端，并将其释放在某个 `Person` 对象内部；该总线将连接到该对象（视觉上连接到该对象的边界）。
4.  移动总线的另一端，并将其释放在第二个 `Person` 对象内部；该总线端将连接到该对象的边界。

此序列比单个 `New couple` 命令长得多，但对于所设计树状图的工作而言，这并不重要。

如果需要断开总线连接，该怎么办？在连接点处调用菜单，并选择此菜单上唯一的命令——`Delete connection`（图 7-37a）。总线将被断开，并且在大多数情况下，其端点将移开，以在两个元素之间形成视觉间隔。如果总线长度减小会导致其变得过小，则不会移动端点，也不会形成间隔。无论是否形成间隔，总线都将被断开。具有自由端的总线是一种异常情况，因此在断开连接后，总线会使用警告颜色绘制（图 7-37b）。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig43_HTML.jpg](img/501148_1_En_7_Fig43_HTML.jpg)

图 7-37b

断开连接后，两个对象之间存在间隔，并且总线使用警告颜色
```

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig42_HTML.jpg](img/501148_1_En_7_Fig42_HTML.jpg)

`图 7-37a`

连接点上的菜单只有一个命令。

可以在家谱元素上调用多个菜单。其中一些菜单很小，只有一两个命令，但 `Person` 对象和总线段上的菜单非常大，包含修改现有家谱和添加新部分的命令。

## 总线段上的菜单

总线段上的菜单（`图 7-38`）可以在任何总线上调用，无论这条总线是否连接到任何东西，或者完全是自由的，这都无关紧要。和往常一样，菜单在按下总线的位置处被调用。如果所选菜单命令带来新的元素到屏幕上，则此新部分将在同一点连接到被按下的总线。以下四幅图展示了将新元素放置到屏幕上的命令，被按下的总线以宽蓝线显示。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig44_HTML.jpg](img/501148_1_En_7_Fig44_HTML.jpg)

`图 7-38`

总线段上的菜单

| *Modify (bus)* | 此命令打开辅助窗体（`图 7-21`），允许你更改被按下总线的外观。 |
|---|---|
| *Movable (bus)* | 此命令允许你更改被按下总线的可移动性，但此命令只能应用于两端都自由的总线。这对于真实的族谱来说是一种极其罕见和奇怪的情况，但它是有可能的，所以我决定在菜单中保留此命令。 |
| *Use this bus as sample for all* | 假设你花了一些时间构建家谱，然后决定更改所有总线的外观。你可以通过使用其调整窗体单独更改任何总线的外观，但在大型树状图中对每条总线都重复此过程将花费太长时间。相反，你可以将一条总线的外观更改成你喜欢的任何样式，然后使用此命令；所有总线都将根据此样本更改其外观。 |
| *Add parents* | 一对带有它们之间一条总线的 `Person` 对象代表一对夫妇；父母之间的总线与被按下的总线相链接（`图 7-39`）。这对新的 `Person` 元素与 `图 7-35` 中的一对完全相同；结果中的唯一区别在于这一对元素与被按下总线之间的链接。 |

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig45_HTML.jpg](img/501148_1_En_7_Fig45_HTML.jpg)

`图 7-39` “添加父母”命令的结果

| *添加单亲* | 如果有关于一位家长的信息，或者用户只想在家谱中包括一对夫妇中的一个人，那么用户可以使用“添加单亲”命令。因为屏幕上只会出现一个新的 `Person` 对象（`图 7-40`），并且这个新对象至少需要更改一些信息，因此系统会自动在该新对象上调用一个用于调整 `Person` 对象的辅助表单。 |
|---|---|

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig46_HTML.jpg](img/501148_1_En_7_Fig46_HTML.jpg)

`图 7-40` 添加单亲

| *添加孩子* | 此命令与前一个命令类似，但新的 `Person` 对象出现在被点击的连接线下方（`图 7-41`）。使用此命令有两种标准情况：某对夫妇只有一个孩子，或者有超过六个兄弟姐妹的情况。六个兄弟姐妹的屏幕元素可以通过一个命令来组织；如果需要更多兄弟姐妹，可以在此兄弟姐妹上方的连接线上使用此命令来添加。 |
|---|---|

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig47_HTML.jpg](img/501148_1_En_7_Fig47_HTML.jpg)

`图 7-41` 添加单个孩子

| *添加多个孩子* | 有一个子菜单可以在屏幕上放置 2 到 6 个兄弟姐妹；`图 7-42` 显示了两个兄弟姐妹的变体。如果有超过六个兄弟姐妹，你可以用一个命令放置六个，然后根据需要在兄弟姐妹上方的连接线上反复应用“添加孩子”命令。我已经解释过添加多个兄弟姐妹的步骤顺序。一切始于一条由三段折线设计的连接线；然后第一个和最后一个兄弟姐妹连接到这条连接线的两端。为了更好理解，`图 7-42` 中的连接点以特殊方式进行了标记。 |
|---|---|

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig48_HTML.jpg](img/501148_1_En_7_Fig48_HTML.jpg)

`图 7-42` “添加多个孩子”的结果：两个兄弟姐妹

| *连接新总线* | 新的短连接线连接到被点击的连接线上；新连接线的另一端是自由的。你可以重新配置新连接线，移动其自由端，并将其连接到任何所需的对象（连接线或人物）。 |
|---|---|

家谱的构建不仅可以将一组新元素连接到连接线，还可以连接到任何 `Person` 对象；这通过可以在该类对象上调用的菜单命令来完成（`图 7-43`）。从可用添加新元素命令列表可以看出，这些命令与空白位置菜单的命令（`图 7-33`）以及连接线段菜单的命令（`图 7-38`）类似。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig49_HTML.jpg](img/501148_1_En_7_Fig49_HTML.jpg)

`图 7-43` 人物对象上的菜单

`Person` 对象菜单中的第一个命令会调用其调整表单，这在 40 页前已经演示过（参见 `图 7-29`），但在真实的家庭树应用程序中，用户需要编辑所有信息片段，因此所有三个文本框都允许此类编辑（`图 7-44`）。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig50_HTML.jpg](img/501148_1_En_7_Fig50_HTML.jpg)

`图 7-44` 人物对象的调整表单

如果你清除了出生和死亡文本框中的信息，那么这些注释将不会出现在 `Person` 对象中。如果你尝试对姓名字段进行同样的操作，那么*未知*这个词将代替姓名出现。此调整表单允许你单独更改每个注释的字体和颜色，尽管我认为用户可能希望在家谱中对元素有更标准化的视图。我假设此调整表单主要用于更改注释文本。按照这种优先使用方式，组内的六个小按钮就变得多余了，因此它们的外观通过空白位置菜单的命令来调节。

`Person` 对象菜单的主要部分包含六个不同的命令，用于为被点击的人添加一些亲属；其中四个命令会添加一个新的 `Person` 对象。我想再次强调，菜单中的命令是按照现有人员与新人之间的关系来表述的，但这些关系并未存储在家谱中的任何地方。家谱中只有 `Bus`、`Person` 和 `BusConnection` 类的元素。

`图 7-45` 演示了使用这四个添加单个 `Person` 对象的命令的结果。该对象会与调整表单一起出现在屏幕上；其发生方式与 `图 7-34` 所示完全相同。在 `图 7-45` 的所有版本中，黄色矩形代表调用菜单的现有 `Person` 对象；青色矩形则是使用某个命令后出现的结果。如你所见，这四个不同的命令将新矩形放置在现有矩形的一侧或另一侧，这也是这四个命令之间的唯一区别。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig54_HTML.jpg](img/501148_1_En_7_Fig54_HTML.jpg)

`图 7-45d` “添加孩子”命令

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig53_HTML.jpg](img/501148_1_En_7_Fig53_HTML.jpg)

`图 7-45c` “添加单亲”命令

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig52_HTML.jpg](img/501148_1_En_7_Fig52_HTML.jpg)

`图 7-45b` “添加配偶”(右侧) 命令

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig51_HTML.jpg](img/501148_1_En_7_Fig51_HTML.jpg)

`图 7-45a` “添加配偶”(左侧) 命令

一个变体可以通过以下三个简单步骤转换为另一个变体：

- 将青色矩形移动到黄色矩形的另一侧。
- 将黄色矩形上的连接线端点移动到青色矩形当前所在的那一侧。
- 移动青色矩形上的连接线端点，使得连接线显示为两个矩形之间的最短直线。

其他几个命令需要一些说明。

| *连接新总线* | 此菜单在矩形内部某处调用。执行此命令后，新连接线将连接到距菜单调用点最近的边界点。如果需要，可以轻松地围绕边界移动新的连接点。 |
|---|---|

| *设置标准参数* | 此命令有一个子菜单，提供更改部分或全部可见性参数的选项。我之前提到过存在保持标准视图的 `Bus` 和 `Person` 元素，也提到用户可以在一定程度上更改这些视图。 |
|---|---|

# 家庭树应用程序与函数查看器

## 家庭树应用程序

在“家庭树”应用程序的主菜单中，有一个“设置”项；如果你点击此菜单命令，将打开`Form_FamilyTree_NewElemParams.cs`（图 7-46）。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig55_HTML.jpg](img/501148_1_En_7_Fig55_HTML.jpg)

**图 7-46** 一个专门的表单允许你设置用于设计所有新元素的参数

### 设置表单

`Bus` 组允许您设置所有新总线的参数，因此该组中的元素与总线调谐表单（图 7-21）中显示的相同。

`Distances` 组允许您定义通过不同菜单命令添加到家谱中的多个 `Person` 对象之间的标准距离。调节值为配偶及兄弟姐妹之间矩形的水平间距，以及父母与子女之间的垂直间距。

`Person` 组中的元素允许您为未来的 `Person` 对象设置所有字体和颜色。此表单左上角有一个 `Person` 对象。所需的大小通过调整此示例来设置；所有三个注释（名称、出生和死亡）的定位系数通过移动注释来设置。

### 应用设置

我想强调的是，当您关闭此辅助表单并返回家谱应用程序的主表单时，此表单中的所有更改不会立即生效。这些设置仅在您开始向家谱添加新元素时使用。您可以将这些设置应用于现有家谱，但由于此操作可能会破坏精心准备的大树视图，从而造成有害影响，因此在更改整体视图的操作中，我特意加入了几个中间步骤。

首先，在某个 `Person` 对象上调用菜单，并使用子菜单中的命令 `设置标准参数`。我建议不要只对一个 `Person` 对象执行此操作，而是对多个 `Person` 对象执行，并且可能使用子菜单中的不同命令。如果您对结果满意，并希望进行全局更改，则在同一 `Person` 对象上调用菜单，并使用命令 `用作样本`。

更改总线不会导致灾难性后果，但最好也分两步进行全局更改。首先，在某个总线上调用菜单，并使用 `修改（总线）` 命令。如果您对结果满意，则调用同一菜单，并将此总线用作其他所有总线的样本。

### 主菜单命令

让我们回到主家谱。`Form_FamilyTree.cs` 的主菜单中有两个有用的命令。`文件 ➤ 另存为` 命令允许您将家谱保存为二进制文件，而 `文件 ➤ 打开` 命令允许您从文件中恢复家谱。

### 滚动与设计规则

真实的家族树可能很大，您极有可能需要比屏幕更大的空间来显示它。您可以轻松地以任意方式滚动整棵树；只需在任意空白处按下左键并开始移动即可。这种滚动整棵家谱的方法在之前的示例中已经解释过。

不要害怕在设计中进行任何操作或犯错，因为此家谱应用程序中实施了若干规则。

- 任何用户的操作都不会对家谱设计造成致命影响。如果您放置了错误的元素，可以通过上下文菜单中的命令删除它；如果您放置了错误的元素组合，可以对每个元素使用 `删除` 命令。如果总线连接到了错误的元素，只需断开连接（通过在错误连接处的菜单中使用命令），然后继续您的设计。

- 设计中有一些限制：总线不能连接到自身；总线两端不能连接到同一总线；两条总线不能通过相互连接形成环路。除此之外，其他操作都是允许的。

- 通过调整可见性参数，您可以添加从您的角度看可能重要的个人信息，但这些信息不会在任何标准命令系统中提及。例如，如果您想用两条总线突出显示配偶之间的联系，您可以这样做。没有人会阻止您尝试这样做，也不允许您在相同的两个 `Person` 对象之间组织两条并行的总线。或者，您可以在总线中为宗教婚姻和民事婚姻标记不同的方式。

- 不仅可以更改元素的视觉参数，甚至一些标准的家族联系也可以与之前描述的命令系统所建议的方式不同。图 7-47a 显示了使用几页前讨论的两个命令的结果：首先设置了一对夫妇；然后在他们之间的总线上使用另一个命令添加了三个孩子。同一个由父母和三个孩子组成的家庭可以用不同的方式表示（图 7-47b）。第一个命令是相同的；然后，将添加单个孩子的命令应用到上方总线三次。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig57_HTML.jpg](img/501148_1_En_7_Fig57_HTML.jpg)

**图 7-47b** 父母矩形作为标准命令的结果出现，而孩子则通过在上方总线上调用命令逐个添加

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig56_HTML.jpg](img/501148_1_En_7_Fig56_HTML.jpg)

**图 7-47a** 此视图是使用两个标准命令的结果

这是一个标准的用户驱动型应用程序。这是您的应用程序，您是主导者，您可以完全按照自己的意愿组织视图。

## 函数查看器：一窥科学应用

在本示例的描述中，你将看到一些简短的代码片段，因为所有元素及其工作方式我们都已经讨论过了。本节要演示的主要内容，是一个相对复杂、用户操作和请求无法预测的应用的总体设计。所有科学应用都必须遵循一条简单规则来开发：“用户能做一切。”作为开发人员，我必须按照这条规则来提供程序。许多读者可能会觉得，开发允许用户为所欲为的应用会给程序员带来巨大的、不堪重负的负担，所以我想证明这种看法并不正确。设计者不需要对一切能想象到和无法想象的情况都做出响应，而是应该基于那些完全由用户控制的元素来开发程序。

在 2001 年至 2004 年间，我参与开发了一个成功销往全球的大型工程程序。该程序用于分析大型电网，包含数十个对话框，允许熟练的用户操控数百个参数，以分析不同的场景和情况。与这类程序的常规做法一样，许多计算的结果不仅要通过数字和填充的表格来展示，还需要绘制成图表。（人们以图形形式查看数据时，理解和评估数据的效果要好得多。）2004 年年中，我们这些该程序的开发者在讨论新版本的想法。其中一个想法是增加结果的图形化展示，并赋予用户对此展示的一定控制权，例如，让用户决定显示的绘图区域数量。

这个程序是以经典方式设计的。当时还没有人谈论屏幕对象的可移动性。整个应用是自适应界面的一个经典例子，因此未来的所有工作场景都必须提前构思好并编入代码。我一直在思考一种简便的工具（对用户而言），让用户能够声明所需的绘图数量及其位置。按照当时的惯常做法，所有对话框都基于普通控件来设计。无论我尝试何种方案，声明绘图位置和大小的机制看起来都不够优雅，但至少能工作，我也希望在过程中能想出更好的办法。然而，有一个小问题我甚至找不到一个稍微靠谱的解决方案。

在科学文章中，你经常会看到一个绘图区域中，几条曲线以不同颜色和线型显示。曲线之间的差异是由某个参数的变化引起的，因此曲线需要附上注释，说明其关联的参数。当文章作者准备此类图片用于发表时，要显示的曲线是已知的，为曲线附近的注释选择最佳位置也不是问题，因此在科学期刊中，整张图看起来总是很美观。

当你开发一个需要展示此类绘图区域的应用时，你事先并不知道内部会呈现什么样的曲线，因此你不得不将所需的注释放在绘图区域内部之外的某个地方。你能为它们在绘图区域下方或侧面预留一些空间吗？可以，但是：

*   注释会占用大量宝贵的屏幕空间。

*   注释会远离曲线，从而难以将曲线与注释关联起来（尤其是在同一区域有多条曲线时）。

*   还有围绕绘图区域的坐标轴刻度问题。可能存在多个坐标轴及其自身的注释，因此刻度位置与不同注释之间会有各种能想象到和无法预测的冲突。

*   如果一个视图中有多个绘图，那么问题会远远超过线性增长的速度。

总结所有这些需求，这界面本身已经因要放置所有所需绘图区域的几个控件而变得复杂，现在又得用一个额外的工具来放置所有需要的注释，这会使界面复杂得多。即使所有这些都以某种方式组织起来，看起来也非常笨拙（这是最客气的说法）。此类程序的用户是高素质的专家，他们试图解决非常严肃的工程/科学问题，不需要把时间浪费在与糟糕的界面设计作斗争上。这个需要快速、准确、简便地在带有未知曲线的绘图区域任意位置放置所需注释的问题，在设计一个庞大且非常复杂的科学应用时，可能看起来并非首要问题。因此许多人认为它总能用某种方式解决，但在标准设计下，这个问题无解。而这并非科学应用中此类问题的孤例。

在 2006 年至 2007 年间，我设计了一种屏幕对象可移动性算法，研究此算法的主要动力正是工程/科学程序的需求。2008 年初，当我的算法已准备好用于真实的科学程序时，我开始在传热传质研究所的数学建模系工作。来自该系及其他系的这些科学家是热力学领域的高素质专家，其中许多人也擅长编程，因此他们非常清楚界面的局限性。他们向我提出了一系列开发任务，这些任务需要开发成可工作的程序，但在标准设计的范畴内却无法实现。任务各不相同，但有一个共同特点：每个任务都必须处理来自实验和计算的数据不确定性。第一阶段这种不可预测性使得在标准界面下进一步工作几乎不可能。如果你试图设计一个能应对所有可能变体的程序，那么标准界面会变得极其笨拙，以至于根本无法使用该程序。每个程序都需要对屏幕元素进行手动操作，而这与用户驱动应用的理念和可能性完美契合。我逐一为这些任务设计了程序，每一次用户获得的功能都超出了他们最初的预期（或要求）。这些程序在研究工作中很有帮助，于是我收到了一个疑问：能否将所有旧程序重新设计成用户驱动应用？

通常，工程和科学程序规模庞大且复杂。每个程序都有许多特殊功能和细节，需要冗长的解释，并且只对特定领域的专家有较高价值，但对于该领域之外的人则毫无意义。同时，这些程序在设计上又有许多相似之处，因此多年来，我一直在为彼此相距甚远的知识领域设计应用。演示任何一个此类程序都需要解释其特定领域的问题和细节，这远非我在这本书中想讨论的内容。这就是为什么我更愿意演示一个包含科学应用所有特性，但又能被任何读者轻松理解的示例。我想演示和讨论一个函数查看器程序，它允许你输入`Y(x)`函数的表达式或`{X(p), Y(p)}`类型的参数方程，并对其进行操作。这个程序有助于学校的数学教学，同时它也展示了大量可用于设计复杂、专业科学应用的内容。

*   文件：`Form_FunctionViewer.cs`
*   菜单位置：应用 ➤ 函数查看器

# Function Viewer 的设计与实现

## 核心算法的历史背景

本函数查看器所依赖的核心工具本身并无新奇之处。您可以找到多种能将数学表达式转换为图形的程序，这些程序都基于相同的数学工具。20 世纪 20 年代，波兰数学家扬·卢卡西维茨发明了一种表示法，其中省略了所有括号，并将运算符置于操作数之前；为纪念其发明者，这种表示法被称为波兰表示法。20 世纪 50 年代末，澳大利亚计算机科学家查尔斯·汉布林改变了操作数与运算符的顺序，从而发明了逆波兰表示法。如今，任何数学表达式解释器都基于此类表示法。在三十多年前出版的一些书籍中，您能找到这类解释器的说明，并附有演示汽车在火车站调头的插图。每辆汽车都承载着原始表达式中的一个元素，根据元素顺序，汽车可以直接穿过车站，或暂时驶入死胡同，只在恰当的时刻再被取出。通过这种方式，原始表达式中元素的顺序被改变，最终输出同样以逆波兰表示法形式呈现的表达式。编写这样一个解释器，是那些刚学习队列和栈的编程课程学生的标准练习，因此我不打算详述编写此类解释器的细节，而是直接使用 `MoveGraphLibrary.dll` 中提供的 `FunctionInterpreter` 类。

## `FunctionInterpreter` 类的核心方法

该类方法不多，但已足够使用。解释过程始终始于对待转换字符串的分析，该字符串需被转换为 `Elem` 类元素的 `List`，因此第一个要使用的方法是 `FunctionInterpreter.Analyse()` 方法。

```
bool Analyse (string strIn, ref List elems, out int iError,
out int kErrPlace)
```

原始函数文本可以包含不同的元素。

*   标准函数名称：`{ sin, cos, tg, sh, ch, th, ln, lg, exp, sqrt, mod, arcsin, arccos, arctg }`。
*   二元运算：`{ +, –, *, /, ^ }`。符号 `^` 用于表示幂运算。
*   一元运算：`–`
*   括号：`(` 和 `)`
*   变量：`{ x, p, r, X, P, R }`
*   数字。

如果函数文本中存在任何错误，则会返回错误类型和位置。如果文本正确，它将被转换为一种特殊视图——元素集合 `List<Elem>`；此 `List` 可用于计算。计算由 `FunctionInterpreter` 类的另一个方法完成。

```
bool Calculate (List PolishForm, double fArg, ref double fVal)
```

此方法返回的布尔值仅指示计算是否正确；计算结果通过其中一个参数（`fVal`）获取。

## 设计理念的创新

虽然函数查看器的主旨思想并无新奇之处，但其使用方式却是全新的。开发者需要提供一个无误的解释器，并将其与一个近乎无限的功能演示工具相结合。只有用户才需决定查看哪些函数以及所有可见性参数。我将此函数查看器与我多年前设计的旧版科学程序中的类似模块进行比较，之前提到的那些没有解决方案的问题就此消失了。

不再有笨拙的工具来设置新绘图的数量、位置和大小，也没有完美的工具来完成所有这些设置。这样的工具已不再需要。用户可随时添加或删除绘图区域，为单个或多个图形创建区域，移动和调整绘图区域大小，并轻松更改所有可想象的可见性参数。用户并非被迫这样做，而是可以随心所欲地随时操作。开发者不会干预这些操作，只负责提供一个易于使用的工具来完成所有操作。

多年前，存在一个无法解决的注释定位问题；现在，你无需考虑任何解决方案，因为问题本身已不复存在。用户自行决定注释文本的显示与隐藏。注释可以随时移动、旋转、隐藏、显示和修改，因此用户能轻松为任意图形决定所需的注释。

所有更改都会被保存和恢复；用户可以组织不同的变体，并在它们之间轻松切换。

这并非我多年来展示的函数查看器的第一个版本，它被收录在我的一本大部头专著[2]的示例中。^(¹⁰) 虽然当前版本与旧版高度相似，但新版使用了不同的绘图类，并具备一些不同的特性。

## 科学绘图中的通用需求

科学和工程应用可能有取决于特定领域的特殊绘图需求，但同时，许多领域的绘图都具有大量共同特征。大多数情况下，科学家处理的是 `Y(x)` 函数，这些函数通过在带有两个正交坐标轴的矩形区域中绘图来展示。通常，这些是线性坐标轴，尽管在某些领域，对数坐标轴也很常用。在大多数情况下，拥有一个水平和一个垂直坐标轴就足够了，但有时为了更好的视觉对比，同一区域内的多个图形必须在完全不同的范围内显示，这时就需要多个不同的坐标轴。旧的 `Plot` 类支持多坐标轴工作，而当前版本的 `Plot_Adh` 类在每个方向上仅使用一个坐标轴。

```
public class Plot_Adh : GraphicalObject
{
Scale_Numbers scaleHor, scaleVer;
List m_comments = new List ();
```

`Plot_Adh` 类使用了熟悉的注释类和坐标轴类；这些坐标轴在第 6 章讨论条形图、其坐标轴及其调整时已经演示过（图 6-4–6-7）。`Plot_Adh` 类主区域的调整在一个特殊的窗体（图 7-48）中完成，该窗体包含了在条形图区域调整中已展示过的几个熟悉部分和元素（参见图 6-8）。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig58_HTML.jpg](img/501148_1_En_7_Fig58_HTML.jpg)

图 7-48 用于调整绘图区域的辅助窗体

## 绘图区域的可视化定制

*   背景通常设置为与周围窗体（或 `TabControl` 的页面）相同，尽管也可以轻松选择任何所需背景色及其透明度。
*   绘图区域的边框和两个方向的网格线以相似方式独立调整。它们每个都可以选择显示或不显示；如果显示，则可以选择线条颜色和样式。网格线的位置由坐标轴上数字（和刻度）的位置决定，但刻度线和网格的外观是独立的，并在不同的窗体中分别对坐标轴和主区域进行调整。

颜色的选择也与条形图的颜色选择类似。一个矩形区域包含颜色样本。所使用的颜色从最左边的样本开始选择，因此可以通过更改这些样本的顺序（按住并移动样本到另一个位置）、更改样本（在要更改的样本上调用菜单）或添加更多样本（在样本区域的空白处调用菜单）来改变函数的视图。

任何图形都显示为连接计算点的线段序列。线段的端点可以在绘图区域内部或外部；“要显示的线段”组允许您选择要显示的线段。通常，要显示的函数足够平滑，此组中的选择不会改变图形的视图。对于`Y(x)`函数，为绘图区域内的每个水平像素计算值，生成的线是每对后续值之间的线段序列。当图形线接近垂直且相邻水平点的值差异巨大时，不同变体之间的差异就变得明显。最糟糕的情况发生在垂直渐近线的情况下。在这种情况下，最好选择第一个选项（两端都在视野内），而最后一个选项（全部）可能会在渐近线位置显示垂直线。

为了更好地理解函数查看器，让我们将其用于一个足够简单的任务，该任务不需要对其数学方面进行大量解释。假设您想与某人讨论多项式函数的特性以及此类函数随多项式次数的变化。为了进行此讨论，您需要准备几个函数，并为它们选择不同的视图以便更好地演示。

首次启动函数查看器时，您只会看到一个包含空函数列表的组，并且所有按钮都处于禁用状态（图 7-49）。函数查看器允许您处理两种类型的函数。多项式函数属于`Y(x)`类型，因此主菜单的相应命令会打开所需的辅助窗体（图 7-50）。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig60_HTML.jpg](img/501148_1_En_7_Fig60_HTML.jpg)

图 7-50 定义`Y(x)`函数的辅助窗体

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig59_HTML.jpg](img/501148_1_En_7_Fig59_HTML.jpg)

图 7-49 开始时，既没有函数也没有绘图

多年来，我对我程序的描述经常包含诸如“在左边你看到……”之类的短语。对于用户驱动的应用程序，解释中的此类词语会变得令人困惑，因为用户可以以任何可能的方式更改程序视图，而正在运行的程序的视图可能与设计师精心构思和准备的原始视图毫无共同之处。`Form_FuncYx.cs`有一个绘图区域、用于函数定义的组和信息。信息可以阅读一次，之后就不再需要，因此它可以被压缩到很小的尺寸。所有元素都可以移动和调整大小，因此每个用户都可以在几秒钟内组织他偏好的视图。

此窗体中的大多数元素都是图形对象，这些对象已在前面章节中讨论过。还有两个普通控件，与所有其他元素一样，它们也是可移动和可调整大小的。普通控件的移动和调整大小将在附录 A 中讨论，因此在此我只想简要提一下。

所有元素都必须可移动和可调整大小。通常，图形元素通过边框调整大小，并通过任意内部点移动。这对于普通控件是不可能的，因为单击其区域内的任何位置都会触发用户预定义和期望的某些操作。因此，对于控件，移动和调整大小都从边框开始。调整大小从角点和边的中间部分开始；移动从所有其他边框部分开始。

在设计任何窗体时，我尝试以对实际工作最佳的方式进行组织，但这只是我对“最佳”的看法。每个用户都可以自由组织自己的视图。用户不仅可以按照自己的意愿更改视图，而且我的主要努力是使这个更改过程对用户尽可能简单。

对于纸质插图，我会更改程序视图，使其与纸张格式匹配；偶尔我不得不考虑纸张文本中几个相邻图形的定位。因此，在进入`Form_FuncYx.cs`时，您将看到的视图与图 7-50 中显示的视图不同，但我保证，无论您如何更改它们，这些元素都将完全按照它们应该做的去做。

当用户首次打开`Form_FuncYx.cs`时，要定义的函数是不可预测的，并且显示此函数的最佳范围是未知的，因此比例尺以这样的方式组织。

```csharp
private void DefaultView ()
{
plot = new Plot_Adh (this, mover, new Rectangle (50, 30, 400, 280),
Side .S, 0, 100, GridOrigin .ByStep, 20,
Side .W, 10, -10, GridOrigin .ByStep, 5);
... ...
```

对于任何`Y(x)`函数，您需要定义函数表达式；您还需要为该函数指定一个唯一的名称，以便将其与其他函数区分开来。函数表达式和名称在“函数”组中包含的两个控件内输入。与所有普通控件一样，这两个元素通过其边框进行移动和调整大小；所有其他元素通过内部点移动并通过边框调整大小。

我们开始研究多项式函数，从一次函数开始，并输入一个简单函数`x–3`的文本。要查看其图形，请单击“显示”按钮。水平和垂直刻度的结束值是在绘图区域初始化时设置的。对于我们当前的函数，X 刻度的默认范围 [0, 100] 不太合适，因此最好更改它。用于`Scale_Numbers`类的水平和垂直刻度的调优窗体已经讨论过（图 6-4 和图 6-5）。通过使用水平和垂直刻度的调优窗体，我们可以为我们的函数获得更好的视图（图 7-51）。在辅助窗体中准备函数的过程中，不仅可以设置刻度范围，还可以设置许多其他参数。通常，每当您需要更改某个对象时，您可以在该对象上调用菜单；将会有用于更改特定参数的命令或用于调用调优窗体的命令。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig61_HTML.jpg](img/501148_1_En_7_Fig61_HTML.jpg)

图 7-51 经过一些刻度调优后的`Form_FuncYx.cs`

必须声明函数的名称。名称只是文本，因此您的想象力有广阔的空间。

当一切准备就绪后，我们可以单击“添加”按钮，并将这个准备好的函数返回到函数查看器的主窗体。函数名称出现在列表中，而函数本身通过`NewPlotForSingleFunction()`方法显示在新的绘图区域中（图 7-52）。

```csharp
private void Click_miNewFunc_Yx (object sender, EventArgs e)
{
Form_FuncYx form = new Form_FuncYx (m_functions, -1);
form .ShowDialog ();
if (form .FunctionReady)
{
... ...
NewPlotForSingleFunction (iNew);
}
}
```

在辅助形式和函数查看器中，函数的外观存在巨大差异（图 7-51）。在辅助形式中，始终只有一个绘图区域。当我们准备示例中的第一个函数时，主窗体中有一个单独的绘图区域；之后我们会拥有越来越多的绘图区域，每个新区域都会出现在已有图形之上。试图将新图形放置在某个空白位置是没有意义的，它只会简单地出现在当前显示内容的顶层。新绘图区域的大小取决于窗体的大小，而每个新绘图区域的位置会与前一个略有偏移。

最关键的参数（如刻度范围）从辅助形式中复制，而其他参数则采用默认值。两个注释显示了函数名称及其表达式。

```csharp
private void NewPlotForSingleFunction (int iFunc)
{
RectangleF rc = NewArea ();
Function_Origin func = m_functions [iFunc];
Plot_Adh plot = new Plot_Adh (this, mover, rc, Side .S, ..., Side .W, ...);
plot .AddFunction (func);
... ...
```

用户可以在新图形出现在主窗体后立即进行移动、调整大小和修改，也可以在之后的任何时刻进行。对于多项式函数的进一步讨论，我倾向于先准备好所有需要的函数，然后为所有图形组织一个良好的视图。

当列表中的任意函数被选中时，组内的几个按钮会变为启用状态（图 7-52）。要准备一个二次多项式，我可以重复之前描述的过程，但我想将现有的一次函数作为下一个函数的一部分，在这种情况下，在列表中选择现有函数的名称并点击此按钮会更简单：![../images/501148_1_En_7_Chapter/501148_1_En_7_Figb_HTML.jpg](img/501148_1_En_7_Figb_HTML.jpg)。同样的`Form_FuncYx.cs`会被打开，但与准备前一个函数相比，准备新函数`Y = (x-3)(x-1)`所需的精力略少。如果在一个略有不同的 X 范围内显示，新函数看起来也更好，因此我使用水平刻度调整窗体。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig62_HTML.jpg](img/501148_1_En_7_Fig62_HTML.jpg)

图 7-52 带有第一个多项式函数的函数查看器

由于某个函数被用作进入辅助形式的来源，因此该窗体中的`Change`按钮也会被启用（图 7-53）。我不想更改第一个函数，而是要组织一个新函数，所以输入一个不同的名称，然后点击`Add`按钮返回主窗体。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig63_HTML.jpg](img/501148_1_En_7_Fig63_HTML.jpg)

图 7-53 准备二次多项式函数

`Form_Functions.cs`中的函数列表新增了一行，并组织好了带有第二个函数的新的绘图区域。当一个带有函数的绘图区域被组织好时，它会自动获得两个新注释，分别显示函数名称及其表达式。任何注释都可以被更改、隐藏或删除。我不需要在函数各自的绘图区域中显示其名称，因此我在那里留下一个带有表达式的注释，但隐藏了名称。

我想演示高达五次的多项式，因此继续重复相同的过程几次，最终得到五个不同的绘图区域，每个区域包含一个图形（图 7-54）。这些图形可能有助于某些解释，但这种视图并不适合比较多项式，也不适合讨论随多项式次数变化而发生的变化。如果它们都在同一个绘图区域中就好了，因此我在列表中选择所有需要的函数，并点击此按钮：![../images/501148_1_En_7_Chapter/501148_1_En_7_Figc_HTML.jpg](img/501148_1_En_7_Figc_HTML.jpg)。屏幕上会出现另一个绘图区域；在该区域中，显示了五个图形。现在，我拥有了所有需要的图形，但视图并不理想。经过一些额外的调整后，我得到了所需的视图（图 7-55）。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig64_HTML.jpg](img/501148_1_En_7_Fig64_HTML.jpg)

图 7-54 准备五个多项式

在准备图 7-55 时，我必须对每个绘图区域进行一些调整，但更改很小。在包含所有图形的区域中，我不喜欢其中一个图形的颜色，因此不得不更改那种颜色。然后我决定，在大绘图区域和小绘图区域中，每个函数最好用相同的颜色显示，因此对于小区域，我不得不打开它们的调整窗体，并将不同的颜色样本移到前列。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig65_HTML.jpg](img/501148_1_En_7_Fig65_HTML.jpg)

图 7-55 分别显示和全部显示的多项式

`Scale_Numbers`和`Plot_Adh`对象的调整窗体是相同的，无论它们用于哪个程序。这些调整窗体通常通过双击、菜单或两者结合来调用。绘图区域是工程/科学程序中极其有价值的对象，但总会有其他类的对象，根据程序的复杂性和新颖性，用户可能会看到熟悉的对象和新对象。为了使任何程序的使用都更加简单，所有程序都实现了一些简单的规则。

*   要对某个元素执行任何操作，用户需要调用该元素上的菜单。
*   要对一个组或组内的所有元素执行任何操作，用户需要调用该组上的菜单。（这意味着在组内的任意空白位置调用菜单。）
*   要对视图中的所有元素执行任何操作，用户需要在窗体的任意空白位置调用菜单。

将这些简单规则应用于任何应用程序意味着一个重要的事情：用户在开始使用新应用程序之前无需阅读长篇手册。这就像使用一辆新车：你不需要说明书来找到刹车踏板。

将这些规则应用于函数查看器，几乎找不到任何无法调用菜单的内部点。在此示例中，菜单仅不能在组内的小按钮上调用。（我只是想不出这个按钮所需的任何命令。）图 7-56 演示了在所有其他点上调用菜单的结果。看到所有这些菜单，用户对函数查看器的控制可能会显得过于复杂，但实际上并非如此。在工作中的应用程序中，你永远不会同时看到多个菜单，而且这恰好就是你想看到的菜单。如果你想更改某个元素，请调用该元素上的菜单。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig66_HTML.jpg](img/501148_1_En_7_Fig66_HTML.jpg)

图 7-56 蓝色线条显示了菜单所关联的区域。右键菜单可以在任意空白位置调用。处理视图的命令包含在主菜单中

到目前为止，我已经演示了组织 `Y(x)` 类型函数的过程。当前的函数查看器也允许您处理 `{X(p), Y(p)}` 类型的参数函数，这可以通过另一种辅助形式来定义。要声明此类函数，用户需要为两个函数输入表达式，并为参数的范围和步长输入三个值。因此，`Form_FuncXrYr.cs` 包含更多元素，但除此之外，它的设计方式与之前类似（图 7-57）。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig67_HTML.jpg](img/501148_1_En_7_Fig67_HTML.jpg)

图 7-57

此辅助形式用于定义 `{X(p), Y(p)}` 类型的参数函数。

`Y(x)` 函数比参数函数更为常见，而第二类函数之所以被纳入函数查看器，主要是因为这类函数中的一种在我的记忆中占据着特殊的位置。许多年前，在我甚至还没到上学年龄的时候，我的父亲——莫斯科电信学院的教授——带着我和哥哥去了实验室。为了让我们有事可做，父亲打开了一台示波器，并教我们如何摆弄利萨茹曲线^(11)。这个新奇玩意儿让两个孩子兴奋不已，以至于我们宣布暂时休战，并尝试合作，以便在小屏幕上获得最美妙的曲线。几十年后，当我决定为我的函数查看器添加一些特别功能时，我加入了处理参数函数的能力，而程序中这一部分首个被测试的便是利萨茹曲线。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Figd_HTML.jpg](img/501148_1_En_7_Figd_HTML.jpg)

通过使用两个辅助形式（图 7-51 和图 7-57），可以准备许多所需的函数及其变体。用于调整绘图区域、比例和注释的表单允许您组织不同的视图，并在多个绘图区域中以不同组合展示这些函数。您可以准备任意数量的绘图，但屏幕尺寸有限，因此始终存在一个关于如何良好展示以及如何保存/恢复已准备图像而不丢失任何一个的问题。在我自己的电脑上工作时，我倾向于通过注册表来保存/恢复。当函数数量众多，或者我需要与他人交换结果时，通过二进制文件保存/恢复则更为合适。函数查看器通过其主菜单的命令提供了此功能（图 7-56，左上角的菜单）。

函数查看器作为一个示例，展示了开发新工程和科学应用程序的主要风格。绘图是此类程序的重要组成部分，我想演示如何在新应用程序中包含可移动、可调整大小、可调节的绘图。您可以在我的另一本书 [2] 中找到更多相关信息。

每一个新的科学任务都需要一个新的程序。如果您在研究工作中对科学家施加过多限制，就不要期望得到非凡的成果。他们所受的限制越少，其研究成果就越有趣。程序应当帮助科学家工作，而非限制他们探索新事物的尝试。

多年前，在开始使用那些用户驱动的应用程序后，数学建模系的科学家们宣称，所有新程序都必须采用这种类型。顺便提一下，他们问了一个简单的问题：“你们能否将这种完全的用户控制功能添加到我们仍在使用的所有旧程序中？”

## 几何光学示例

一些科学实验，尤其是物理学实验，可能成本高昂，需要大量时间和精力准备，甚至可能具有危险性。有些实验甚至无法在地球上组织进行。计算机的广泛使用以及编程领域的成就，促使人们萌生了从真实实验转向模型模拟的想法。

在组织真实实验时，您可以自由决定如何使用设备及所有相关元素。而在使用由他人设计的模拟程序时，您会受到其设计者对所提模型观点的限制，并且无法超越这种观点。您只能做该模型开发者认为正确的事情。通常，每个特定领域的科学家都比他们不得不使用的程序的开发者更专业。这种知识与理解水平上的差异导致了一种矛盾的局面：科学家们在寻求新颖和意想不到的结果时，却被水平较低的专业人员的视野所限制。用户驱动的应用程序指明了走出这种编程死胡同的道路。

计算机上对不同过程的模型，通常试图重现科学家们在现实世界中希望看到的真实过程。真实实验总是存在一些限制，而采用标准编程技术开发的模型也会被设计成带有同样的限制。然而，有几个非常著名的例子表明，真实实验中的设备被意外地以非常规方式使用，从而产生了一些奇怪的结果，最终获得了诺贝尔奖或促成了著名的发明（仅举两例，1895 年 X 射线的发现和 1945 年微波炉的发明）。如果一个编程模型以标准方式设计，一切都被其开发者预先决定，那么这样的模型永远不会允许我们看到意想不到的结果。关于所有编程模型的设计，存在一个根本性问题：“是否有可能开发出一个程序（模型），让你能够获得超出开发者知识范围的结果？”

用户驱动的应用程序对此问题给出了肯定的回答。这是因为新型程序被设计为研究和分析的工具。它们不是纯粹的科学计算器，而是用户可以以任何方式使用的研究工具。

汽车制造商生产汽车，并保证它们在合理条件下能够行驶。汽车制造商不会给你一份允许到达的目的地清单，因此你探索世界的欲望不受限制。用户驱动的应用程序也是如此。一个精心设计的程序就是一个研究工具；进一步的结果只取决于用户进行此类研究的意愿和能力。为了展示用户驱动应用程序的这些特性，我将从几何光学领域列举几个示例。

几何光学通常是物理课程中的一小部分，在我看来，这是该课程中最容易的部分之一。所有关于几何光学的书籍几乎都遵循标准的讲解顺序。它通常从光线直线传播开始；然后通过平面镜的讨论来说明入射角等于反射角。讨论球面镜时会涉及成像的构建。光从一种介质进入另一种介质时，会从折射率和斯涅尔定律^(12) 开始讲解。接着是光的色散。之后，讨论薄透镜中的成像。

# 2019 年，我撰写了一本名为《几何光学图解》[3]的小书，其中包含一个涵盖所有主题示例的程序。本节收录了该书中的三个示例。这本提到的书分为两部分：第一部分从光学角度进行解释，第二部分则涉及所有示例的编程。在本节中，几何光学的解释被精简到最低必要水平，而我则着重强调新设计对程序用户带来的优势。让我们从平面镜开始；这个示例完美地展示了所有示例的设计。

## 平面镜示例

*   文件：`Form_Mirror_Flat_Any.cs`
*   菜单位置：应用程序 ➤ 几何光学 ➤ 平面镜

在此示例中，用户可以处理任意数量的平面镜（图 7-58）。每个镜子都可以单独放置和旋转。镜子的数量可以轻松更改，这些特性使您能够组织出非常奇特的光线路径。这看起来像是一个有趣的游戏，但从我决定设计这个示例的那一刻起，我就想起了阿尔伯特·迈克尔逊的那次著名实验——光在威尔逊山天文台和瞭望山之间传播。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig68_HTML.jpg](img/501148_1_En_7_Fig68_HTML.jpg)

**图 7-58** — 在此示例中可以使用任意数量的镜子

每个镜子（属于 `Mirror_Flat` 类）都是矩形形状。其覆盖层的设计对所有矩形而言都是标准化的。用户可以决定每个对象是否允许调整大小，但通常情况下，矩形可以通过边和角来调整大小。镜子有一个反射面，可以通过使用不同的颜色来突出显示。另外三个面则吸收光线。

光源（`Flashlight` 类）被设计成手电筒的形式。在以下示例中，这个手电筒是唯一不可调整大小的元素；我实在想象不出可调整大小的光源有什么必要，尽管组织起来很容易。在真实实验中，不可能将一对光学对象（镜子和手电筒）放置在相同位置，因此当前示例中也禁止了这种重叠。

光线路径的计算方式如下：

*   初始光线的点及其角度从手电筒获取。
*   光线可以触及任何镜子，每个镜子有四个面，每个面都是由两个端点确定的线段。反射后的光线可以触及手电筒。手电筒的边界由八个直线段组成。因此，对于一个包含 `N` 个镜子的系统，我们有 `4 * N + 8` 个可能与光线相交的线段。系统会计算出最近的交点。
*   如果没有交点，那么这就是路径中的最后一程。（在图 7-58 中，光线在镜子 A 的中间被反射，然后延伸到窗体的底边之外。）
*   如果光线触及手电筒或任何镜子的非反射面，则计算结束。
*   如果光线触及镜子的反射面，则该接触点成为新的光源点，反射角等于入射角，然后重新开始计算。

当计算显示光线接触到了某个边界时，系统会在此边界上组织一个光学边界（`OpticalBorder` 类的元素）。该元素允许您处理吸收、反射或折射。虽然当前示例中只发生吸收和反射，但折射的情况将在后续示例中用到。

这个示例基于简单的元素——矩形——但即使是这个最简单的基元，也允许您组织起截然不同的示例。几乎在每本几何光学书籍中，你都能看到一个直角形状的反射器，其内部有两个反射面。这种设计完美地展示了入射角等于反射角的原理。在当前示例中，可以通过放置两个互成直角的镜子，并将它们的反射面端点相邻放置来构造同样的反射器。另一种有趣的设计是创建一个多边形，其反射面朝向内部。留一个小缝隙，让光线可以射入内部，然后观察结果。

我只提到了两种可能性，但一个由用户驱动的应用程序对用户的想象力没有设限。

## 折射与斯涅尔定律

光在任何材料中的速度都小于真空中的速度。在真空中的速度（`c`）与在材料中的速度（`v`）之比，称为该材料的*折射率*：`n = c / v`。由于`v`总是小于`c`，因此任何材料的折射率都大于 1。折射率略微依赖于波长，但对于可见光谱中的波，这种差异仅体现在小数点后第二位甚至第三位上。这种微小差异是色散的原因，这在书[3]中有讨论，但本书不涉及。在以下示例中，使用了单色光线，因此每种材料都使用单一的折射率。

当光线到达两种材料的界面时，一部分光线被反射，另一部分则进入另一种材料。反射角等于入射角，而折射角可以通过斯涅尔定律求得。

`n[1] * Sin ά[1] = n[2] * Sin ά[2]`

*   `n[1]` 和 `n[2]` — 两种材料的折射率
*   `ά[1]` — 入射角
*   `ά[2]` — 折射角

| 材料 | 折射率 |
| --- | --- |
| 空气 | 1.0003 |
| 水 | 1.33 |
| 玻璃 | 1.5 |
| 金刚石 | 2.48 |

有两类经典的折射示例，在每本几何光学书籍中都会被讨论：光穿过窗户的传播，以及从湖（或河）岸看水中某人的景象。这两个示例之所以流行，是因为我们一生中会多次看到它们。

### 从光疏介质到光密介质

当光线从光疏介质进入光密介质时，例如从空气进入水（图 7-59），那么 `n[1] < n[2]`，这意味着 `ά[1] > ά[2]`，因此折射光线（在此例中是在水中）会向法线方向弯曲。这对于任何入射角都同样适用。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig69_HTML.jpg](img/501148_1_En_7_Fig69_HTML.jpg)

**图 7-59** — 对于从光疏介质进入光密介质的光线，折射光线总是向法线方向弯曲

现在想象一下，在手电筒的位置上是岸边某人的眼睛，而红线则显示了水中某人脚部反射的光线路径。眼睛捕捉到光线并看到了脚，但大脑对光线的弯曲一无所知。大脑认为整条光线是笔直的，因此大脑“看到”水中的脚位于上方光线的延长线上。结果，水中人的腿看起来比实际要短，而水面以上的身体部分则没有变形。

### 斯涅尔定律与折射

斯涅尔定律并未对光线方向做出任何限制，因此当光线从光密介质传播到光疏介质时，该定律同样适用。只是此时`n[1] > n[2]`，所以两个角度之间存在不同的关系，且当`ά[1] < ά[2]`时，折射光线会偏离法线（图 7-60a）。让我们从某个较小的入射角开始，并逐渐增大它。在当前程序中可以轻松实现这一点，因为你可以按下手电筒并旋转它。在一段时间内，斯涅尔定律仍然有效；折射光线越来越偏离法线，并趋近于界面（图 7-60b）。在我们的案例中，边界线是水平的，因此很容易观察和估计角度如何变化。

临界角是斯涅尔方程仍然成立，但`Sin ά[2] = 1`时的角度。对于水-空气边界，当入射角为 47.77 度时出现该情况。对于更大的入射角，方程只有在`Sin ά[2] > 1`时才成立，但这不可能，因此不会发生折射，而是发生全内反射（图 7-60c）。像往常一样，反射角等于入射角。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig72_HTML.jpg](img/501148_1_En_7_Fig72_HTML.jpg)

**图 7-60c** 入射角大于临界角时，发生全内反射

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig71_HTML.png](img/501148_1_En_7_Fig71_HTML.png)

**图 7-60b** 当入射角接近临界角时，折射光线几乎沿界面传播

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig70_HTML.jpg](img/501148_1_En_7_Fig70_HTML.jpg)

**图 7-60a** 入射角小于临界角时，根据斯涅尔定律发生正常折射

图 7-59 中的情况很容易解释和理解，因为它在我们一生中多次发生，我们非常熟悉透过水面看到的物体扭曲现象。图 7-60 中的情况很容易解释，但不太容易理解，因为我们并不生活在水下，在这种情况下，我们只能依靠数学而非感觉。让我们再看一个熟悉的情况。

当光线穿过窗户玻璃时（图 7-61），在两个边界都会发生折射。首先，空气-玻璃边界使光线向法线方向弯曲。然后，这条光线到达玻璃-空气边界，在那里使用相同的一对折射率但顺序相反，使光线偏离法线。因为两个边界使用了相同的一对折射率但作用方向相反，所以第二个角度变化的结果与第一个相同但符号相反，因此出射光线与原始光线平行。只是在传播过程中发生了侧向位移；图 7-61 中清晰可见。这正是我们透过任何窗户看到的情况。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig73_HTML.jpg](img/501148_1_En_7_Fig73_HTML.jpg)

**图 7-61** 第二个边界上的折射使出射光线与原始光线平行

前面提到的两种情形——从岸边的视角和透过窗户的视角——对每个人来说都很熟悉，因此它们的解释从不会引起任何兴奋。这两种情形都包含在下一个示例中。尽管这个示例也涉及一个熟悉的情况，但我相信你会看到一些完全意想不到的结果。

## 水族馆示例

*   文件：`Form_Aquarium.cs`
*   菜单位置：`Applications ➤ From Geometric Optics ➤ Aquarium`

# 水族箱中的光线路径

假设你有一个大型水族箱（比如海洋馆）。玻璃墙的一侧是游客区，游客有机会从不同高度观察水族箱。在玻璃墙的另一侧，水总是填满一半的体积（图 7-62）。由于我更关注光学效果而非这类水族箱的实际构造，因此玻璃墙可以轻松地左右移动，并且其厚度也可以在合理范围内以同样简单的方式改变。厚度不会影响所讨论过程的物理原理，但会使光线路径的变化更加明显。光源——手电筒——可以放置在空气中或水中的任何位置；但是，不允许出现手电筒与玻璃重叠的任何情况。

## 光线路径变体

图 7-62 中的光线路径变体是最简单的，代表了图 7-61 和图 7-59 中情形的序列。在三个边界上发生了三种普通的折射情况。首先，光线接触到玻璃墙的垂直侧面；然后，在玻璃墙的相对侧发生折射。在这两点，外部都是空气，因此第二次折射后的光线角度等于原始角度；每个特殊点之前和之后的光线角度都显示在一个列表中。第三次折射发生在空气-水边界。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig74_HTML.jpg](img/501148_1_En_7_Fig74_HTML.jpg)

**图 7-62** 玻璃墙从底部延伸到顶部；水填满了右侧一半的体积

组织任何光线路径最简单的方法是将手电筒放置在感兴趣的点，并将其旋转到所需角度。光学的主要定律之一是光的可逆性。这意味着，如果在光线路径上的任何位置放置另一个手电筒，并将其朝向与光线相反的方向，那么第二个手电筒发出的光线将精确地到达第一个手电筒上的光源点。我们看到物体是因为光线从物体反射并进入我们的眼睛。在我们水族箱的例子中，这意味着如果一位游客从我们光源（手电筒）的位置出发，沿着发出的光线方向观察，那么这位游客会看到光线路径上的物体。

## 家庭参观的观察差异

假设一对父母带着小孩参观海洋馆；身高差对于后续讨论很重要。家庭成员站在玻璃墙附近，寻找另一侧的奇特生物。一个成年人看到前方有令人惊叹的东西（图 7-63a），并告诉孩子们往那里看。他们尝试了，但为了看到提到的那个生物，他们必须以完全不同的角度观看（图 7-63b）。也许他们会看到同一个生物，但由于光线在玻璃墙两侧的转向，他们很可能错过它。父母坚持认为前面确实有令人惊叹的东西，所以孩子们为了找到它，稍微改变了一下观看角度。他们没有看到紧靠玻璃墙的东西，反而有机会看到海洋馆底部远处角落的生物（由于页面限制，图 7-63c 未显示海洋馆的全尺寸，但你可以轻松延长光线路径的最后一段）。孩子们根本没有想到海洋馆的那个远处部分，但他们现在看到了。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig77_HTML.jpg](img/501148_1_En_7_Fig77_HTML.jpg)

**图 7-63c** 这条光线到达了远离其初始方向的某处

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig76_HTML.jpg](img/501148_1_En_7_Fig76_HTML.jpg)

**图 7-63b** 在水中因折射而精确抵达同一位置并非易事。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig75_HTML.jpg](img/501148_1_En_7_Fig75_HTML.jpg)

**图 7-63a** 这条光线几乎是笔直的。

这并非我为了增加文本趣味性而设想的人造情景。图 7-63c 让我想起了一个困扰我多年的未解之谜。童年时，我家有一个鱼缸放在书柜顶上。那书柜几乎是我当时身高的两倍。为了看鱼，我得仰起头，才能看到紧贴玻璃的鱼。如果我站在离书柜稍远的地方，以一个角度仰视，有时能看到鱼缸最远端的水下洞穴。我无法直接看到它，因为视线被书柜的上部挡住了。这种偶尔瞥见隐藏角落的情形让我困惑不已，但直到多年后我才了解到全内反射。如今多年过去，我能轻易解释童年那段插曲。那些隐藏在鱼缸角落的奇特画面，多年来一直深藏在我记忆深处，直到程序向我展示了图 7-63c 中的路径，它们才重新浮现。

借助这个程序，我试图证明：如果用户能完全控制所有屏幕元素，那么由用户发起的一个微小改变，就能让你从预期的结果转向意想不到的结果。在科学领域，这两种结果都可能具有价值。预期结果能让你证明实验前所作假设的正确性。非预期结果则表明，实验所依据的理论可能是部分错误，甚至是完全错误的。同样的实验（图 7-62）让我看到了其他结果，而在设计这个例子之前，我无法对此做出任何预测。

## 水下生物的视角

你能说出当水下生物从海洋馆内部向外看时，它们看到了什么吗？为了避免讨论非人类心智的问题，我们假设里面有美人鱼在表演，或者有科学家/维护人员在作业。它们能看到玻璃墙外的游客吗？我认为不会有太多游客费心去想这类问题。在我们的日常生活中，如果我们能看到某人，那么那个人通常也能看到我们。光的可逆性意味着，如果光线沿反方向发射，它会沿着相同的路径返回。如果我们靠近玻璃墙观看另一侧的各种生物，那么那些生物也能看到我们。这个定律在生物恰好沿着光线路径的反方向观察时总是成立，但如果角度不同呢？

图 7-64 展示了当有人从水下的同一位置，以不同角度看向玻璃墙时的路径变化。我得说，只有图 7-64a 中的结果是预期的，而其他三个都是非预期的。当生物以相对较小的入射角观察时（图 7-64a），在两个边界上会因折射产生一些畸变，但至少能看到外部的一些景物。当入射角增大时，水下生物就看不到游客了。图 7-64b、7-64c 和 7-64d 展示了入射角逐渐增大时的各种情况；我不知道有多少读者能预料到这样的结果。我原本也没想到这些结果，若非在当前示例中手电筒可以移动和旋转，你根本不可能看到它们。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig81_HTML.jpg](img/501148_1_En_7_Fig81_HTML.jpg)

**图 7-64d** 首先是普通折射点，然后是一系列全内反射。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig80_HTML.jpg](img/501148_1_En_7_Fig80_HTML.jpg)

**图 7-64c** 相同的折射和全内反射序列，但最后一段光线贴到了玻璃上。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig79_HTML.jpg](img/501148_1_En_7_Fig79_HTML.jpg)

**图 7-64b** 三个普通折射点和一个全内反射点。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig78_HTML.jpg](img/501148_1_En_7_Fig78_HTML.jpg)

**图 7-64a** 两个普通折射点。

优秀的物理书不仅会给出解释，还会要求读者回答一些问题。在标准设计的书籍中，读者需要做一些计算来回答这些问题。本书附带了一个程序，让读者能够扮演实验者的角色。

假设你正在参观同一个海洋馆，你没有紧贴玻璃墙站立，而是站在远处地面最左边的角落里。（在图 7-62 中，这是黄色矩形左下角的位置。）从那里有可能看到最右边水面上方的东西吗？

*   如果可以，那么你需要看向哪里？
*   如果不行，你需要移动到何处，并从新位置看向哪里？

从编程的角度来看，这个例子与上一个例子同样简单。四个直线段描述了海洋馆内部的边界；还有八个标准线段用于描述手电筒的边界。我从未找到能返回手电筒的光线路径，所以这八个线段看起来是多余的，但我仍然把它们算上，以保持路径计算方法的一致性。在海洋馆内部的四条边界上，可能会发生折射或全内反射；这取决于每条特定边界和入射角。

这个例子完美地展示了标准设计程序与用户驱动应用程序之间的区别。为了解释折射，标准设计的程序会演示图 7-59（中间情况）、7-60a 和 7-60c 中的情况。而新设计能做的远多于此。它让你能轻松看到光线穿过窗玻璃以及光线射向水面的标准情况。如果学生对物理毫无兴趣，他们可以看看标准情况，然后就把程序抛之脑后。但对于那些想要摆弄光线和边界的人来说，这里有无数的机会。还有些有趣的结果我没有提及，甚至可能我自己都还没发现。可能结果的多样性取决于个人玩弄相关元素的意愿和能力。因此，实验者越熟练，得到的结果就可能越有趣。这就是用户驱动应用程序在科学和教育领域应用时的力量所在。

## 棱镜实验

让我们再试一个例子。

*   文件：`Form_Prism_RegularPolygon.cs`
*   菜单位置：应用程序 ➤ 几何光学 ➤ 棱镜（正多边形）

大约 350 年前，艾萨克·牛顿用三棱镜进行了他著名的实验，以解释光的起源。自那时起，一幅白色光线入射一个平面，然后从另一个平面射出七束彩色光线（或光锥）的三棱镜图像，出现在每一本几何光学书籍中。为了证明预期结果和非预期结果之间的差别微乎其微，我可以问一个简单的问题：“是否可能看到从第二个平面射出少于七束的彩色光锥？”（书中 [3] 关于三棱镜的例子能帮你找到答案。）

### 三角棱镜中射出的七色光锥

# 光的色散与彩虹

三角棱镜中射出的七色光锥是光的色散的完美展示。这种色散的另一个更著名的例子是彩虹。为了解释彩虹现象，我们使用光线在水滴中的传播过程。水滴是圆形的。一个圆形可以看作是一个具有无限多个顶点（或平面）的正多边形。因此，我们在三角棱镜中观察到光的色散，而借助具有无限多个平面的棱镜，我们观察到了色散（彩虹）。（准确地说，彩虹是光在一组水滴上发生色散的结果，每种颜色的光线来自不同的水滴。）当白光照射到具有三个以上平面的正多边形时会发生什么？当这个数字改变时又会发生什么？

进行真实实验需要准备整套棱镜，这并非易事；而用计算机建模会容易得多。屏幕上三角棱镜转变为具有可变平面数量的另一形状的过程很快。我最初看到的结果与三角棱镜相似，所以起初这种棱镜变换并没有产生任何出人意料的结果。接着我开始观察到一些截然不同的现象；过了一段时间，我意识到类似的结果在埃莉诺·H·波特所著的著名小说《波莉安娜》[6]中已有描述。

> *“波莉安娜还没在阳光照射的窗户上挂好三个吊坠，就开始略微明白将会发生什么。她兴奋得手指颤抖，几乎无法控制地挂好剩下的吊坠。但任务终于完成，她后退一步，发出了一声低低的欢呼。*
>
> *那间华丽却沉闷的卧室变成了仙境。到处都是舞动着的红色、绿色、紫色、橙色、金色和蓝色的光斑。墙壁、地板、家具，甚至床本身，都闪耀着波光粼粼的色彩。”*

这无疑是白光被分解成各色成分的过程，但这色散有点奇特，并非我们在彩虹或三角棱镜中看到的那种。在彩虹中，我们总能同时看到七种颜色并列出现；它们绝不会在天空的不同区域彼此分离。在等边三角棱镜的情况下，各色光锥也保持在一起，分散光线中红色和紫色部分之间的最大夹角大约为 10 度。从这样一个棱镜中射出的光会使彩色光斑彼此相邻排列。书中生动的描述则提到房间里到处都是彩色光斑，因此产生这种画面的原因必定有所不同；图 7-65 展示了这是如何发生的。为了避免将所有不同颜色混杂在一张图中，我针对不同光成分准备了三张不同的图。所涉及元素的几何结构对于三者来说是相同的。现在想象一下，太阳光以图中所示的方式照射到吊坠上。白光发生色散；然后每种成分将沿自己的路径传播，并以各自的角度射出。结果，我们在完全不同的位置得到了不同颜色的光斑。

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig84_HTML.jpg](img/501148_1_En_7_Fig84_HTML.jpg)

图 7-65c

紫色光在内部旋转近 4 圈，经过 25 次内反射后射出

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig83_HTML.jpg](img/501148_1_En_7_Fig83_HTML.jpg)

图 7-65b

蓝色光经过 13 次内反射后射出

![../images/501148_1_En_7_Chapter/501148_1_En_7_Fig82_HTML.jpg](img/501148_1_En_7_Fig82_HTML.jpg)

图 7-65a

红色光经过一次内反射点后射出

当我设计一个正多边形棱镜的例子时，我预料到这样的结果了吗？当然没有，尽管我知道宝石切割工艺已经使用了几个世纪。（我们可以跳过关于我预测结果能力的讨论，但至少我是诚实的。）幸运的是，该程序被设计成一个用户驱动的应用程序，因此用户（就是我！）可以对所涉及的元素做任何想做的事。像往常一样，这位用户决定摆弄屏幕上的元素，并在此过程中得到了设计者（也是我！）从未想到过的结果。这对于“科学程序是否可能产生意外结果？”这一基本问题给出了完美的答案。

脚注 1 2 3 4
