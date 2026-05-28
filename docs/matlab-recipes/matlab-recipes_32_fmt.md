# 11. 飞机

电子补充材料：本章的在线版本 (doi:[10.1007/978-1-4842-0559-4_11](http://dx.doi.org/10.1007/978-1-4842-0559-4_11)) 包含授权用户可获取的补充材料。

我们的飞机模型是一个三维质点。该模型仅模拟三个维度上的平动动力学。平动是指在 `x`、`y` 和 `z` 方向上的运动。飞机通过改变其相对于风向的姿态（倾斜和迎角）以及改变其发动机产生的推力来控制其运动。在我们的模型中，我们假设飞机能够为了控制目的而瞬间改变其姿态和推力。这简化了我们的模型，但同时也使我们能够模拟大多数飞机操作，例如起飞、水平飞行和着陆。我们还假设飞机的质量不随时间变化。

## 11.1 创建飞机的动态模型

### 问题

您需要一个数值模型来模拟飞机在大气中的三维轨迹。该模型应允许您演示从起飞到着陆的飞机控制过程。

### 解决方案

您将使用飞行路径坐标构建一个六状态模型。控制量将是滚转角、迎角和推力。您不会模拟飞机的姿态动力学。如果您想模拟飞机改变迎角和滚转角所需的时间，那么姿态动力学是必需的。在我们的模型中，您假设飞机可以瞬间改变迎角、滚转角和推力。

### 工作原理

我们的飞行器将具有六个状态量，用于在三维空间中模拟速度和位置，以及三个控制量。这三个控制量分别是滚转角 `φ`、攻角 `α` 和推力 `T`。你不会将笛卡尔坐标及其时间导数（即速度）用作状态量；相反，你将使用航迹坐标。图 11-1 展示了二维情况下的航迹坐标。滚转角 `φ` 是绕 `x` 轴的旋转，航向角 `ψ` 垂直于纸面。阻力 `D` 与速度矢量方向相反。

![A335353_1_En_11_Fig1_HTML.jpg](img/A335353_1_En_11_Fig1_HTML.jpg)

**图 11-1.** 二维航迹坐标

攻角 `α` 用于调节升力 `L` 和阻力。推力矢量 `T` 与机体 `x` 轴对齐。航迹角 `γ` 是 `x` 轴与速度矢量 `V` 之间的夹角。状态矢量 `s` 为：

![ $$ s=\left[\begin{array}{r}\hfill v\\ {}\hfill \gamma \\ {}\hfill \psi \\ {}\hfill x\\ {}\hfill y\\ {}\hfill h\end{array}\right] $$ ](A335353_1_En_11_Chapter_Equ1.gif)

(11.1) 其中 `v` 是速度大小；`γ` 是 `xz` 平面内的航迹角；`ψ` 是 `xy` 平面内的航向角；`x`、`y`、`h` 是笛卡尔坐标。`h` 是高度，即 `z` 坐标。动力学方程为：

![ $$ \left[\begin{array}{r}\hfill \dot{v}\\ {}\hfill \dot{\gamma}\\ {}\hfill \dot{\psi}\\ {}\hfill \dot{x}\\ {}\hfill \dot{y}\\ {}\hfill \dot{h}\end{array}\right]=\left[\begin{array}{l}\frac{T \cos \alpha -D}{m}-g \cos \gamma \hfill \\ {}\frac{\left(L+T \sin \alpha \right) \cos \phi - mg \cos \gamma }{mv}\hfill \\ {}\frac{\left(L+T \sin \alpha \right) \sin \phi }{mv \cos \gamma}\hfill \\ {}v \cos \gamma \cos \psi \hfill \\ {}v \cos \gamma \sin \psi \hfill \\ {}v \sin \gamma \hfill \end{array}\right] $$ ](A335353_1_En_11_Chapter_Equ2.gif)

(11.2) 其中 `g` 是重力加速度，`m` 是质量，`D` 是阻力，`L` 是升力。定义动压为：

![ $$ q=\frac{1}{2}\rho {v}² $$ ](img/A335353_1_En_11_Chapter_Equ3.gif)

(11.3) 其中 `ρ` 是大气密度。我们简单的升力和阻力模型为：

![ $$ D=q\left({C}_{D_0}+k{C}_L²\right) $$ ](img/A335353_1_En_11_Chapter_Equ4.gif)

(11.4)

![ $$ L=q{C}_L $$ ](img/A335353_1_En_11_Chapter_Equ5.gif)

(11.5)

![ $$ {C}_L={C}_{L_{\alpha }}\alpha $$ ](img/A335353_1_En_11_Chapter_Equ6.gif)

(11.6)

第一个方程称为阻力极线。`C_D0` 是零升力时的阻力。`k` 是升力-阻力耦合系数。`s` 是浸润面积。此升力模型仅适用于小攻角 `α`，因为它未考虑失速——失速是指气流从机翼分离，升力迅速降至零的情况。

实现这些方程的函数是我们的 `RHS` 函数，名为 `RHSAircraft`。请注意，当方程 11.2 中 `v = 0` 时，这些方程存在奇点。函数头提供了关于该奇点的警告。

```
%% RHSAIRCRAFT 六自由度质点飞行器模型。
%% 格式：
% d = RHSAircraft;
% [sDot, D, LStar] = RHSAircraft( t, s, d )
%% 描述
% 计算质点飞行器的右侧函数。若调用时不带任何参数，则返回默认数据结构。
% 当 v = 0 时， sDot(2) 和 sDot(3) 将为无穷大。默认的大气模型是
% AtmosphericDensity，该模型使用指数大气模型。
%
%% 输入
% t (1,1) 时间（未使用）
% s (6,1) 状态矢量 [v;gamma;psi;x;y;h]
% d (.) 数据结构
% .m (1,1) 飞行器质量
% .g (1,1) 重力加速度
% .thrust (1,1) 推力
% .alpha (1,1) 攻角
% .phi (1,1) 滚转角
% .s (1,1) 表面积
% .cD0 (1,1) 零升力阻力
% .k (1,1) 升力-阻力耦合项
% .cLAlpha (1,1) 升力系数
% .density (1,1) 指向大气密度函数的函数句柄
%
%% 输出
% sDot (6,1) 状态矢量导数 d[v;gamma;psi;x;y;h]/dt
% D (1,1) 阻力
% LStar (1,1) 升力/攻角
```

接下来是函数体。由于各项比较简单，将状态导数 `sDot` 组装成一个数组。为了提高可读性，每个元素单独一行。返回 `D` 和 `LStar` 作为辅助输出，供平衡计算使用。

```
function [sDot, D, LStar] = RHSAircraft( ~, s, d )

% 默认数据结构
if ( nargin == 0 )
    sDot = struct('m',5000, 'g', 9.806, 'thrust',0,'alpha',0, 'phi',0,...
        'cLAlpha',2*pi,'cD0',0.006,'k',0.06,'s',20,'density',...
        @AtmosphericDensity);
    return
end

% 保存为局部变量
V = s(1);
gamma = s(2);
psi = s(3);
h = s(6);

% 三角函数
cG = cos(gamma);
sG = sin(gamma);
cPsi = cos(psi);
sPsi = sin(psi);
cB = cos(d.phi);
sB = sin(d.phi);

% 指数大气模型
rho = feval(d.density,h);

% 升力和阻力
qS = 0.5*rho*d.s*v²;   % 动压
cL = d.cLAlpha*d.alpha;
cD = d.cD0 + d.k*cL²;
LStar = qS*d.cLAlpha;
L = qS*cL;
D = qS*cD;

% 速度导数
% sDot 是 d[v;gamma;psi;x;y;h]/dt
lT = L + d.thrust*sin(d.alpha);
sDot = [ (d.thrust*cos(d.alpha) - D)/d.m - d.g*sG;...
         (lT*cB - d.m*d.g*cG)/(d.m*v);...
         lT*sB/(d.m*v*cG);...
         v*cPsi*cG;...
         v*sPsi*cG;...
         v*sG];
```

更复杂的右侧函数会为阻力和升力计算传入函数句柄，从而允许用户使用自己的模型。我们为大气密度计算传入了一个函数句柄，以允许用户选择自己的密度函数。我们本可以对气动模型也采用同样的做法。这将使 `RHSAircraft` 更加灵活。

注意，我们必须编写一个大气密度模型 `AtmosphericDensity`，作为 `RHS` 函数的默认值。该模型使用指数方程计算密度，这是最简单的表示形式。该函数自带一个模型演示，绘图时使用了对数刻度。

```
%% AtmosphericDensity 基于指数模型的大气密度。
% 使用指数模型计算给定高度的大气密度。提供最高海拔 100 公里的演示图。
%% 格式：
% rho = AtmosphericDensity( h )
%
%% 输入
% h (1,:) 海拔高度 (m)
%
%% 输出
% rho (1,:) 密度 (kg/m³)

function rho = AtmosphericDensity( h )

% 演示
if ( nargin < 1 )
    h = linspace(0,100000);
    AtmosphericDensity( h );
    return
end

% 密度
rho = 1.225*exp(-2.9e-05*h.¹.15);

% 若无输出请求则绘图
if ( nargout < 1 )
    PlotSet(h,rho,'x label','h (m)','y label','Density (kg/m³)',...
        'figure title','Exponential Atmosphere',...
        'plot title','Exponential Atmosphere',...
        'plot type','y log');
    clear rho
end
```

## 11.2 使用数值搜索寻找飞行器的平衡控制量

### 问题

你希望找到使速度、航迹角和倾斜角状态（滚转角）导数为零的滚转角、推力和攻角。这就是一个平衡点。

### 解决方案

你将使用下山单纯形法，通过 `MATLAB` 函数 `fminsearch` 来寻找平衡角度。

### 工作原理

第一步是找到能够产生所需平衡状态（即设定点）的控制量。将设定点定义为向量

![$$ \left[\begin{array}{l}{v}_s\\ {}{\gamma}_s\\ {}{\psi}_s\end{array}\right] $$](A335353_1_En_11_Chapter_Equ7.gif)

(11.7) 其中包含速度 `${v}_s`、航向角 `${\psi}_s$` 和飞行路径角 `${\gamma}_s$` 的设定值。将这些值代入方程 11.2 中的前三个动力学方程，并将等号左侧设为零。

![$$ 0=\left[\begin{array}{l}T \cos \alpha -D\left({v}_s,\alpha \right)- mg \cos {\gamma}_s\\ {}\left(L\left({v}_s,\alpha \right)+T \sin \alpha \right) \cos \phi - mg \cos {\gamma}_s\\ {}\left(L\left({v}_s,\alpha \right)+T \sin \alpha \right) \sin \phi \end{array}\right] $$](A335353_1_En_11_Chapter_Equ8.gif)

(11.8)

控制量为攻角 `$\alpha$`、滚转角 `$\phi$` 和推力 `$T$`。由于三个方程对应三个未知数，因此可以得到唯一解。求解平衡控制量最简单的方法是使用 `fminsearch`。该程序会找到能使三个方程归零的三个控制量。

函数 `EquilibriumControl.m` 在循环中使用 `fminsearch` 来处理多个状态。在循环内部，你需要计算控制量的初始猜测值。推力需要平衡阻力，因此你在零攻角下进行计算。升力必须平衡重力，因此你根据这一关系计算攻角。如果没有合理的初始猜测值，算法可能无法收敛，或者收敛到局部最小值。代价函数嵌套在控制函数内部。

```
function [u, c] = EquilibriumControl( x, d, tol )
n = size(x,2);
u = zeros(3,n);
c = zeros(1,n);
p = optimset('TolFun',tol);
% additional options during testing:
% 'PlotFcns',{@optimplotfval,@PlotIteration},'Display','iter','MaxIter',50);
for k = 1:n
[˜,D,LStar] = RHSAircraft(0,x(:,k),d);
alpha = d.m*d.g/LStar;
u0 = [D;alpha;0];
[umin,cval,exitflag,output] = fminsearch( @Cost, u0, p, x(:,k), d );
u(:,k) = umin;
c(k) = Cost( u(:,k), x(:,k), d );
end
```

默认输出是绘制结果。

```
% Plot if no outputs are specified
if ( nargout == 0 )
yL = {'T (N)', '\alpha (rad)', '\phi (rad)' 'Cost'};
s = 'Equilibrium Control:Controls';
PlotSet(1:n,[u;c],'x label','set','y label',yL, ...
'plot title',s, 'figure title',s);
yL = {'v' '\gamma' '\psi' 'h'};
s = 'Equilibrium Control:States';
PlotSet(1:n,x([1:3 6],:),'x label','set','y label',yL, ...
'plot title',s,'figure title',s);
clear u
end
```

接下来展示代价函数。使用一个二次代价函数，即状态导数平方的无权重和。

```
%% Find the cost of a given control
function c = Cost( u, x, d )
d.thrust = u(1);
d.alpha = u(2);
d.phi = u(3);
xDot = RHSAircraft(0,x,d);
y = xDot(1:3);
c = sqrt(y'*y);
```

该函数内置了一个演示，考察在恒定速度下，从 0 到 10 km 高度递增时的推力和攻角变化。

```
if( nargin < 1 )
x = [200*ones(1,101);...
zeros(4,101);...
linspace(0,10000,101)];
d = RHSAircraft;
EquilibriumControl( x, d )
return;
end
```

图 11-2 显示了在默认演示中计算控制量所对应的状态。图 11-3 显示了最终得到的控制量。正如预期，攻角随高度增加而增大，但推力却随高度增加而减小。空气密度降低会减小阻力和升力，因此你需要减小推力，同时增大攻角以产生更多升力。滚转角几乎为零。

![A335353_1_En_11_Fig3_HTML.jpg](img/A335353_1_En_11_Fig3_HTML.jpg)

`图 11-3.` 演示中的控制量。推力随高度增加而减小，攻角随高度增加而增大

![A335353_1_En_11_Fig2_HTML.jpg](img/A335353_1_En_11_Fig2_HTML.jpg)

`图 11-2.` 演示中的状态。仅有高度（`h`）在变化

图 11-3 还绘制了代价函数。如果函数按预期工作，代价应几乎为零。

在调试过程中，编写需要优化的函数时，对数值搜索过程有更多了解可能会很有帮助。虽然你只需要 `umin`，但在此函数调用的版本中，可以考虑 `fminsearch` 提供的其他输出。

```
[umin,cval,exitflag,output] = fminsearch( @Cost, u0, p, x(:,k), d )
```

输出结构中包含迭代次数，而退出标志指示函数的退出条件：是否达到容差（1）、是否超过最大允许迭代次数（0），或者用户提供的输出函数是否终止了搜索（-1）。我们在脚本中设置断点来检查这些输出。对于 `k = 4` 时 `v = 200` 且 `h = 300` 的状态，输出为：

因此你可以看到，搜索需要 141 次迭代，并且推力从我们初始猜测的 2880 N 增加到 3180 N。最终代价为 `3e^(-9)`。

获取更多信息的另一种方法是将 `fminsearch` 的 `Display` 选项设置为 `iter` 或 `final`，默认值为 `notify`。在这种情况下，选项如下所示：

```matlab
p = optimset('TolFun',tol,'Display','iter');
```

并且以下类型的输出会打印到命令窗口中：

```
>> d = RHSAircraft;
>> x = [200;0;0.5;0;0;5000];
>> EquilibriumControl( x, d )
Iteration Func-count min f(x) Procedure
0 1 0.0991747
1 4 0.0817065 initial simplex
2 6 0.0630496 expand
3 7 0.0630496 reflect
4 9 0.0133862 expand
5 10 0.0133862 reflect
6 11 0.0133862 reflect
7 13 0.0133862 contract outside
8 15 0.0133862 contract inside
...
49 87 2.1869e-05 contract inside
50 89 2.1869e-05 contract outside
Exiting: Maximum number of iterations has been exceeded
- increase MaxIter option.
Current function value: 0.000022
```

为了更深入了解，你可以添加一个在每次迭代时调用的绘图函数。MATLAB 提供了一些默认的绘图函数；例如，`optimplotfval` 会在每次迭代时绘制代价函数值。你实际上需要在编辑器中打开 `optimplotfval` 才能了解所需的语法。我们像这样将函数添加到优化选项中：

```matlab
p = optimset('TolFun',tol,'PlotFcns',@optimplotfval);
```

图 11-4 是根据演示的第一次迭代生成的。

![A335353_1_En_11_Fig4_HTML.jpg](img/A335353_1_En_11_Fig4_HTML.jpg)

图 11-4. 使用 `optimplotfval` 绘制的函数值图

你可以看到，在此图中，代价函数值在最后 100 次迭代中几乎保持不变。你可以使用 `PlotFcns` 的元胞数组添加更多绘图，MATLAB 会自动为每个绘图分配其自己的子图坐标轴。对于棘手的数值问题，你可能希望生成一个曲面并追踪优化的迭代过程。对于我们的问题，我们添加了自定义绘图函数 `PlotIteration`，结果如图 11-5 所示。

![A335353_1_En_11_Fig5_HTML.jpg](img/A335353_1_En_11_Fig5_HTML.jpg)

图 11-5. 使用曲面的自定义优化绘图函数

```matlab
p = optimset('TolFun',tol,'PlotFcns',{@optimplotfval,@PlotIteration});
```

我们编写了两个函数：一个用于生成曲面，另一个用于绘制迭代步骤。MATLAB 在初始化时将迭代值设为零，因此在这种情况下，你需要根据给定的初始状态生成曲面。对于所有其他迭代值，你在曲面上用星号绘制代价函数值。

```matlab
% Plot an iteration of the numerical search
function stop = PlotIteration (u0,optimValues,state,varargin)
stop = false;
x0 = varargin{1};
d = varargin{2};
switch state
case 'iter'
if optimValues.iteration == 0
a = PlotSurf( x0, u0, d );
end
plot3 (u0(1),u0(2),optimValues.fval,'k*');
end

% Plot a surface using the given initial state for a range of controls.
% MATLAB will already have an empty axis available for plotting.
function a = PlotSurf( x0, u0, d )
u1 = linspace( max(u0(1)-1000,0),u0(1)+1000);
u2 = linspace(0, max(2*u0(2),0.1));
u3 = u0(3);
cvals = zeros(100,100);
for m = 1:100
for l = 1:100
cvals(l,m) = Cost( [u1(m);u2(l);u3], x0, d );
end
end
s = surf(u1,u2,cvals);
set(s,'edgecolor','none');
a = gca;
set(a,'Tag','equilibriumcontrol');
hold on;
xlabel('Thrust')
ylabel('Angle of Attack')
```

最后，请注意，要查看 MATLAB 用于 `fminsearch` 的默认选项集，请使用优化函数的名称调用 `optimset`。

```matlab
>> options = optimset('fminsearch')
options =
Display: 'notify'
MaxFunEvals: '200*numberofvariables'
MaxIter: '200*numberofvariables'
TolFun: 0.0001
TolX: 0.0001
FunValCheck: 'off'
OutputFcn: []
PlotFcns: []
```

你可以看到，默认容差相等，均为 0.0001，而函数评估次数和迭代次数取决于输入状态 `x` 中的变量数量。