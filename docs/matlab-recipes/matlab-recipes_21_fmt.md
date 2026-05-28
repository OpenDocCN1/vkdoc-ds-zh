# 工作原理

您将编写一个函数，该函数使用参数对，在单次函数调用中灵活地创建子图图形。`y` 输入可以有多个行，而 `x` 输入可以有一行，或者与 `y` 相同的行数。您提供默认标签，以便只需两个输入即可调用该函数。

此版本支持的参数仅有各种标签和绘图类型——标准 `plot`、`semilogx`、`semilogy` 和 `loglog`——但您可以轻松地添加线条粗细、绘图标记、阴影等功能。您使用一个 `for` 循环，在 `switch` 语句中检查 `varargin` 的每隔一个分量。请记住，`varargin` 提供了一个元胞数组参数。请注意使用了一个内置演示，展示了函数的两个主要分支，分别用于一个 `x` 序列和两个序列的情况。

绘图代码根据 `x` 和 `y` 的行数，在图形中为每个图创建子图。该函数假设子图排成单列，但您可以扩展逻辑以创建多列或任何适合您应用的子图排列。网格是开启的。

```
function PlotSet( x, y, varargin )

% 演示
%-----
if ( nargin < 1 )
    x = linspace(1,1000);
    y = [ sin(0.01*x); cos(0.01*x)];
    disp('PlotSet: 一个 x 和两个 y 行')
    PlotSet( x, y )
    disp('PlotSet: 两个 x 和两个 y 行')
    PlotSet( [x;y(1,:)], y )
    return ;
end

% 默认值
nCol = 1;
n = size(x,1);
m = size(y,1);
yLabel = cell (1,m);
xLabel = cell (1,n);
plotTitle = cell (1,n);
for k = 1:m
    yLabel{k} = 'y';
end
for k = 1:n
    xLabel{k} = 'x';
    plotTitle{k} = 'y vs. x';
end
figTitle = 'PlotSet';
plotType = 'plot';

% 处理输入参数
for k = 1:2:length(varargin)
    switch lower( varargin{k} )
        case 'x label'
            for j = 1:n
                xLabel{j} = varargin{k+1};
            end
        case 'y label'
            temp = varargin{k+1};
            if ( ischar(temp) )
                yLabel{1} = temp;
            else
                yLabel = temp;
            end
        case 'plot title'
            plotTitle{1} = varargin{k+1};
        case 'figure title'
            figTitle = varargin{k+1};
        case 'plot type'
            plotType = varargin{k+1};
        otherwise
            fprintf(1,'%s 不是允许的参数\n',varargin{k});
    end
end

h = figure ;
set( h,'Name',figTitle );

% 第一种路径：x 只有一行
if ( n == 1 )
    for k = 1:m
        subplot( m,nCol,k );
        plotXY(x,y(k,:),plotType);
        xlabel( xLabel{1} );
        ylabel( yLabel{k} );
        if ( k == 1 )
            title( plotTitle{1} )
        end
        grid on
    end
else
    for k = 1:n
        subplot( n,nCol,k );
        plotXY(x(k,:),y(k,:),plotType);
        xlabel( xLabel{k} );
        ylabel( yLabel{k} );
        title( plotTitle{k} )
        grid on
    end
end

%% 实现不同的绘图类型
function plotXY(x,y, type )
    switch type
        case 'plot'
            plot( x,y );
        case {'log' 'loglog' 'log log'}
            loglog( x,y );
        case {'xlog' 'semilogx' 'x log'}
            semilogx( x,y );
        case {'ylog' 'semilogy' 'y log'}
            semilogy( x,y );
        otherwise
            error( '%s 不是可用的绘图类型', type );
    end
```

该函数允许 `DoubleIntegratorSim.m` 用一行代码生成其图形。