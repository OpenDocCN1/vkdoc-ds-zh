# 7. 常微分方程求解方法的比较分析

在前几章中，我们讨论了求解一阶、二阶、高阶及耦合常微分方程初值问题的几种不同方法及 MATLAB/Simulink 内置函数，这些方程有显式定义和隐式定义两种形式，并给出了相应示例。综合所有讨论过的方法，我们可以概括出六种利用 MATLAB/Simulink 工具、函数、工具箱以及脚本编写来求解初值问题的不同途径。

*   使用符号数学工具箱中的 `dsolve()` 进行解析解搜索
*   使用符号数学工具箱中名为 `laplace` 和 `ilaplace` 的拉普拉斯变换进行解析解搜索
*   使用 MuPAD 笔记（符号数学工具箱）结合 `ODE` 和 `ODE::SOLVE` 进行解析解搜索
*   使用内置 ODEx 求解器进行数值解搜索，例如：`ode15s`、`ode23`、`ode45`、`ode113` 等
*   使用 Simulink 模型进行数值解搜索，该模型包含几种不同的固定步长与变步长求解器，例如：`ode2`、`ode4`、`ode8`、`ode15s`、`ode45` 等
*   通过基于欧拉法、休恩法、米尔恩法、亚当斯-巴什福思法、亚当斯-莫尔顿法、龙格-库塔法、泰勒级数展开法及其他方法编写脚本进行数值解搜索

接下来，我们通过求解下列二阶显式定义的非齐次常微分方程来展示所有六种方法，然后比较结果的精度与效率。



### 示例 1

以下是示例问题：![$$ \ddot{y}+\dot{y}+3y=\sin (5t) $$](img/490522_1_En_7_Chapter_TeX_IEq1.png), ![$$ \dot{y}(0)=-2;y(0)=3\. $$](img/490522_1_En_7_Chapter_TeX_IEq2.png)

我们首先采用两种方法：使用`dsolve()`进行解析求解，以及使用符号数学工具箱中的`laplace`和`ilaplace`工具。该练习的解决方案可在 `COMPARE_ODE_Solvers.m`、`COMPARE_ODE_Solvers_MUPAD.mn` 和 `COMPARE_ODE_Solvers_sim.slx` 文件中找到。脚本 `COMPARE_ODE_Solvers.m` 使用以下方法进行计算：`dsolve()`、`laplace/ilaplace`、`ode45/ode113` 以及欧拉/亚当斯-莫尔顿方法。该脚本从 Simulink 模型（`COMPARE_ODE_Solvers_sim.slx`，如图 7-5 所示）中调用使用 `ode8` 的数值解。根据解析公式计算得到的数值解显示在图 7-1 和 7-2 中。

![../images/490522_1_En_7_Chapter/490522_1_En_7_Fig1_HTML.png](img/490522_1_En_7_Fig1_HTML.png)

**图 7-1** 使用 `dsolve()` 以及 `laplace` 和 `ilaplace` 计算的解析解

```
% COMPARE_ODE_Solvers.m
close all; clearvars
% Part 1
%% 1st Solution Approach: DSOLVE
syms t s Y y(t)
Dy=diff(y, t);
D2y=diff(y, t, 2);
Equation = D2y==sin(5*t)-Dy-3*y;
ICs = [y(0)==3, Dy(0)==-2];
Solution_dsolve=dsolve('D2y+Dy+3*y-sin(5*t)=0', 'y(0)=3','Dy(0)+2=0');
disp('Symbolic MATH dsolve function output is: ')
pretty(Solution_dsolve)
% display plot of the solution of dsolve and compare it with LT
figure;
subplot(211)
fplot(Solution_dsolve, [0, 2*pi], 'b-', 'linewidth', 1.5)
title('DSOLVE solution of: $$ \frac{d²y}{dt²}+\frac{dy}{dt}+3*y=sin(5*t) $$', 'interpreter', 'latex')
grid on
ylabel('\it y(t)'), xlabel('\it t')
%% 2nd Solution Approach: Laplace Transforms laplace/ilaplace
% Define symbolic variables' names
syms t s Y y(t)
assume([Y, t]>0)
Dy=diff(y, t);
D2y=diff(y, t, 2);
Equation = D2y==sin(5*t)-Dy-3*y;
% Laplace Transforms
LT_Y=laplace(Equation, t, s);
% Substitute the arbitrary unknown Yand Initial Conditions
LT_Y=subs(LT_Y,laplace(y(t),t, s),Y);
LT_Y=subs(LT_Y,y(0),3);
LT_Y=subs(LT_Y,subs(diff(y(t), t), t),-2);
% Solve for Y unknown
disp('Laplace Transforms of the given 2nd Order ODE with ICs')
Y=solve(LT_Y, Y)  %#ok
Solution_Laplace=ilaplace(Y);
disp('Solution found using Laplace Transforms is:')
Solution_Laplace=simplify(Solution_Laplace);
pretty(Solution_Laplace)
subplot(212)
fplot(Solution_Laplace, [0, 2*pi], 'r-', 'linewidth', 1.5); grid on
title('LAPLACE/iLAPLACE solution of: $$ \frac{d²y}{dt²}+\frac{dy}{dt}+3*y=sin(5*t) $$', 'interpreter', 'latex')
ylabel('\it y(t)'), xlabel('\it t')
```

```
Symbolic MATH dsolve function output is:
/   1/2   \
| 11    t |
1532 cos| ------- |
\    2    /   22 sin(5 t)   5 cos(5 t)
------------------- - ----------- - ---------- -
/ t \           509          509
509 exp| - |
\ 2 /
/   1/2   \
1/2    | 11    t |
284 11    sin| ------- |
\    2    /

/ t \
5599 exp| - |
\ 2 /
Laplace Transforms of the given 2nd Order ODE with ICs
(3*s + 5/(s² + 25) + 1)/(s² + s + 3)
Solution found using Laplace Transforms is:
(1532*exp(-t/2)*(cos((11^(1/2)*t)/2) - (71*11^(1/2)*sin((11^(1/2)*t)/2))/4213))/509 - (22*sin(5*t))/509 - (5*cos(5*t))/509
/                              /   1/2   \ \
|                       1/2    | 11    t | |
|    /   1/2   \   71 11    sin| ------- | |
|    | 11    t |               \    2    / |
1532 | cos| ------- | - ----------------------- |
\    \    2    /            4213           /   22 sin(5 t)
------------------------------------------------- - ----------- -
/ t \                          509
509 exp| - |
\ 2 /
5 cos(5 t)

```

因此，问题的解析解如下：

![$$ y(t)=\frac{1532\cos \left(\frac{\sqrt{11}t}{2}\right)}{509{e}^{\frac{t}{2}}}-\frac{22\mathit{\sin}(5t)}{509}-\frac{5\cos (5t)}{509}-\frac{284\sqrt{11}\sin \left(\frac{\sqrt{11}t}{2}\right)}{5599{e}^{\frac{t}{2}}} $$](img/490522_1_En_7_Chapter_TeX_Equa.png)

给定 ODE 的解析解也可以在 MuPAD 笔记（`COMPARE_ODE_Solvers_MUPAD.mn`）中使用以下 MuPAD 命令求解：

```
Third Approach with MuPAD:
ODE_eqn:=ode({y”(t)+y'(t)+3*y(t)=sin(5*t),y(0)=3,y'(0)=-2}, y(t))
```

![../images/490522_1_En_7_Chapter/490522_1_En_7_Figa_HTML.jpg```Solution:=ode::solve(ODE_eqn)```![../images/490522_1_En_7_Chapter/490522_1_En_7_Figb_HTML.jpg```simplify(Solution)```![../images/490522_1_En_7_Chapter/490522_1_En_7_Figc_HTML.jpg![../images/490522_1_En_7_Chapter/490522_1_En_7_Fig2_HTML.jpg](img/490522_1_En_7_Fig2_HTML.jpg)

**图 7-2** 在 MuPAD 笔记中计算的练习解

```
[plot(Solution, #G, #L, t=0..2*PI)
```

请注意，通过三种方法（`dsolve()`、`laplace/ilaplace` 和符号数学工具箱的 MuPAD 笔记）找到的给定二阶初值问题的解析解表达式在公式上有所不同；但是，通过一些代数变换和简化，可以从所有三种方法的解中获得相同的格式。

第四种方法是使用内置的 ODEx 求解器（`ode15s`、`ode23`、`ode23tb`、`ode45` 和 `ode113`）计算数值解。让我们看看使用 `ode45` 和 `ode113` 求解器计算给定问题的数值解，如下所示：

```
%% Part 3. 4th Solution Approach: ODE45, ODE113 solvers
Ys=@(t, y)([y(2);-(y(2)+3*y(1))+sin(5*t)]);
ICs=[3, -2];
timeSPAN=0:pi/100:2*pi;
options_ODE=odeset('RelTol', 1e-6, 'AbsTol', 1e-8);
[t1, y_ODE45]=ode45(Ys, timeSPAN, ICs, options_ODE);
[t2, y_ODE113]=ode113(Ys, timeSPAN, ICs, options_ODE);
figure('name', 'ODEx solvers 1')
subplot(211)
plot(t1, y_ODE45(:,1), 'bo', t2, y_ODE113(:,1), 'k-')
ylabel('\it y(t)'), xlabel('\it t')
title('ODE45 and ODE113 solutions of: $$ \frac{d²y}{dt²}+\frac{dy}{dt}+3*y=sin(5*t) $$', 'interpreter', 'latex')
grid on
legend('\it ode45', 'ode113')
axis tight
subplot(212)
plot(t1, y_ODE45(:,2), 'bo', t2, y_ODE113(:,2), 'k-')
ylabel('\it $$ \frac{dy}{dt} $$', 'interpreter', 'latex'), xlabel('\it t')
title('ODE45 and ODE113 solutions of: $$ \frac{d²y}{dt²}+\frac{dy}{dt}+3*y=sin(5*t) $$', 'interpreter', 'latex')
grid on
legend('\it dy with ode45','\it dy with ode113', 'location', 'best')
axis tight
figure('name', 'ODEx solvers 2')
plot(y_ODE45(:,1), y_ODE45(:,2),'bo', ...
y_ODE113(:,1), y_ODE113(:,2), 'r-')
title('Phase plot of:  $$ \frac{d²y}{dt²}+\frac{dy}{dt}+3*y=sin(5*t) $$', 'interpreter', 'latex')
legend('\it ode45', 'ode113'), grid on
axis tight; shg
xlabel('\it y(t)')
ylabel('\it $$ \frac{dy}{dt} $$', 'interpreter', 'latex')
```

图 7-3 和图 7-4 显示了使用 `ode45` 和 `ode113` 求解器获得的数值解绘图。

![../images/490522_1_En_7_Chapter/490522_1_En_7_Fig4_HTML.png](img/490522_1_En_7_Fig4_HTML.png)

**图 7-4** 使用 `ode45` 和 `ode113` 计算的数值解（相图）

![../images/490522_1_En_7_Chapter/490522_1_En_7_Fig3_HTML.png](img/490522_1_En_7_Fig3_HTML.png)

**图 7-3** 使用 `ode45` 和 `ode113` 计算的数值解

下一种方法是 Simulink 建模。给定练习的模型如图 7-5 所示。请注意，两个初始条件是通过 `Integrator` 和 `Integrator1` 模块输入的。此外，在模型设置中设置了固定步长求解器（`ode8`，Dormand-Prince 算法）和固定步长 `pi/100`。

![../images/490522_1_En_7_Chapter/490522_1_En_7_Fig5_HTML.jpg](img/490522_1_En_7_Fig5_HTML.jpg)

**图 7-5** 给定问题的 Simulink 模型 `COMPARE_ODE_Solvers_sim.slx`

```
%% Part 4. 5th Solution Approach: Simulink modeling
%{
NB: This model uses ODE solver ODE8 in simulation.
A solver type can be altered if needed for a better/faster solution search.
This cell first recalls and then executes the Simulink model
COMPARE_ODE_Solvers_sim.slx that is to be put in a current directory
of MATLAB from the archived folder.
A fixed step is set to pi/100.
%}
%set_param('COMPARE_ODE_Solvers_sim','Solver','ode8','StopTime','2*pi');
[tout, SIMout]=sim('COMPARE_ODE_Solvers_sim.slx');
```

最后一种方法是基于欧拉法和亚当斯-莫尔顿法编写脚本。

```
%% Part 5. 6th Solution Approach: scripts using Euler's Method
t0=0;                  % start time
tend=2*pi;             % end time
h=pi/100;              % time step
t=t0:h:tend;           % time space
steps=length(t);       % # of steps
y=[3, -2];             % ICs: u0 at t0
for k=2:steps
f=[y(k-1,2), sin(5*t(k-1))-3*y(k-1,1)-y(k-1,2)];
y(k,:)=y(k-1,:)+h*f;
end
%% Part 6. 6th solution approach: scripts - 5-step Adams-Moulton
% method
t0=0;                   % start time
tend=2*pi;              % end time
h=pi/100;               % time step
t=t0:h:tend;            % time space
steps=length(t);        % # of steps
Fn(1,:)=[3, -2];        % ICs: u0 at t0
% function is defined via anonymous function handle:
Fcn =@(t, y1, y2)([y2, sin(5*t)-y2-3*y1]);
for ii=1:steps-4
% 1st: Predicted value by EULER's forward method
Fn(ii+1,:)=Fn(ii,:)+h*Fcn(t(ii), Fn(ii, 1),Fn(ii,2));
% 2nd: Corrected value by trapezoidal rule
Fn(ii+1,:)=Fn(ii,:)+(h/2)*(Fcn(t(ii+1),Fn(ii+1,1),Fn(ii+1,2))+...
Fcn(t(ii),Fn(ii,1),Fn(ii,2)));
% 3rd: Predicted solution:
Fn(ii+2,:)=Fn(ii+1,:)+(3*h/2)*Fcn(t(ii+1),Fn(ii+1,1),Fn(ii+1,2))-...
(h/2)*Fcn(t(ii),Fn(ii,1),Fn(ii,2));
% 3rd: Corrected ADAMS-Moulton method:
Fn(ii+2,:)=Fn(ii+1,:)+h*((5/12)*Fcn(t(ii+2),Fn(ii+2,1),Fn(ii+2,2))+...
(2/3)*Fcn(t(ii+1),Fn(ii+1,1),Fn(ii+1,2))-...
(1/12)*Fcn(t(ii),Fn(ii,1),Fn(ii,2)));
% Predicted solution:
Fn(ii+3,:)=Fn(ii+2,:)+h*((23/12)*Fcn(t(ii+2),Fn(ii+2,1),Fn(ii+2,2))...
-(4/3)*Fcn(t(ii+1),Fn(ii+1,1),Fn(ii+1,2))+...
(5/12)*Fcn(t(ii),Fn(ii,1),Fn(ii,2)));
% Corrected solution by ADAMS-Moulton method 4-step from here:
Fn(ii+3,:)=Fn(ii+2,:)+h*((3/8)*Fcn(t(ii+3),Fn(ii+3,1),Fn(ii+3,2))+...
(19/24)*Fcn(t(ii+2),Fn(ii+2,1),Fn(ii+2,2))-...
(5/24)*Fcn(t(ii+1),Fn(ii+1,1),Fn(ii+1,2))+...
(1/24)*Fcn(t(ii),Fn(ii,1),Fn(ii,2)));
% Predicted solution:
Fn(ii+4,:)=Fn(ii+3,:)+h*((55/24)*Fcn(t(ii+3),Fn(ii+3,1),Fn(ii+3,2)) ...
-(59/24)*Fcn(t(ii+2),Fn(ii+2,1),Fn(ii+2,2))+...
(37/24)*Fcn(t(ii+1),Fn(ii+1,1),Fn(ii+1,2))-...
(3/8)*Fcn(t(ii),Fn(ii,1),Fn(ii,2)));
% Corrected solution by ADAMS-Moulton 5-step method from here:
Fn(ii+4,:)=Fn(ii+3,:)+ ...
h*((251/720)*Fcn(t(ii+4),Fn(ii+4,1),Fn(ii+4,2))+...
(646/720)*Fcn(t(ii+3),Fn(ii+3,1),Fn(ii+3,2))-...
(264/720)*Fcn(t(ii+2),Fn(ii+2,1),Fn(ii+2,2))+...
(106/720)*Fcn(t(ii+1),Fn(ii+1,1),Fn(ii+1,2))-...
(19/720)*Fcn(t(ii),Fn(ii,1),Fn(ii,2)));
end
%% Part 6. Numerical values from DSOLVE and LAPLACE/iLAPLACE
SDsolve  = eval(Solution_dsolve);
SLaplace = eval(Solution_Laplace);
figure('name', 'Compare ALL 1')
plot(t, SDsolve, 'bx-'); grid on, hold on,
xlabel('\it t'), ylabel('\it y(t)')
plot(t, SLaplace, 'ro-')
plot(t, y_ODE45(:,1), 'm^--')
plot(t, y_ODE113(:,1), 'k<-')
plot(tout, SIMout(:,1), 'gd-')
plot(t, y(:,1), 'linewidth', 2)
plot(t, Fn(:,1), 'b--', 'linewidth', 1.5), grid minor
title('Solutions of: $$ \frac{d²y}{dt²}+\frac{dy}{dt}+3*y=sin(5*t) $$', 'interpreter', 'latex')
legend('dsolve','Laplace Transforms','ODE45','ODE113',...
'Simulink', 'Euler','Adams-Moulton'),
xlim([0, 2*pi]), hold off
figure('name', 'Compare ALL 2')
plot(y_ODE45(:,1), y_ODE45(:,2), 'mo'); grid on, hold on,
xlabel('\it y(t)'), ylabel('\it $$ \frac{dy}{dt} $$', ... 'interpreter', 'latex')
plot(y_ODE113(:,1), y_ODE113(:,2), 'k<')
plot(SIMout(:,1), SIMout(:,2), 'gd')
plot(y(:,1), y(:,2), 'linewidth', 2)
plot(Fn(:,1), Fn(:,2),'b--', 'linewidth', 1.5), grid minor
title('Phase plot of: $$ \frac{d²y}{dt²}+\frac{dy}{dt}+3*y=sin(5*t) $$', ...
'interpreter', 'latex')
legend('ODE45','ODE113', 'Simulink', 'Euler','Adams-Moulton'),
axis tight, hold off
%% Part 7. Residuals are computed
Res_ode45=abs(SDsolve)-abs(y_ODE45(:,1)');
Res_ode113=abs(SDsolve)-abs(y_ODE113(:,1)');
Res_sim=abs(SDsolve)-abs(SIMout(:,1)');
Res_Euler=abs(SDsolve)-abs(y(:,1)');
Res_AM=abs(SDsolve)-abs(Fn(:,1)');
Res_LTSD=abs(SLaplace)-abs(SDsolve);
figure('name', 'Residuals')
subplot(311)
plot(t, Res_ode45, 'r-',t,Res_ode113, 'k-.', t, Res_sim, 'b-')
hold on
plot(t, Res_LTSD, 'g--', 'linewidth', 1.5)
title('\it Residuals'), ylabel('\it Residual values')
legend('ode45','ode113','Simulink', 'Laplace', 'location', 'best')
subplot(312)
plot(t, Res_Euler, 'k-'), hold on
plot(t, Res_LTSD, 'g--', 'linewidth', 1.5)
ylabel('\it Residual values')
legend('Euler', 'Laplace', 'location', 'best')
subplot(313)
plot(t, Res_AM, 'm-', 'linewidth', 1.5), hold on
plot(t, Res_LTSD, 'g--', 'linewidth', 1.5)
ylabel('\it Residual values')
xlabel('\it t')
legend('Adams-Moulton', 'Laplace', 'location', 'best')
xlim([0, 2*pi]), shg
```

通过执行脚本 `COMPARE_ODE_Solvers.m` 和 Simulink 模型（`COMPARE_ODE_Solvers_sim.mdl`，如图 7-5 所示），可以得到图 7-6、图 7-7 和图 7-8 所示的绘图。它们展示了数值解以及各数值方法相对于解析解值的残差。

![../images/490522_1_En_7_Chapter/490522_1_En_7_Fig8_HTML.png](img/490522_1_En_7_Fig8_HTML.png)

**图 7-8** 数值解相对于解析解的残差

![../images/490522_1_En_7_Chapter/490522_1_En_7_Fig7_HTML.png](img/490522_1_En_7_Fig7_HTML.png)

**图 7-7** 由 `ode45`、`ode113`、Simulink、欧拉法和亚当斯-莫尔顿法计算的解的相图 (![$$ y(t)\ versus\kern0.5em \frac{dy}{dt} $$](img/490522_1_En_7_Chapter_TeX_IEq3.png))

![../images/490522_1_En_7_Chapter/490522_1_En_7_Fig6_HTML.png](img/490522_1_En_7_Fig6_HTML.png)

**图 7-6** 使用 `dsolve()`、`laplace/ilaplace`、`ode45`/`ode113`、Simulink (`ode8`)、欧拉法和亚当斯-莫尔顿法找到的所有数值解

在这个初值问题求解方法的比较分析练习中，从六种方法获得的仿真结果收敛得相对较好（图 7-6 和 7-7）。同时，存在一定的误差范围，如图 7-8 的残差图所示。从残差可以看出，欧拉法产生的误差最大，但在较长的仿真时间内，误差趋于减小。最精确的数值解来自 Simulink 模型（`COMPARE_ODE_Solvers_sim.mdl`）。其他数值方法的结果与解析解收敛得非常好，以至于小于 2 × 10^(−3) 的误差可以通过采用更小的时间步长进一步减小；另一方面，减小步长会显著增加计算时间。

综合在具体线性/非线性、刚性/非刚性、齐次/非齐次 ODE 示例（第 1 章至第 7 章）中演示的解析和数值求解初值问题方法的所有要点，可以得出以下结论性意见：

-   在为给定问题选择任何方法之前，有必要确定给定的 ODE 问题类型，即刚性/非刚性。
-   并非所有初值问题都能解析求解。如果可以解析求解，则应采用 `dsolve()`、`laplace` 和 `ilaplace` 等工具。如果给定的初值问题是非齐次的，并且受到不连续外力的作用，那么拉普拉斯变换（`laplace/ilaplace`）是最合适的方法。
-   如果数值求解方法是唯一的选择，则应根据给定的 ODE 类型仔细考虑解的搜索空间并选择合适的步长。如果给定问题的解搜索空间非常大，建议先通过基准测试数值方法在较小的搜索空间内工作，然后选择最合适的方法。
-   考虑给定任务的优先级：精度与速度的权衡。
-   在处理微分代数方程时，重写给定的隐式 ODE 并定义其一致的初始条件是首要步骤。此外，建议在 Simulink 中建模，然后比较两种方法（`ode15i` 与 Simulink）计算出的解。
-   建议使用几种不同的方法计算数值解。我们甚至看到，例如，MATLAB 的一些推荐求解器在刚性问题上可能会产生误导性的数值解。此外，建议相应地调整内置 ODEx 求解器（`ode15s`、`ode23`、`ode45`、`ode113` 等）的设置，并提供雅可比矩阵以提高所选求解器处理刚性 ODE 的效率。



## 演练习题

以下是一些可以尝试的练习。

### 练习 1

以下为二阶非齐次常微分方程的初值问题：

*   ![$$ \ddot{y}+9{y}²=\sin (2t) $$](img/490522_1_En_7_Chapter_TeX_IEq4.png)，`y(0) = 0` 且 ![$$ \dot{y}(0)=6 $$](img/490522_1_En_7_Chapter_TeX_IEq5.png)，`t ∈ [0, 3π]`
*   ![$$ \ddot{y}+4\dot{y}+104y=2\mathit{\cos}(10t) $$](img/490522_1_En_7_Chapter_TeX_IEq6.png)，`y(0) = 0` 且 ![$$ \dot{y}(0)=0 $$](img/490522_1_En_7_Chapter_TeX_IEq7.png)，`t ∈ [0, 5π]`
*   ![$$ \ddot{y}+y={\mathrm{e}}^{\mathrm{x}}+2, $$](img/490522_1_En_7_Chapter_TeX_IEq8.png) `y(0) = 0` 且 ![$$ \dot{y}(0)=0 $$](img/490522_1_En_7_Chapter_TeX_IEq9.png)，`x ∈ [0, 13]`
*   ![$$ \ddot{y}+2\dot{y}+y=2\mathrm{x} $$](img/490522_1_En_7_Chapter_TeX_IEq10.png)，![$$ \dot{y}(0)=0 $$](img/490522_1_En_7_Chapter_TeX_IEq11.png) 且 ![$$ \dot{y}(0)=6 $$](img/490522_1_En_7_Chapter_TeX_IEq12.png)，`x ∈ [0, 15]`
*   ![$$ \ddot{y}+2\dot{y}+101y=5\mathit{\sin}(10t) $$](img/490522_1_En_7_Chapter_TeX_IEq13.png)，`y(0) = 0` 且 ![$$ \dot{y}(0)=20 $$](img/490522_1_En_7_Chapter_TeX_IEq14.png)，`t ∈ [0, 5π]`

首先，使用以下方法求解上述每一个二阶常微分方程：
1.  编写基于前向欧拉法的脚本
2.  编写基于四阶亚当斯-莫尔顿方法的脚本
3.  编写基于四阶龙格-库塔方法的脚本
4.  使用 MATLAB 内置的 ODE 求解器 `ode23`、`ode45` 和 `ode113`，并相应地调整其设置，即相对和绝对误差容限
5.  构建一个 Simulink 模型并使用求解器 `ode3`

然后，比较从 (a) 到 (e) 找到的所有解，并找出哪一种方法最有效、最准确（正确且误差最小）。

最后，是否可以使用 `dsolve()` 和拉普拉斯变换（`laplace` 和 `ilaplace`）计算给定问题的解析解？如果可以，则将解析解与从 (a) 到 (e) 找到的数值解绘制在同一张图上。

### 练习 2

首先，求解此处给出的二阶非齐次常微分方程问题：![$$ {x}²\ddot{y}-5x\dot{y}+8y={e}^{3x} $$](img/490522_1_En_7_Chapter_TeX_IEq15.png)，`y(1) = 0`，且 ![$$ \dot{y}(1)=24 $$](img/490522_1_En_7_Chapter_TeX_IEq16.png)，`x ∈ [1, 15]`。注意初始点在 `x = 1`。使用以下方法：
1.  编写基于后向欧拉法的脚本
2.  编写基于五步亚当斯-巴什福斯方法的脚本
3.  编写基于米尔恩方法的脚本
4.  编写基于改进欧拉法的脚本
5.  使用 MATLAB 内置的 ODE 求解器：`ode23`、`ode45`、`ode113`
6.  构建一个 Simulink 模型并使用求解器 `ode2`
7.  比较从 (a) 到 (e) 找到的所有解，并确定哪种方法最合适、最高效

接下来，是否可以使用 `dsolve()` 和拉普拉斯变换（`laplace` 和 `ilaplace`）计算给定问题的解析解？如果可以，则将解析解与从 (a) 到 (e) 找到的数值解绘制在同一张图上。

最后，是否可以使用 MuPAD 计算给定问题的解析解？如果可以，则将其与使用 `dsolve()` 和拉普拉斯变换（`laplace` 和 `ilaplace`）找到的解析解进行比较。

### 练习 3

首先，求解此处给出的二阶非齐次非线性常微分方程：![$$ \ddot{y}+16\left|y\right|\dot{y}+12y=3{t}³+12\cos (3t) $$](img/490522_1_En_7_Chapter_TeX_IEq17.png)，`y(0) = −1` 且 ![$$ \dot{y}(0)=0 $$](img/490522_1_En_7_Chapter_TeX_IEq18.png)，`x ∈ [0, 13]`。使用以下方法：
1.  编写基于拉尔斯顿方法和米尔恩方法的脚本
2.  编写基于四阶亚当斯-莫尔顿方法的脚本
3.  编写基于龙格-库塔-吉尔方法的脚本
4.  使用 MATLAB 内置的 ODE 求解器：`ode23`、`ode45`、`ode113`
5.  构建一个 Simulink 模型并使用求解器 `ode4`

接下来，通过绘制 `t` 与 `y(t)` 以及 ![$$ t\ \mathrm{versus}\ \dot{y}(t), $$](img/490522_1_En_7_Chapter_TeX_IEq19.png) 的关系图，比较从 (a) 到 (e) 找到的所有解，并找出哪种方法最合适（正确且误差最小）且效率最高。

### 练习 4

首先，求解此处给出的二阶非齐次非线性常微分方程的初值问题：![$$ \ddot{y}+2{y}²{\dot{y}}²+101y={e}^{5t}+2{t}²+5\ \sin (10t) $$](img/490522_1_En_7_Chapter_TeX_IEq20.png)，`y(0) = 0` 且 ![$$ \dot{y}(0)=20 $$](img/490522_1_En_7_Chapter_TeX_IEq21.png)，`t ∈ [0, 6π]`。使用以下方法：
1.  编写基于休恩方法的脚本
2.  编写基于五阶亚当斯-莫尔顿方法的脚本
3.  编写基于龙格-库塔-吉尔方法的脚本
4.  使用 MATLAB 内置的 ODE 求解器：`ode23`、`ode45`、`ode113`
5.  构建一个 Simulink 模型并使用求解器 `ode14x`

接下来，比较从 (a) 到 (e) 找到的所有解，并找出哪种方法效率最高。如有必要，可采取更小的时间步长。

最后，是否可以使用 `dsolve()` 和拉普拉斯变换（`laplace` 和 `ilaplace`）计算给定问题的解析解？

### 练习 5

首先，求解此处给出的二阶非齐次非线性常微分方程：![$$ {\ddot{y}}²-5{y}²\mid \dot{y}\mid ={e}^{2t}+2t $$](img/490522_1_En_7_Chapter_TeX_IEq22.png)，`y(0) = 1` 且 ![$$ \dot{y}(0)=0 $$](img/490522_1_En_7_Chapter_TeX_IEq23.png)，`t ∈ [0, 13]`。使用以下方法：
1.  编写基于米尔恩方法的脚本
2.  编写基于五步亚当斯-巴什福斯方法的脚本
3.  编写基于龙格-库塔-费尔伯格方法的脚本
4.  使用 MATLAB 内置的 ODE 求解器：`ode23`、`ode45`、`ode113`
5.  构建一个 Simulink 模型并使用求解器 `ode1`

接下来，比较从 (a) 到 (e) 找到的所有解，并找出哪种方法效率最高。



### 练习 6

根据基尔霍夫定律，串联电阻-电感-电容 (RLC) 电路中的电荷方程如下：![$$ L\ddot{q}+R\dot{q}+\frac{q}{C}={\mathcal{E}}_{max}\cos \omega t $$](img/490522_1_En_7_Chapter_TeX_IEq24.png)。

![../images/490522_1_En_7_Chapter/490522_1_En_7_Figd_HTML.jpg](img/490522_1_En_7_Figd_HTML.jpg)

其中，对于 `t ∈` [0, 4π]，有

![$$ q(0)=\dot{q}(0)=0 $$](img/490522_1_En_7_Chapter_TeX_IEq25.png)

电动势：`ε[max]` = 110 [V]

电阻：`R` = 7.17 [Ω]

电容：`C` = 50 × 10^(−3) [F]

电枢电感：![$$ \mathcal{L}=9.53\ast {10}^{-4}\left[H\right] $$](images/490522_1_En_7_Chapter/490522_1_En_7_Chapter_TeX_IEq26.png)

频率：`ω` = 60 [Hz]

首先，请完成以下操作：

1.  通过编写基于改进欧拉法和拉尔斯顿法的脚本，求解 `q(t)` 的数值解。
2.  通过编写基于五阶亚当斯-莫尔顿法的脚本，求解 `q(t)` 的数值解。
3.  通过编写基于四阶龙格-库塔-吉尔法的脚本，求解 `q(t)` 的数值解。
4.  使用 MATLAB 内置的 ODE 求解器：`ode23`、`ode45`、`ode113`，求解 `q(t)` 的数值解。
5.  通过构建 Simulink 模型并使用求解器 `ode8`，求解 `q(t)` 的数值解。
6.  比较从 (a) 到 (e) 找到的所有解，找出哪种方法最高效且正确/合适。如有必要，采用合理更小的时间步长，并指定（合适的）初始步长、相对容差和绝对容差。

然后，是否可以使用 `dsolve()` 和拉普拉斯变换（`laplace` 和 `ilaplace`）计算该问题的解析解？如果可以，请将解析解与从 (a) 到 (e) 找到的数值解绘制在同一张图上。

最后，是否可以使用 MuPAD 笔记计算该问题的解析解？如果可以，将其与使用 `dsolve()` 和拉普拉斯变换（`laplace` 和 `ilaplace`）找到的解析解进行比较。

### 练习 7

首先，求解此处给出的四阶非齐次常微分方程的初值问题（IVP）：![$$ {y}^{iv}+3{\ddot{y}}³-\cos (100t)\dot{y}+8y={t}²+10\sin \left(100\mathrm{t}\right) $$](img/490522_1_En_7_Chapter_TeX_IEq27.png)，初始条件为 ![$$ y(0)=0,\dot{y}(0)=1,\kern0.5em \ddot{y}(0)=2,\mathrm{and}\ \overset{\dddot{}}{y}(0)=-3\. $$](img/490522_1_En_7_Chapter_TeX_IEq28.png)，对于 `t ∈` [0, 3π]。请使用以下方法：

1.  通过编写基于亨恩法的脚本求解该问题。
2.  通过编写基于五阶亚当斯-莫尔顿法的脚本求解该问题。
3.  通过编写基于龙格-库塔-吉尔法的脚本求解该问题。
4.  使用 MATLAB 内置的 ODE 求解器，特别是 `ode23`、`ode45` 和 `ode113` 求解该问题。同时设置相对和绝对误差容限。
5.  使用 MATLAB 内置的 ODE 求解器，特别是 `ode23s`、`ode15s` 和 `ode23tb` 求解该问题。仅通过绘图获取该问题的数值解。（提示：使用 `odeset` 为 `@odeplot` 设置 `OutputFcn`）。
6.  通过构建使用求解器 `ode2` 的 Simulink 模型来求解该问题。

最后，比较从 (a) 到 (f) 找到的所有解，找出哪种方法最高效且充分。

### 练习 8

求解此处给出的四阶非齐次常微分方程的初值问题（IVP）：![$$ {y}^{iv}+2\overset{\dddot{}}{y}+\ddot{y}+8\dot{y}-12y=12\ \mathit{\sin}\ (25t)-{e}^{-5t} $$](img/490522_1_En_7_Chapter_TeX_IEq29.png)，初始条件为 ![$$ y(0)=3,\dot{y}(0)=0,\ddot{y}(0)=-1\kern0.5em \mathrm{and}\kern0.5em \overset{\dddot{}}{y}(0)=2, $$](img/490522_1_En_7_Chapter_TeX_IEq30.png)，对于 `t ∈` [0, 5π]。请使用以下方法：

1.  通过编写基于欧拉前向法的脚本求解该问题。
2.  通过编写基于五阶亚当斯-巴什福思法的脚本求解该问题。
3.  通过编写基于龙格-库塔-费尔伯格法的脚本求解该问题。
4.  使用 MATLAB 内置的 ODE 求解器，特别是 `ode23s`、`ode15s` 和 `ode113` 求解该问题。同时设置相对和绝对容差。
5.  使用 MATLAB 内置的 ODE 求解器，特别是 `ode23`、`ode45` 和 `ode23tb` 求解该问题。仅通过绘图获取该问题的数值解。（提示：使用 `odeset` 为 `@odeplot` 设置 `OutputFcn`）。
6.  通过构建使用求解器 `ode8` 的 Simulink 模型来求解该问题。
7.  比较从 (a) 到 (f) 找到的所有解，并明确指出哪种方法最高效且合适。

### 练习 9

求解以下耦合常微分方程组定义的数值解：

1.  ![$$ \left\{\begin{array}{c}\frac{dx_1}{dt}=-{x}_2+\cos (t)\\ {}\frac{dx_2}{dt}={x}_1+\sin (t)\end{array}\right. $$](img/490522_1_En_7_Chapter_TeX_IEq31.png)，初始条件为：`x1 = 2.5`，`x2 = 3.5`，`t ∈` [1, 13]。
2.  ![$$ \left\{\begin{array}{c}\frac{dx}{dt}=-3x+5y+2{ye}^{\mathrm{x}}\\ {}\frac{dy}{dt}=-13x-{\mathrm{x}}²-{\mathrm{y}}²\end{array}\right. $$](img/490522_1_En_7_Chapter_TeX_IEq32.png)，初始条件为：`x(0.5) = 2`，`y(0.5) = −2`，`t ≤ 5.55`。
3.  ![$$ \left\{\begin{array}{c}\frac{dx}{dt}=\left(1+x\right)\mathit{\sin}(y)\\ {}\frac{dy}{dt}=1-x-\mathit{\cos}(y)\end{array}\right. $$](img/490522_1_En_7_Chapter_TeX_IEq33.png)，初始条件为：![$$ x\left(\frac{\pi }{4}\right)=1.25,\kern0.5em y\left(\frac{\pi }{4}\right)=0.75,\kern0.75em t\ \epsilon\ \left[\frac{\pi }{4},\frac{7\pi }{2}\right]. $$](images/490522_1_En_7_Chapter/490522_1_En_7_Chapter_TeX_IEq34.png)

对于每个方程组，请执行以下任务：

-   编写一个关于耦合方程组的匿名函数。
-   编写一个关于耦合方程组的内联函数。
-   创建一个函数文件，例如命名为 `CoupleODE.m`。
-   通过构建一个使用定步长求解器 `ode3` 的 Simulink 模型（例如命名为 `CoupledODEsim.mdl`）来求解该问题。
-   使用 `ode23`、`ode45` 和 `ode113` 求解该问题的数值解。比较来自 ODE 求解器与 Simulink 模型的解，并检查每种方法的效率。采用更小的时间步长，调整相对和绝对容差，并从 m 文件中仿真您创建的 Simulink 模型（`CoupledODEsim.mdl`）。（提示：使用 `sim()` 和 `simset()`）。
-   通过编写基于中点法则的脚本求解给定耦合系统的数值解，并将其数值解与从 `ode23`、`ode45`、`ode113` 以及 Simulink 模型获得的解进行比较。



### 练习 10

利用拉普拉斯变换（`laplace` 和 `ilaplace`），求解以下在非连续强制函数下的二阶非齐次常微分方程：

1. `¨y + 5y = h(t), y(0) = 0, ẏ(0) = 0`，其中 `h(t) = { 0, (t-3)/3, 13 }` 对应 `{ 0 ≤ t < 3, 3 ≤ t < 11, t ≥ 11 }`
2. `¨y + 5ẏ + 5y = g(t), y(0) = 0, ẏ(0) = 2`，其中 `g(t) = { 5, 0 }` 对应 `{ π ≤ t < 3π, 0 ≤ t < π 且 t ≥ 3π }`
3. `ẍ + 5ẋ + (5/6)x = u(t), x(0) = 0, ẋ(0) = 0`，其中 `u(t) = { sin(t), 0 }` 对应 `{ 0 ≤ t < 2π, t ≥ 2π }`

绘制足够长时间范围内解析解的数值结果。

### 练习 11

使用以下方法模拟罗伯逊问题：

1. 使用 `ode15s` 和 `ode113` 解算器，并编写一个基于 Adams-Moulton 方法的脚本来模拟罗伯逊问题模型，并比较数值解。哪种方法最适合该问题？
2. 通过编写基于改进欧拉法和 Runge-Kutta-Fehlberg 法的脚本来模拟 Akzo-Nobel 问题。比较这两种方法的仿真结果。
3. 通过构建一个使用变步长解算器 `ode23` 的 Simulink 模型，并使用解算器 `ode15s` 来模拟 HIRES 问题。如有必要，调整两个模型中的相对和绝对误差容限，并比较它们的数值解。哪种方法更合适，为什么？

### 练习 12

求解给定的一阶常微分方程隐式定义的初值问题：

1. `(y′)² + xy′ − y = 2x`，`y(2) = −1`，其中 `x ∈ [2, 15]`。注意初始点不在 0 而在 2。
2. `y(y′)³ + 5ty′ − 3y = 2t`，`y(1) = −2`，其中 `t ∈ [1, 15]`。注意初始点不在 0 而在 1。
   - 使用 MATLAB 解算器 `ode15i`。
   - 构建一个 Simulink 模型。
   - 比较 `ode15i` 和 Simulink 模型的结果。

### 练习 13

求解由以下方程定义的二阶常微分方程隐式定义的初值问题：

1. 创建问题的 Simulink 模型。
2. 使用 MATLAB 内置的 ODE 解算器 `ode15i` 和 `decic`。
3. 比较 (a) 和 (b) 求得的数值解，并找出哪种方法最有效。如有必要，采用更小的时间步长。
4. 是否可以使用 `dsolve()` 或拉普拉斯变换（`laplace` 和 `ilaplace`）来评估该问题的解析解？

- `3ÿ² - 2ÿ + e^(2t)ẏ - 12y = 0, y(0) = 2, ẏ(0) = 2e², t ∈ [0, 25]`
- `3ÿ³ - 2yÿ + ẏ - 13y = e^(3t), y(0) = 5, ẏ(0) = 3e⁴, t ∈ [0, 15]`

# 第二部分 – 常微分方程中的边值问题



# 8. 边值问题

在本章中，我们将讨论使用 MATLAB 内置求解器求解常微分方程 (ODE) 边值问题 (BVP) 的基本步骤。BVP 与初值问题 (IVP) 的唯一区别在于，BVP 中给定的微分方程在两个边界条件（即初始条件和结束条件）下有效。BVP 可以用以下表达式表示：

![$$ \frac{\partial u}{\partial t}\left(y,t\right)=\beta \frac{\partial²y}{\partial {y}²}\left(y,t\right),\kern1em a&lt;y&lt;b,\kern0.5em 0&lt;t $$](img/490522_1_En_8_Chapter_TeX_Equ1.png)

(8-1)

BVP 中有三种主要类型（外加另一种类型）的边界条件 (BC)，如下所列：

- 狄利克雷边界条件：

![$$ u\left(a,t\right)=0;\kern0.75em u\left(b,t\right)=0 $$](img/490522_1_En_8_Chapter_TeX_Equ2.png)

(8-2)

- 诺伊曼边界条件：

![$$ \frac{\partial u}{\partial y}\left(a,t\right)=0;\kern0.75em \frac{\partial u}{\partial y}\left(b,t\right)=0 $$](img/490522_1_En_8_Chapter_TeX_Equ3.png)

(8-3)

- 罗宾或混合边界条件：

![$$ u\left(a,t\right)=0;\kern0.5em \frac{\partial u}{\partial y}\left(b,t\right)=0 $$](img/490522_1_En_8_Chapter_TeX_Equ4.png)

(8-4)

或

![$$ \frac{\partial u}{\partial y}\left(a,t\right)=0;\kern0.75em u\left(b,t\right)=0 $$](img/490522_1_En_8_Chapter_TeX_Equ5.png)

(8-5)

- 周期边界条件：

![$$ u\left(a,t\right)=u\left(b,t\right);\kern0.5em \frac{\partial u}{\partial y}\left(a,t\right)=\frac{\partial u}{\partial y}\left(b,t\right); $$](img/490522_1_En_8_Chapter_TeX_Equ6.png)

(8-6)

大多数 BVP 是二阶问题；因此，我们着重求解或模拟定义如下的二阶 BVP 方程：

![$$ \ddot{y}+p(t)\dot{y}+q(t)y=r(t),\kern0.5em a&lt;t&lt;b $$](img/490522_1_En_8_Chapter_TeX_Equ7.png)

(8-7)

其中，令 *Ι* = (*a*, *b*) ⊆ ℝ 为一个区间，并令 *p*, *q*, *r* : (*a*, *b*) → ℝ 为连续函数。

BVP 中的常微分方程可能有一个解、几个解或无限多个解，具体取决于边界条件（边界值和边界类型）。例如，考虑如下所示的二阶微分方程：

![$$ \ddot{y}+y=0 $$](img/490522_1_En_8_Chapter_TeX_Equ8.png)

(8-8)

以下是该方程的三个案例：

- **案例 #1：** 方程 (8-8) 在边界条件![$$ y(0)=1\kern0.75em \mathrm{and}\kern0.5em y\left(\frac{\pi }{2}\right)=1 $$](img/490522_1_En_8_Chapter_TeX_IEq1.png)下，有唯一解 `y(t) = sin(t) + cos(t)`。

- **案例 #2：** 方程 (8-8) 在边界条件 `y(0) = 1` 和 `y(π) = 1`下无解。

- **案例 #3：** 方程 (8-8) 在边界条件 `y(0) = 1` 和 `y(2π) = 1`下有无穷多解。

类似地，如果需要，我们也可以利用其他边界条件生成更多其他案例。

**对于案例 #1。** 在![$$ y(0)=1\kern0.75em \mathrm{and}\kern0.5em y\left(\frac{\pi }{2}\right)=1 $$](img/490522_1_En_8_Chapter_TeX_IEq2.png)条件下，我们假设方程 (8-8) 的通解为 `y(t) = C[1] * sin(t) + C[2] * cos(t)`。可以通过以下表达式并将边界条件应用于问题的通解来求出 `C[1]` 和 `C[2]`：

![$$ 1=y(0)={C}_1\sin (0)+{C}_2\mathit{\cos}(0)={C}_1\ast 0+{C}_2\ast 1={C}_2\Rightarrow {C}_2=1 $$](img/490522_1_En_8_Chapter_TeX_Equa.png)

![$$ 1=y\left(\frac{\pi }{2}\right)={C}_1\sin \left(\frac{\pi }{2}\right)+{C}_2\mathit{\cos}\left(\frac{\pi }{2}\right)={C}_1\ast 1+{C}_2\ast 0={C}_1\Rightarrow {C}_1=1 $$](img/490522_1_En_8_Chapter_TeX_Equb.png)

现在，解如下所示：

![$$ y(t)=\mathit{\sin}(t)+\mathit{\cos}(t) $$](img/490522_1_En_8_Chapter_TeX_Equc.png)

**对于案例 #2。** 在 `y(0) = 1` 和 `y(π) = 1`条件下，我们假设方程 (8-8) 的通解为 `y(t) = C[1] * sin(t) + C[2] * cos(t)`。可以通过以下表达式并将边界条件应用于问题的通解来求出 `C[1]` 和 `C[2]`：

![$$ 1=y(0)={C}_1\sin (0)+{C}_2\mathit{\cos}(0)={C}_1\ast 0+{C}_2\ast 1={C}_2\Rightarrow {C}_2=1 $$](img/490522_1_En_8_Chapter_TeX_Equd.png)

![$$ 1=y\left(\pi \right)={C}_1\sin \left(\pi \right)+{C}_2\mathit{\cos}\left(\pi \right)={C}_1\ast 0+{C}_2\ast \left(-1\right)={C}_2\Rightarrow {C}_2=-1 $$](img/490522_1_En_8_Chapter_TeX_Eque.png)

对于 `a = 0`，`C[2] = 1`；而对于 `b = π`，`C[2] = -1`。这相互矛盾，意味着对于这些边界条件，没有解。

**对于案例 #3。** 在 `y(0) = 1` 和 `y(2π) = 1`条件下，我们假设方程 (8-8) 的通解为 `y(t) = C[1] * sin(t) + C[2] * cos(t)`。可以通过以下表达式并将边界条件应用于问题的通解来求出 `C[1]` 和 `C[2]`：

![$$ 1=y(0)={C}_1\sin (0)+{C}_2\mathit{\cos}(0)={C}_1\ast 0+{C}_2\ast 1={C}_2\Rightarrow {C}_2=1 $$](img/490522_1_En_8_Chapter_TeX_Equf.png)

![$$ 1=y\left(2\pi \right)={C}_1\sin \left(2\pi \right)+{C}_2\mathit{\cos}\left(2\pi \right)={C}_1\ast 0+{C}_2\ast (1)={C}_2\Rightarrow {C}_2=1 $$](img/490522_1_En_8_Chapter_TeX_Equg.png)

`C[1]` 可以是任意数值，这意味着对于给定的边界条件，该问题具有无限多个解，形式为 `y(t) = C[1] * sin(t) + cos(t)`，其中 `C[1] ⊆ ∀`。

让我们探索 MATLAB 工具和函数，以及 Symbolic Math Toolbox 的 MuPAD 笔记，来求解几个不同的 BVP。以下 MATLAB 内置函数用于求解 BVP：`bvpinit`、`bvp4c`、`bvp5c`、`bc`、`odeset` 和 `deval`。在 Symbolic Math Toolbox 中，使用命令 `ode::solve` 来计算给定问题的解析解。

现在，让我们考虑几个不同的 BVP 二阶常微分方程，以找到数值解，并使用 MATLAB 和 Symbolic Math Toolbox 对其进行仿真。

在 MATLAB 中计算 BVP 的数值解时，有一些通用的“必须遵循”步骤。

- **步骤 #0.** 通过一阶微分方程组重写给定的高阶微分方程。此步骤与 IVP 中的步骤类似。

- **步骤 #1.** 通过内联函数或函数句柄（`@`）或函数文件（m 文件）定义给定的问题微分方程。

- **步骤 #2.** 通过匿名函数（函数句柄，`@`）或单独的函数文件（m 文件）或嵌套函数定义边界条件的残差。

- **步骤 #3.** 定义模拟范围/空间（例如，时间空间）。

- **步骤 #4.** 使用 `bvpinit()` 对解的结构进行猜测。请注意，初始猜测可以是常数值，也可以是针对特定（常数）值计算的函数。

- **步骤 #5.** 使用 `bvp4c()` 或 `bvp5c()` 获取解。

- **步骤 #6.** 使用 `deval()` 计算数值解。

让我们考虑几个不同的问题，以展示如何根据先前用于求解 BVP 的步骤过程来使用 MATLAB 工具。

## 狄利克雷边界条件问题



### 示例 1

示例如下：

![$$ \ddot{y}+2y={\mathrm{e}}^{\mathrm{t}} $$](img/490522_1_En_8_Chapter_TeX_IEq3.png)，其中 `y = [y[0] y[end]]`

这里 `y[0] = y(0) = 0` 和 `y[end] = y(π) = 1` 是狄利克雷边界条件。

给定问题 `f(t, y)` 的解仅在 `t ∈ [0, π]` 范围内及边界之外有效。`f(t, y)` 的有效性并不重要。

我们首先需要将其重写为两个一阶常微分方程：![$$ \left\{\begin{array}{c}{y}_2={\dot{y}}_1\\ {}{\dot{y}}_2={e}^t-2{y}_1\end{array}\right. $$](img/490522_1_En_8_Chapter_TeX_IEq4.png).

这类似于求解二阶常微分方程的初值问题方法。现在我们编写一个 m 文件来计算其数值解。以下是计算给定微分方程数值解的脚本（`BVP_EX1.m`）。练习的计算解如图 8-1 所示。

```
clc; clearvars
% BVP_EX1.m
%{
示例 1. 已知：y"+2y=exp(t) 边界条件为：y(0)=0, y(pi)=1。
帮助：首先，我们需要将给定的问题方程
重写为一组一阶常微分方程，类似于二阶初值问题。
因此，我们将其重写为：dy/dt=[y2, exp(t)-2*y1]；
dy/dt 可以通过函数文件或匿名函数定义。
%}
% 通过匿名函数定义：
dy=@(t, y)([y(2); exp(t)-2*y(1)]);
% 通过匿名函数定义边界条件的残差（狄利克雷边界条件）：
Res=@(yl, yr)([yl(1), yr(1)-1]);
% 创建模拟的时间空间：
t = linspace(0, pi, 40);
% 创建一个猜测结构，包含一个初始网格
% 在 [0,pi] 内均匀分布的 40 个点，以及
% 常量值 y1=0 和 y2=1 的猜测，使用以下命令：
SOLin1 = bvpinit(t,[0 1]);
% 另一个初始猜测（为了好玩）：y1=2 和 y2=1
SOLin2 = bvpinit(t,[2 1]);
% 获取问题的数值解：
SOL1 = bvp4c(dy, Res, SOLin1);
SOL2 = bvp4c(dy, Res, SOLin2);
y1 = deval(SOL1,t);
y2 = deval(SOL2,t);
figure
plot(t, y1(1,1:end), 'ro-', t, y2(1, :),'bx-', 'linewidth', 1.5),
grid minor;
legend('\it 猜测 #1: y_1=1  & y_2=0 ',...
'\it 猜测 #2: y_1=2  & y_2=1', 'location', 'SouthWest')
xlabel( '\it t, 时间')
ylabel( '\it y(t) 解值')
axis tight
syms y t d dt e
EQN = d²*y/dt²+2*y==e^t;
title('\it 边值问题解: $\frac{d²y}{dt²}+2y=e^t$' , 'Interpreter',  'latex')
```

![../images/490522_1_En_8_Chapter/490522_1_En_8_Fig1_HTML.png](img/490522_1_En_8_Fig1_HTML.png)

**图 8-1** 二阶边值问题的模拟结果：![$$ \ddot{y}+2y={e}^t $$](img/490522_1_En_8_Chapter_TeX_IEq5.png)

解决此练习的另一种方法是使用符号数学工具箱，该工具箱可以用两行命令（在 MuPAD 笔记中）计算问题的解析解，语法如下：

![../images/490522_1_En_8_Chapter/490522_1_En_8_Figa_HTML.jpg](img/490522_1_En_8_Figa_HTML.jpg)

![../images/490522_1_En_8_Chapter/490522_1_En_8_Figp_HTML.jpg](img/490522_1_En_8_Figp_HTML.jpg)

```
[ Sol1:=ode::solve({u"(t)=exp(t)-2*u(t),
u(0) = 0, u(PI) = 1}, u(t));
```

```
[ plot(Sol1, t=0..PI, #G, #L)
```

使用两个彼此值较为接近的不同数值初始猜测对此边值问题进行数值模拟，结果表明，对于此类边值问题，数值初始猜测对给定问题的最终数值解没有任何影响。此练习也可用 `[10⁷, −10⁷]` 或 `[−10⁷, 10⁷]` 的第二个初始猜测进行测试，并且之前图中显示的结果将以相同方式重复。然而，这些数值初始猜测不能取无穷大（`∞`）的值。如果初始猜测值设置为 `inf`，则求解器 `bvp4c` 将无法解决问题，因为问题的雅可比矩阵存在奇异性问题。

使用符号数学工具箱（MuPAD 笔记）评估解析解具有简单的语法。然而，并非所有边值问题都能用符号数学工具箱的命令和函数求解。这与初值问题的情况类似。

## 示例 2

这是另一个狄利克雷边界条件问题：![$$ {t}²\ddot{y}-5t\dot{y}+8y=0,y(1)=0,y(2)=24. $$](img/490522_1_En_8_Chapter_TeX_IEq6.png) 我们将给定的问题重写为两个一阶常微分方程：![$$ \left\{\begin{array}{c}{y}_2={\dot{y}}_1\\ {}{\dot{y}}_2=\frac{5t{y}_2-8{y}_1}{t²}\end{array}\right. $$](img/490522_1_En_8_Chapter_TeX_IEq7.png).

现在，我们通过脚本函数文件中的一个名为 `dy` 的嵌套函数来实现上述方程组，该文件命名为 `BVP_EX2.m`。请注意，此问题通过使用函数文件实现，并且定义边界条件和初始猜测结构的几个嵌套函数已嵌入该函数文件中。模拟结果如图 8-2 所示。

```
function BVP_EX2
% BVP_EX2.m 求解给定的边值问题：t²*y"-5t*y'+8y=0, y(1)=0, y(2)=24
t = linspace(1, 2, 30);
% 创建一个猜测结构，包含在边界条件范围内的
% 时间网格和一个猜测函数 (Gsol1)：
SOLin1 = bvpinit(t,@Gsol1);
% 另一个初始猜测（使用函数）Gsol2
SOLin2 = bvpinit(t,@Gsol2);
% 另一个初始猜测（使用数值）y1=0 和 y2=24
SOLin3 = bvpinit(t,[0,24]);
% 获取问题的数值解：
SOL1 = bvp4c(@dy,@Res,SOLin1);
SOL2 = bvp4c(@dy,@Res,SOLin2);
SOL3 = bvp4c(@dy,@Res,SOLin3);
y1 = deval(SOL1,t);
y2 = deval(SOL2,t);
y3 = deval(SOL3,t);
figure
plot(t, y1(1,1:end), 'ro-', t, y2(1, :), 'bx-',...
t, y3(1,:),'kd:','linewidth', 1.5), grid on
legend('\it 猜测 #1: 多项式', '\it 猜测 #2: cos&sin 函数 ',...
'\it 猜测 #3: 数值', 'location', 'SouthEast')
title('\it 边值问题解:  $\frac{d²y}{dt²}*t²-5t*\frac{dy}{dt}+8y=0$', 'Interpreter', 'latex')
xlabel '\it t',
ylabel '\it y(t) 解值'
function Ft1=Gsol1(t,~)
% 第一个猜测解函数：
Ft1=([8*t.²-8*t, 16*t-8]);
end
function Ft2=Gsol2(t,~)
% 第二个猜测解函数：
Ft2=([cos(t), -sin(t)]);
end
function f=dy(t,y)
% 给定问题的公式表示：
f=[y(2), (5*t.*y(2)-8*y(1))./t.²];
end
function F=Res(yl,yr)
% 现在，我们将边界条件的残差定义为一个嵌套函数。
F=[yl(1), yr(1)-24];
end
end
```

![../images/490522_1_En_8_Chapter/490522_1_En_8_Fig2_HTML.png](img/490522_1_En_8_Fig2_HTML.png)

**图 8-2** 问题的模拟：![$$ {t}²\ddot{y}-5t\dot{y}+8y=0 $$](img/490522_1_En_8_Chapter_TeX_IEq8.png)

MuPAD 笔记中符号数学工具箱的求解命令如下：

![../images/490522_1_En_8_Chapter/490522_1_En_8_Figb_HTML.jpg](img/490522_1_En_8_Figb_HTML.jpg)

```
 Sol1:=ode::solve({u"(t) =(5*t*u'(t)-8*u(t))/t²,
u(1) = 0, u(2) = 24}, u(t));
```

```
[ plot(Sol1, t=1..2, #G, #L, #3D)
```

![../images/490522_1_En_8_Chapter/490522_1_En_8_Figq_HTML.jpg

对于此具有狄利克雷边界条件的二阶非线性边值问题（示例 2）的数值解，我们进行了三种不同的猜测（`BVP_EX2.m`），具体而言，多项式（二次和线性方程）、正弦和余弦函数以及数值猜测，所有这些都得到了良好收敛的解。此外，在本示例中，我们还探索了使用嵌套函数和函数文件来加速模拟过程的方法。此外，我们使用符号数学工具箱以更简单的方式用两行命令计算了此练习的解析解，并绘制了数值。

# 罗宾边界条件问题

罗宾边界条件是混合边界条件。



### 示例 3

在本例中，我们考察两个具有相同微分方程但使用不同边界条件（罗宾边界条件和狄利克雷边界条件）的边值问题，以进行比较。文中比较了这两个边值问题的两种不同边界条件。

*   **问题 1:** `\ddot{y}+2y=0\ \mathrm{with}\ BCs:y(0)=1,\dot{y}\left(\pi \right)=0` （罗宾边界条件或混合边界条件）
*   **问题 2:** `\ddot{y}+2y=0\ \mathrm{with}\ BCs:y(0)=1,y\left(\pi \right)=0` （狄利克雷边界条件）

注意，这两个问题的唯一区别在于它们的边界条件。

首先，我们将给定的问题方程重写为一阶常微分方程组：`\left\{\begin{array}{c}{y}_2={\dot{y}}_1\\ {}{\dot{y}}_2=-2{y}_1\end{array}\right.`

现在，我们使用以下脚本（`BVP_EX3.m`）模拟该问题，该脚本包含两部分；第 1 部分求解问题 1（使用狄利克雷边界条件），第 2 部分求解问题 2（罗宾边界条件或混合边界条件）。除了边界条件残差的定义方式不同外，求解这两个问题的方法类似。

```
% BVP_EX3.m
% 问题 1\. 给定: y"+2y=0
% 第 1 部分. 边界条件: y(0)=1, dy(pi)=0\. 罗宾边界条件.
clearvars; clc; close all
dy=@(t, y)([y(2), -2*y(1)]);
% 定义边界条件的残差:
Res=@(yl, yr)([yl(1)-1, yr(2)]);
% 创建猜测结构:
SOLin1 = bvpinit(linspace(0,pi,40),[1 0]);
% 另一个初始猜测:
SOLin2 = bvpinit(linspace(0,pi,40),[2 1]);
% 获取问题的解:
SOL1 = bvp4c(dy,Res,SOLin1);
SOL2 = bvp4c(dy,Res,SOLin2);
t = linspace(0,pi, 40);
y1 = deval(SOL1,t);
y2 = deval(SOL2,t);
plot(t, y1(1,1:end), 'mp', t, y2(1, :),'k-', 'linewidth', 2.0),
grid on; axis tight; hold on
% 第 2 部分. 问题 2\. y"+2y=0, 边界条件: y(0)=1, y(pi)=0\. 狄利克雷边界条件
%{
将给定的问题方程重写为一阶常微分方程组:
dydt=[y2, 2*y1];
使用匿名函数定义 dydt:
%}
dy=@(t,y)([y(2), -2*y(1)]);
% 定义边界条件的残差:
Res=@(yl, yr)([yl(1)-1, yr(1)]);
%{
创建一个猜测结构，包含一个初始网格
（由 [0,pi]（边界条件范围内）中 40 个（可选）等间距点组成）
以及一个常数值猜测 y1=1 和 y2=0，如下所示:
%}
SOLin1 = bvpinit(linspace(0,pi,40),[1 0]);
% 取另一个初始猜测: y1=2 和 y2=1
SOLin2 = bvpinit(linspace(0,pi,40),[2 1]);
% 获取问题的解:
SOL1 = bvp4c(dy,Res,SOLin1);
SOL2 = bvp4c(dy,Res,SOLin2);
t  = linspace(0,pi, 40);
y1 = deval(SOL1,t);
y2 = deval(SOL2,t);
plot(t, y1(1,1:end), 'bo', t, y2(1, :), 'r', 'linewidth',2)
legend('\it 问题 1: 猜测 #1 [y1=1 & y2=0] ',...
'\it 问题 1: 猜测 #2 [y1=2 & y2=1]', ...
'\it 问题 2: 猜测 #1 [y1=1 & y2=0] ', ...
'\it 问题 2: 猜测 #2 [y1=2  & y2=1]', ...
'location', 'southwest')
xlabel '\it t', ylabel '\it y(t) 解值'
title('BVP 的解: $\frac{d²y}{dt²}+2y=0$', 'interpreter', 'latex')
gtext('问题 1: y(0)=1, y(\pi)=0')
gtext('问题 2: ; y(0)=1,dy(\pi)=0')
hold off
```

图 8-3 显示了该脚本的仿真结果。本练习清晰地展示了边界条件对边值问题最终解的影响程度。

图 8-3：采用两种不同边界条件（罗宾边界条件（问题 1）和狄利克雷边界条件（问题 2））对 `\ddot{y}+2y=0` 进行的仿真

另一种解法可以通过 MuPAD 注释中的符号数学工具箱计算得出。

```
[ SOL1:=ode::solve({y"(t) =-2*y(t),y(0) = 1, y'(PI) = 0}, y(t));
```

```
[ plot(SOL1, t=0..PI, #G, #L)
```

```
[ SOL2:=ode::solve({y"(t) =-2*y(t), y(0) = 1, y(PI) = 0}, y(t));
```

```
[  plot(SOL2, t=0..PI, #G, #L)
```

```
[ plot(SOL1,SOL2, t=0..PI, #G, #L)
```

本练习的仿真结果表明，两种不同的边界值猜测如何产生收敛良好的解。在此示例中，与之前的示例（示例 1）一样，可以在很大范围内测试边界值的数值猜测，例如 `[−10⁸, 10¹⁰]` 或反之。

## 斯图姆-刘维尔边值问题

在科学计算和工程研究中，有许多具有广泛应用的边值问题。其中一类边值问题是斯图姆-刘维尔边值问题，以两位法国数学家雅克·斯图姆和约瑟夫·刘维尔的名字命名。斯图姆-刘维尔边值问题 [1] 具有广泛的应用领域，例如：

*   非均匀杆中的热传导
*   量子力学中的一维薛定谔方程
*   非均匀杆和弦的振动
*   散射理论中的希尔方程
*   球体的振荡与勒让德方程
*   圆柱体和球体的热力学



### 示例 4

以下是具有常系数值的 Sturm-Liouville 边值问题的一个示例：

![$$ -\ddot{u}+{\omega}²u=f(t)\ \mathrm{with}\ BCsu(0)=u(1)=0 $$](img/490522_1_En_8_Chapter_TeX_IEq13.png) （周期边界条件）

让我们考虑以下情况：*ω* = 0、3、7、13 和 *f*(*t*) = *sinh*(*t*)。

现在，我们将 Sturm-Liouville 方程重写为我们所考虑的情况。

![$$ -\ddot{u}+{\omega}²u=\mathit{\sinh}(t)\Rightarrow \left\{\begin{array}{c}{u}_2={\dot{u}}_1\\ {}{\dot{u}}_2={\omega}²{u}_1-\mathit{\sinh}(t)\end{array}\right. $$](img/490522_1_En_8_Chapter_TeX_Equh.png)

在 MATLAB 的 BVP 求解器工具中，有一个选项可以在 `bvpinit` 中包含参数，我们可以利用该选项来包含 *ω* 的值用于仿真，并编写 `BVP_EX4.m`。仿真结果如图 8-4 所示。

```
clc; clearvars; close all
% BVP_EX4.m
% Sturm_Liouville_bvp. Given: -u"+w²*u=sinh(t)
% periodic boundary conditions: u(0)=u(1)=0
%  ----------------------------------------------------------
% Define residues of BCs via a function handle
Res=@(yl,yr)([yl(1) yr(1)]);
% 1st guess solution function defined with function handle:
Gsol=@(t)([sin(-0.540302305868140*t),cos(t)-0.540302305868140]);
close all; clc
omega=[0, 3, 5, 7, 13];
t = linspace(0, 1, 500);
% A guess structure consisting of time mesh within [0, 1]
% in the range of BCs and a guess function (Gsol):
SOLin1 = bvpinit(linspace(0,1, 10),Gsol);
% Another numeric value based initial guess y1=0 and y2=0
SOLin2 = bvpinit(linspace(0,1, 10),[0,0]);
% Plotting options:
Labelit = { };
Colorit1 = 'krgbm';
Colorit2 = 'krgbm';
Lineit1  = '---:--:---:';
Lineit2  = '---:--:---:';
for ii=1:numel(omega)
Styleit1=[Colorit1(ii), Lineit1(ii)];
Styleit2=[Colorit2(ii), Lineit2(ii)];
% Given problem formulation:
dy=@(t,y)([y(2), omega(ii)²*y(1)-sinh(t)]);
%Obtain the solutions of the problem for two initial guesses:
SOL1 = bvp4c(dy,Res,SOLin1);
SOL2 = bvp4c(dy,Res,SOLin2);
% Compute numeric solutions of the problem within time-space:
y1 = deval(SOL1,t);
y2 = deval(SOL2,t);
subplot(211)
plot(t, y1(1,1:end), Styleit1, 'linewidth', 1.5), grid on
hold on
Labelit{ii}=['\omega = ' num2str(omega(ii))];
legend(Labelit{:})
title('Sturm Liouville BVP: $\frac{-d²u}{dt²}+\omega²*u=sinh(t)$', 'interpreter', 'latex')
ylabel('\it y(t) solution values')
subplot(212)
plot(t,y2(1, :), Styleit2, 'linewidth', 1.5), grid on
hold on
Labelit{ii}=['\omega = ' num2str(omega(ii))];
legend(Labelit{:})
xlabel '\it t'
ylabel('\it y(t) solution values')
end
gtext('Guess 1: sin&cos fcns', 'background', 'y')
gtext('Guess 2: Numerical guess values', 'background', 'y')
hold off
```

![../images/490522_1_En_8_Chapter/490522_1_En_8_Fig4_HTML.png](img/490522_1_En_8_Fig4_HTML.png)

**图 8-4** *ω* = 0、3、5、7、13 时 Sturm-Liouville 边值问题的仿真。

Sturm-Liouville 边值问题的另一种解法在 MuPAD 笔记（`BVP_EX4_MuPAD.mn`）的符号数学工具箱脚本中获得。

![../images/490522_1_En_8_Chapter/490522_1_En_8_Figh_HTML.jpg](img/490522_1_En_8_Figh_HTML.jpg)

```
[ Omega:=([0, 3, 5, 7, 13]);
```

![../images/490522_1_En_8_Chapter/490522_1_En_8_Figi_HTML.jpg](img/490522_1_En_8_Figi_HTML.jpg)

```
[ Sol1:=ode::solve({u"(t)=Omega[1]²*u(t)-sinh(t),
u(0) = 0, u(1) = 0}, u(t));
```

![../images/490522_1_En_8_Chapter/490522_1_En_8_Figj_HTML.jpg](img/490522_1_En_8_Figj_HTML.jpg)

```
[ Sol2:=ode::solve({u"(t)=Omega[2]²*u(t)-sinh(t),
u(0) = 0, u(1) = 0}, u(t));
```

![../images/490522_1_En_8_Chapter/490522_1_En_8_Figk_HTML.jpg](img/490522_1_En_8_Figk_HTML.jpg)

```
[ Sol3:=ode::solve({u"(t)=Omega[3]²*u(t)-sinh(t),
u(0) = 0, u(1) = 0}, u(t));
```

![../images/490522_1_En_8_Chapter/490522_1_En_8_Figl_HTML.jpg](img/490522_1_En_8_Figl_HTML.jpg)

```
[ Sol4:=ode::solve({u"(t)=Omega[4]²*u(t)-sinh(t),
u(0) = 0, u(1) = 0}, u(t));
```

```
[ Sol5:=ode::solve({u"(t)=Omega[5]²*u(t)-sinh(t),
u(0) = 0, u(1) = 0}, u(t));
```

![../images/490522_1_En_8_Chapter/490522_1_En_8_Figm_HTML.jpg](img/490522_1_En_8_Figm_HTML.jpg)

```
 plot(Sol1, Sol2, Sol3, Sol4, Sol5, t=0..1, #G, #L)
```

![../images/490522_1_En_8_Chapter/490522_1_En_8_Fign_HTML.jpg

```
