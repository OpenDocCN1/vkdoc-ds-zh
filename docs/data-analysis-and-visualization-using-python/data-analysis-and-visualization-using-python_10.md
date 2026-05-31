# 7. 数据可视化

Python 提供了多种数据可视化方法。多种 Python 库可用于数据可视化，例如 Pandas、Seaborn、Bokeh、Pygal 和 Ploty。Python Pandas 是进行基本绘图的最简单方法。Python Seaborn 非常适合创建包含颜色的、视觉上吸引人的统计图表。Python Bokeh 适用于更复杂的可视化，尤其是基于 Web 的交互式演示。Python Pygal 在生成矢量文件和交互式文件方面表现出色，但其灵活性不如其他方法。Python Plotly 是创建高度交互式基于 Web 的可视化最有用且最简单的选择。

条形图是一种重要的可视化工具，用于比较不同类别中的值。通过调整 x 轴和 y 轴，条形图可以垂直或水平定向，具体取决于图表需要呈现的信息或类别类型。本章演示了各种可视化工具的使用和实现；本章将使用图 7-1 中所示的 `salaries.csv` 文件作为绘图数据集。

![../images/472943_1_En_7_Chapter/472943_1_En_7_Fig1_HTML.jpg](img/472943_1_En_7_Fig1_HTML.jpg)

图 7-1

薪资数据集

## 直接绘图

Pandas 是一个具有数据框特性的 Python 库，它提供了内置选项，用于以二维表格样式绘制可视化图表。在代码清单 7-1 中，你读取了薪资数据集并创建了一些变量向量，这些变量是 `rank`、`discipline`、`phd`、`service`、`sex` 和 `salary`。

```
In [3]: import pandas as pd
dataset = pd.read_csv("./Data/Salaries.csv")
rank = dataset['rank']
discipline = dataset['discipline']
phd = dataset['phd']
service = dataset['service']
sex = dataset['sex']
salary = dataset['salary']
dataset.head()
代码清单 7-1
读取数据集
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figa_HTML.jpg](img/472943_1_En_7_Figa_HTML.jpg)

### 折线图

你可以使用如代码清单 7-2 所示的折线图。确保数据单位（例如 `phd`、`service` 和 `salary` 变量）用于绘图非常重要。然而，只有薪资是可见的，而 `phd` 和 `service` 信息在图上并未清晰显示。这是因为薪资的数值单位是数十万，而 `phd` 和 `service` 信息的单位非常小。

```
In [5]: dataset[["rank", "discipline","phd","service", "sex", "salary"]].plot()
代码清单 7-2
可视化数值单位差异较大的模式
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figb_HTML.jpg](img/472943_1_En_7_Figb_HTML.jpg)

让我们可视化更具可比性的单位，例如 `phd` 和 `service` 信息，如代码清单 7-3 所示。你可以观察到多年来 `phd` 和 `service` 之间的相关性，除了从 55 岁到 80 岁，`service` 有所下降，这意味着一些人在 55 岁及以后离开了服务岗位。

```
In [6]: dataset[["phd","service"]].plot()
代码清单 7-3
可视化数值单位相近的模式
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figc_HTML.jpg](img/472943_1_En_7_Figc_HTML.jpg)

在代码清单 7-4 中，你按服务年限对数据进行分组，并汇总每个服务类别的薪资。然后，你根据薪资对派生数据集进行降序排序。最后，你使用条形图绘制排序后的数据集。

```
In [4]: dataset1 = dataset.groupby(['service']).sum()
dataset1.sort_values("salary", ascending = False, inplace=True)
dataset1.head()
代码清单 7-4
可视化每个服务类别的薪资
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figd_HTML.jpg](img/472943_1_En_7_Figd_HTML.jpg)

```
In [8]: dataset1["salary"].plot.bar()
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Fige_HTML.jpg](img/472943_1_En_7_Fige_HTML.jpg)

你可以看到大多数人服务年限约为 19 年，这就是为什么该类别累积薪资最高的原因。



### 条形图

清单 7-5 展示了如何绘制 `phd` 和 `services` 的前十条记录，你也可以添加标题。要为图表添加标题，你需要使用 `.bar(title="你的标题")`。

```
In [9]: dataset[[ 'phd', 'service' ]].head(10).plot.bar()
Listing 7-5
Bar Plotting
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figf_HTML.jpg](img/472943_1_En_7_Figf_HTML.jpg)

```
In [11]: dataset[['phd', 'service']].head(10).plot.bar(title="Ph.D. Vs Service\n 2018")
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figg_HTML.jpg](img/472943_1_En_7_Figg_HTML.jpg)

```
In [12]: dataset[['phd', 'service']].head(10).plot.bar(title="Ph.D. Vs Service\n 2018" , color=['g','red'])
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figh_HTML.jpg](img/472943_1_En_7_Figh_HTML.jpg)

### 饼图

饼图适用于比较整体的各个部分，它们不显示随时间的变化。条形图用于比较不同组别或追踪随时间的变化。然而，在衡量随时间的变化时，当变化幅度较大时，条形图效果最佳。此外，饼图适用于比较少量变量，但当变量数量较多时，其效果就不尽如人意了。清单 7-6 比较了 Salaries 数据集中十位专业人士的薪资待遇。

```
In [13]: dataset["salary"].head(10).plot.pie(autopct='%.2f')
Listing 7-6
Pie Chart
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figi_HTML.jpg](img/472943_1_En_7_Figi_HTML.jpg)

### 箱线图

箱线图用于通过一些统计值来比较变量。可比较的变量应具有相同的数据单位；清单 7-7 显示，当你比较 `phd` 和 `salary` 时，会产生不恰当的图形，并且无法提供真实的比较信息，因为 `salary` 的数值单位远高于 `phd` 的数值。绘制 `phd` 和 `services` 的箱线图显示，`phd` 的中位数和分位数高于 `service` 信息的中位数和分位数；此外，`phd` 的范围比 `service` 信息的范围更宽。

```
In [14]: dataset[["phd","salary"]].head(100).plot.box()
Listing 7-7
Box Plotting
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figj_HTML.jpg](img/472943_1_En_7_Figj_HTML.jpg)

```
In [15]: dataset[["phd","service"]].plot.box()
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figk_HTML.jpg](img/472943_1_En_7_Figk_HTML.jpg)

### 直方图

直方图可用于表示特定变量或变量集。清单 7-8 绘制了 `salaries` 变量的 20 条记录；它显示在该数据集中，薪资约为 135,000 的出现频率最高。

```
In [16]: dataset["salary"].head(20).plot.hist()
Listing 7-8
Histogram Plotting
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figl_HTML.jpg](img/472943_1_En_7_Figl_HTML.jpg)

### 散点图

散点图显示了实验中两个因素（例如 `phd` 和 `service`）之间的关系。趋势线用于确定正相关、负相关或无相关性。参见清单 7-9。

```
In [17]: dataset.plot(kind='scatter', x="phd", y="service", title='Popuation vs area and density\n 2018', s=0.9)
Listing 7-9
Scatter Plotting
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figm_HTML.jpg](img/472943_1_En_7_Figm_HTML.jpg)

## Seaborn 绘图系统

Python 的 Seaborn 库提供了多种用于数据可视化的绘图表示。带状图是一种散点图，其中一个变量是分类变量。带状图可以与其他图表结合以提供额外信息。例如，带有叠加带状图的箱线图类似于小提琴图，因为可以观察到关于底层数据分布方式的额外信息。Seaborn 的蜂群图与带状图几乎相同，只是它防止了数据点重叠。

### 带状图

清单 7-10 使用带状图按薪资类别显示数据。

```
In [3]: # Simple stripplot sns.stripplot( x = dataset['salary'])
Listing 7-10
Simple Strip Plot
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Fign_HTML.jpg](img/472943_1_En_7_Fign_HTML.jpg)

```
In [4]: # Stripplot over categories
sns.stripplot( x = dataset['sex'], y= dataset['salary'], data=dataset);
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figo_HTML.jpg](img/472943_1_En_7_Figo_HTML.jpg)

上一个示例按性别可视化了 `salary` 变量。

你可以使用清单 7-11 垂直或水平地可视化数据，该清单展示了两个学科 A 和 B。与学科 A 相比，学科 B 的范围更大，薪资待遇也更高。

```
In [5]: # Stripplot over categories
sns.stripplot( x = dataset['discipline'], y = dataset['salary'], data=dataset, jitter=1)
Listing 7-11
Strip Plot with Vertical and Horizontal Visualizing
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figp_HTML.jpg](img/472943_1_En_7_Figp_HTML.jpg)

```
In [6]: # Stripplot over categories Horizontal
sns.stripplot( x= dataset['salary'], y = dataset['discipline'], data=dataset, jitter=True);
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figq_HTML.jpg](img/472943_1_En_7_Figq_HTML.jpg)

你可以按类别在带状图中可视化数据；清单 7-12 使用了助理教授、副教授和正教授类别。`hue` 属性用于确定 `legend` 属性。

```
In [7]: # Stripplot over categories
sns.stripplot( x = dataset['rank'], y= dataset['salary'], data=dataset, jitter=True);
Listing 7-12
Strip Plot per Category
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figr_HTML.jpg](img/472943_1_En_7_Figr_HTML.jpg)

```
In [8]: # Add hue to the graph
#### Stripplot over categories
sns.stripplot( x ='sex', y= 'salary', hue="rank", data=dataset, jitter=True )
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figs_HTML.jpg](img/472943_1_En_7_Figs_HTML.jpg)



### 箱线图

你可以将箱线图和散点图结合起来，为生成的图表提供更多信息（参见代码清单 7-13）。如图所示，与女性类别相比，男性类别具有更高的薪资中位数、最高薪资和薪资范围。

```
In [10]: # Draw data on top of boxplot
sns.boxplot(x = 'salary', y ='sex', data=dataset, whis=np.inf )
sns.stripplot(x = 'salary', y ='sex', data=dataset, jitter=True, color='0.02')
Listing 7-13
Combined Box Plot and Strip Plot Visualization
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figt_HTML.jpg](img/472943_1_En_7_Figt_HTML.jpg)

```
In [13]: # box plot salaries
sns.boxplot(x = dataset['salary'])
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figu_HTML.jpg](img/472943_1_En_7_Figu_HTML.jpg)

```
In [14]: # box plot salaries
sns.boxplot(x = dataset['salary'], notch=True)
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figv_HTML.jpg](img/472943_1_En_7_Figv_HTML.jpg)

```
In [15]: # box plot salaries
sns.boxplot(x = dataset['salary'], whis=2)
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figw_HTML.jpg](img/472943_1_En_7_Figw_HTML.jpg)

```
In [16]: # box plot per rank
sns.boxplot(x = 'rank', y = 'salary', data=dataset)
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figx_HTML.jpg](img/472943_1_En_7_Figx_HTML.jpg)

```
In [17]: # box plot per rank
sns.boxplot(x = 'rank', y = 'salary', hue="sex", data=dataset, palette="Set3")
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figy_HTML.jpg](img/472943_1_En_7_Figy_HTML.jpg)

```
In [18]: # box plot per rank
sns.boxplot(x = 'rank', y = 'salary', data=dataset)
sns.swarmplot(x = 'rank', y = 'salary', data=dataset, color='0.25')
```

组合箱线图和散点图的可视化效果如下图所示。

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figz_HTML.jpg](img/472943_1_En_7_Figz_HTML.jpg)

### 散点图

散点图用于可视化不同类别；它能清晰地展示一个变量相对于其他变量的分布情况。例如，按性别和职业划分的薪资分布表明，男性教授的薪资范围最高。大多数男性是正教授，其次是副教授，然后是助理教授。男性教授的数量多于女性教授，但女性副教授的数量多于男性副教授。参见代码清单 7-14。

```
In [11]: # swarmplot
sns.swarmplot( x ='sex', y= 'salary', hue="rank", data=dataset, palette="Set2", dodge=True)
Listing 7-14
Swarm ploting of salary against gender
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figaa_HTML.jpg](img/472943_1_En_7_Figaa_HTML.jpg)

```
In [12]: # swarmplot
sns.swarmplot( x ='sex', y= 'salary', hue="rank", data=dataset, palette="Set2", dodge=False)
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figab_HTML.jpg](img/472943_1_En_7_Figab_HTML.jpg)

### 联合图

联合图将多个图表组合在一起，以可视化选定的模式（参见代码清单 7-15）。

```
In [22]: sns.jointplot(x = 'salary', y = 'service', data=dataset)
Listing 7-15
Joint Plot Visualization
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figac_HTML.jpg](img/472943_1_En_7_Figac_HTML.jpg)

```
In [24]: sns.jointplot('salary', 'service', data=dataset, kind="reg")
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figad_HTML.jpg](img/472943_1_En_7_Figad_HTML.jpg)

```
In [25]: sns.jointplot('salary', 'service', data=dataset, kind="hex")
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figae_HTML.jpg](img/472943_1_En_7_Figae_HTML.jpg)

```
In [26]: sns.jointplot('salary', 'service', data=dataset, kind="kde")
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figaf_HTML.jpg](img/472943_1_En_7_Figaf_HTML.jpg)

```
In [27]: from scipy.stats import spearmanr sns.jointplot('salary', 'service', data=dataset, stat_func= spearmanr )
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figag_HTML.jpg](img/472943_1_En_7_Figag_HTML.jpg)

```
In [31]: sns.jointplot('salary', 'service',
data=dataset).plot_joint(sns.kdeplot, n_levels=6)
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figah_HTML.jpg](img/472943_1_En_7_Figah_HTML.jpg)

```
In [32]: sns.jointplot('salary', 'service',
data=dataset).plot_joint( sns.kdeplot,n_levels=6).plot_marginals(sns.rugplot)
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figai_HTML.jpg](img/472943_1_En_7_Figai_HTML.jpg)

## Matplotlib 绘图

Matplotlib 是一个 Python 2D 绘图库，能够在各种硬拷贝格式和跨平台的交互式环境中生成高质量的图形。在 Matplotlib 中，你可以逐个添加功能，例如添加标题、标签、图例等。

### 折线图

在绘制内联图时，你应该先确定 x 轴和 y 轴，然后可以添加更多功能，例如标题、图例等（参见代码清单 7-16）。

```
In [2]: import matplotlib.pyplot as plt
x =[3,6,8,11,13,14,17,19,21,24,33,37]
y = [7.5,12,13.2,15,17,22,24,37,34,38.5,42,47]
x2 =[3,6,8,11,13,14,17,19,21,24,33]
y2 = [50,45,33,24,21.5,19,14,13,10,6,3]
plt.plot(x,y, label='First Line')
plt.plot(x2, y2, label='Second Line')
plt.xlabel('Plot Number')
plt.ylabel('Important var')
plt.title('Interesting Graph\n2018 ')
plt.yticks([0,5,10,15,20,25,30,35,40,45,50],
['0B','5B','10B','15B','20B','25B','30B','35B','40B','45B','50
B'])
plt.legend()
plt.show()
Listing 7-16
Matplotlib Line Plotting
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figaj_HTML.jpg](img/472943_1_En_7_Figaj_HTML.jpg)

```
In [13]: plt.plot(phd, label='Ph.D.')
plt.plot(service, label="service")
plt.xlabel('Ph.D./service')
plt.ylabel('Frequency')
plt.title('Ph.D./service\nDistribution')
plt.legend()
plt.show()
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figak_HTML.jpg](img/472943_1_En_7_Figak_HTML.jpg)

```
In [15]: plt.plot(phd, service, 'bo', label="Ph.D. Vs services", lw=10)
plt.grid()
plt.legend()
plt.xlabel('Ph.D')
plt.ylabel('service')
plt.title('Ph.D./salary\nDistribution')
plt.yscale('log')
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figal_HTML.jpg](img/472943_1_En_7_Figal_HTML.jpg)

### 条形图

代码清单 7-17 展示了如何创建一个条形图来展示注册课程的学生人数；有两名学生注册了四门课程。

```
In [3]: Students = [2,4,6,8,10]
Courses = [4,5,3,2,1]
plt.bar(Students,Courses, label="Students/Courses")
plt.xlabel('Students ')
plt.ylabel('Courses')
plt.title('Students Courses Data\n 2018')
plt.legend()
plt.show()
Listing 7-17
Matplotlib Bar Chart Plotting
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figam_HTML.jpg](img/472943_1_En_7_Figam_HTML.jpg)

```
In [4]: Students = [2,4,6,8,10]
Courses = [4,5,3,2,3]
stds = [3,5,7,9,11]
Projects = [1,2,4,3,2]
plt.bar(Students, Courses, label="Courses", color="r")
plt.bar(stds, Projects, label="Projects", color="c")
plt.xlabel('Students')
plt.ylabel('Courses/Projects')
plt.title('Students Courses and Projects Data\n 2018')
plt.legend()
plt.show()
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figan_HTML.jpg](img/472943_1_En_7_Figan_HTML.jpg)



### 直方图

清单 7-18 展示了如何创建显示年龄频率的直方图；数据集中大多数人年龄在 30 到 40 岁之间。此外，你还可以创建工龄和博士数量的直方图。

```
In [5]: Ages = [22.5, 10, 55, 8, 62, 45, 21, 34, 42, 45, 99, 75, 82,
77, 55, 43, 66, 66, 78, 89, 101, 34, 65, 56, 25, 34,
52, 25, 63, 37, 32]
binsx = [0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100, 110]
plt.hist(Ages, bins=binsx, histtype="bar", rwidth=0.7)
plt.xlabel('Ages')
plt.ylabel('Frequency')
plt.title('Ages frequency for sample pouplation\n 2018')
plt.show()
Listing 7-18
Matplotlib Histogram Plotting
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figao_HTML.jpg](img/472943_1_En_7_Figao_HTML.jpg)

```
In [18]: plt.hist(service, bins=30, alpha=0.4, rwidth=0.8, color="green", label="service")
plt.hist(phd, bins=30, alpha=0.4, rwidth=0.8, color="red", label="phd")
plt.xlabel('Services/phd')
plt.ylabel('Distribution')
plt.title('Services/phd\n 2018')
plt.legend(loc='upper right')
plt.show()
```

可视化获得博士学位后的工作年限。

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figap_HTML.jpg](img/472943_1_En_7_Figap_HTML.jpg)

```
In [19]: plt.hist(service, bins=10, alpha=0.4, rwidth=0.8, color="green", label="service")
plt.hist(phd, bins=10, alpha=0.4, rwidth=0.8, color="red", label="phd")
plt.xlabel('Services/phd')
plt.ylabel('Distribution')
plt.title('Services/phd\n 2018')
plt.legend(loc='upper right')
plt.show()
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figaq_HTML.jpg](img/472943_1_En_7_Figaq_HTML.jpg)

```
In [21]: plt.hist(salary, bins=100)
plt.show()
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figar_HTML.jpg](img/472943_1_En_7_Figar_HTML.jpg)

### 散点图

清单 7-19 展示了如何创建散点图来呈现注册课程的学生情况，其中四名学生注册了五门课程。

```
In [7]: Students = [2,4,6,8,6,10, 6] Courses = [4,5,3,2,4, 3, 4]
plt.scatter(Students,Courses, label='Students/Courses', color="green", marker='*', s=75 )
plt.xlabel('Students')
plt.ylabel('Courses')
plt.title('Students courses\n Spring 2018')
plt.legend()
plt.show()
Listing 7-19
Matplotlib Scatter Plot
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figas_HTML.jpg](img/472943_1_En_7_Figas_HTML.jpg)

```
In [16]: plt.scatter(rank,salary, label='salary/rank', color="g", marker='+', s=50 )
plt.xlabel('rank') plt.ylabel('salary')
plt.title('salary/rank\n Spring 2018')
plt.legend() plt.show()
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figat_HTML.jpg](img/472943_1_En_7_Figat_HTML.jpg)

```
In [20]: plt.scatter(phd,salary, label='Salary/phd', color="g", marker='+', s=80 )
plt.xlabel('phd') plt.ylabel('salary')
plt.title('phd/ salary\n Spring 2018')
plt.legend() plt.show()
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figau_HTML.jpg](img/472943_1_En_7_Figau_HTML.jpg)

### 堆叠图

堆叠图展示了每种活动的频率，例如每天睡眠、饮食、工作和娱乐的频率（参见清单 7-20）。在此数据集中，第 2 天，一个人花了 8 小时睡眠、3 小时饮食、8 小时工作和 5 小时娱乐。

```
In [9]: days = [1,2,3,4,5]
sleeping = [7,8,6,11,7]
eating = [2,3,4,3,2]
working = [7,8,7,2,2]
playing = [8,5,7,8,13]
plt.plot([],[], color="m", label="Sleeping")
plt.plot([],[], color="c", label="Eating")
plt.plot([],[], color="r", label="Working")
plt.plot([],[], color="k", label="Playing")
plt.stackplot(days, sleeping, eating, working , playing, colors=['m','c', 'r', 'k'])
plt.xlabel('days')
plt.ylabel('Activities')
plt.title('Persons Weekly Spent Time per activities\n Spring 2018')
plt.legend()
plt.show()
Listing 7-20
Persons Weekly Spent Time per activities using Matplotlib Stack Plot
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figaw_HTML.jpg](img/472943_1_En_7_Figaw_HTML.jpg)

### 饼图

在清单 7-21 中，你使用 `explode` 属性来分离出特定活动。之后，你可以将性别和标题添加到饼图中。

```
In [10]: days = [1,2,3,4,5]
sleeping = [7,8,6,11,7]
eating = [2,3,4,3,2]
working = [7,8,7,2,2]
playing = [8,5,7,8,13]
slices = [39,14,26,41]
activities = ['sleeping', 'eating', 'working', 'playing']
cols = ['c','m','r', 'b','g']
plt.pie(slices,
labels= activities,
colors= cols,
startangle=100,
shadow=True,
explode = (0.0,0.0,0.09,0),
autopct = '%1.1f%%')
plt.title('Persons Weekly Spent Time per activities\n Spring 2018')
plt.legend()
plt.show()
Listing 7-21
Persons Weekly Spent Time per activities using Matplotlib Pie Chart
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figax_HTML.jpg](img/472943_1_En_7_Figax_HTML.jpg)

## 总结

本章涵盖了如何绘制来自不同集合结构的数据。你学习了以下内容：

*   如何使用 Python 绘图工具（如折线图、条形图、饼图、箱线图、直方图和散点图）直接从序列、数据框或面板绘制数据

*   如何使用带状图、箱线图、蜂群图和联合图实现 Seaborn 绘图系统

*   如何使用折线图、条形图、直方图、散点图、堆叠图和饼图实现 Matplotlib 绘图

下一章将通过两个不同的案例研究来介绍你在本书中学到的技术；它将提供建议以及更多内容。



## 练习与答案

1.  为一个季节内六个城市生成 500 个随机温度读数，然后使用 Matplotlib 绘制生成的数据。

**答案：** 参见代码清单 7-22。

```
In [4]: import matplotlib.pyplot as plt
plt.style.use('classic')
%matplotlib inline
import numpy as np
import pandas as pd
In [30]: # 创建温度数据
rng = np.random.RandomState(0)
season1 = np.cumsum(rng.randn(500, 6), 0)
In [32]: # 使用 Matplotlib 默认设置绘制数据
plt.plot(season1)
plt.legend('ABCDEF', ncol=2, loc='upper left');
代码清单 7-22
绘制六个城市的温度数据
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figay_HTML.jpg](img/472943_1_En_7_Figay_HTML.jpg)

2.  加载著名的鸢尾花数据集，该数据集列出了三种鸢尾花的花瓣和花萼测量值。然后使用 `.pairplot()` 方法绘制每对变量之间的相关性。

**答案：** 参见代码清单 7-23。

```
In [33]: import seaborn as sns
iris = sns.load_dataset("iris")
iris.head()
sns.pairplot(iris, hue="species", size=2.5);
代码清单 7-23
成对相关性
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figaz_HTML.jpg](img/472943_1_En_7_Figaz_HTML.jpg)

3.  加载著名的小费数据集，该数据集根据各种指标数据显示餐厅员工收到的小费金额；然后根据员工性别绘制每笔账单的小费百分比。

**答案：** 参见代码清单 7-24。

```
In [36]: import seaborn as sns
tips = sns.load_dataset('tips')
tips.head()
代码清单 7-24
小费数据集中的前五条记录
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figba_HTML.jpg](img/472943_1_En_7_Figba_HTML.jpg)

```
In [37]: tips['小费百分比'] = 100 * tips['tip'] /tips['total_bill']
grid = sns.FacetGrid(tips, row="sex", col="time", margin_titles=True)
grid.map(plt.hist, "小费百分比", bins=np.linspace(0, 40, 15));
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figbb_HTML.jpg](img/472943_1_En_7_Figbb_HTML.jpg)

4.  加载著名的小费数据集，该数据集根据各种指标数据显示餐厅员工收到的小费金额；然后实现因子图，根据员工性别可视化每日总账单。

**答案：** 参见代码清单 7-25。

```
In [39]: import seaborn as sns
tips = sns.load_dataset('tips')
with sns.axes_style(style='ticks'):
g = sns.factorplot("day", "total_bill", "sex", data=tips, kind="box")
g.set_axis_labels("账单日期", "总账单金额")
代码清单 7-25
实现因子图绘制
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figbc_HTML.jpg](img/472943_1_En_7_Figbc_HTML.jpg)

5.  使用 Seaborn 联合分布图重新实现上一个练习。

**答案：** 参见代码清单 7-26。

```
In [43]: import seaborn as sns
tips = sns.load_dataset('tips')
with sns.axes_style('white'):
sns.jointplot( "total_bill", "tip", data=tips, kind="hex")
代码清单 7-26
实现联合分布图
```

![../images/472943_1_En_7_Chapter/472943_1_En_7_Figbd_HTML.jpg](img/472943_1_En_7_Figbd_HTML.jpg)

