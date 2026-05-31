#           权力的游戏

`print(first_h1.text)`       # 权力的游戏剧集列表
`print(first_h1.get_text())` # 功能相同
`print(first_h1.attrs)`

##### 输出：{'id': 'firstHeading', 'class': ['firstHeading'], 'lang': 'en'}

`print(first_h1.attrs['id'])` # firstHeading
`print(first_h1['id'])`       # 功能相同
`print(first_h1.get('id'))`   # 功能相同
`print('------------ 引用 ------------')`

##### 找到前五个包含引用类的 cite 元素

`cites = html_soup.find_all('cite', class_="citation", limit=5)`
`for cite in cites:`
`    print(cite.get_text())`

##### 在此 cite 元素内，找到第一个 a 标签

`link = cite.find('a')`

##### ... 并显示其 URL

`print(link.get('href'))`
`print()`

#### 关于稳健性的注意事项

请仔细查看上述示例中的"引用"部分。如果 `<cite>` 元素内没有 `<a>` 标签，会发生什么？在这种情况下，`link` 变量会被设为 `None`，而 `link.get('href')` 这行代码会导致程序崩溃。编写网络爬虫时务必小心谨慎，并为最坏情况做好准备。在"安全环境"的示例中，为了简洁起见，我们可以允许自己有些马虎，但在实际场景中，您需要额外检查 `link` 是否为 `None`，并据此采取相应措施。

在进入下一个示例之前，关于 `find` 和 `find_all` 还有两点小说明。如果您发现自己需要遍历如下标签名称链：

```python
tag.find('div').find('table').find('thead').find('tr')
```

请记住，Beautiful Soup 也允许我们用简写方式来实现：

```python
tag.div.table.thead.tr
```

同样，下面这行代码：

```python
tag.find_all('h1')
```

等同于调用：

```python
tag('h1')
```

虽然这同样是为了方便而提供，但在本书中我们仍会继续完整使用 `find` 和 `find_all`，因为我们认为在这种场景下，表达得稍微明确一些有助于提升可读性。

现在让我们尝试解决以下用例。您会注意到我们的《权力的游戏》维基百科页面中，有许多结构良好的表格列出了各集及其导演、编剧、播出日期和收视人数。让我们利用所学知识一次性获取所有这些数据：

```python
import requests
from bs4 import BeautifulSoup
url = 'https://en.wikipedia.org/w/index.php' + \
'?title=List_of_Game_of_Thrones_episodes&oldid=802553687'
r = requests.get(url)
html_contents = r.text
html_soup = BeautifulSoup(html_contents, 'html.parser')

##### 我们将使用列表来存储剧集信息
episodes = []
ep_tables = html_soup.find_all('table', class_="wikiepisodetable")
for table in ep_tables:
    headers = []
    rows = table.find_all('tr')

    # 首先从第一行获取表头单元格以确定字段名称
    for header in table.find('tr').find_all('th'):
        headers.append(header.text)

    # 然后遍历除第一行之外的所有行
    for row in table.find_all('tr')[1:]:
        values = []

        # 获取列单元格，第一个单元格在 th 标签内
        for col in row.find_all(['th','td']):
            values.append(col.text)
        if values:
            episode_dict = {headers[i]: values[i] for i in range(len(values))}
            episodes.append(episode_dict)
```



##### 显示结果
```python
for episode in episodes:
    print(episode)
```

目前来看，大部分代码应该相对直观，不过仍有几点值得指出：

* 我们并非凭空想出 "`find_all('table', class_="wikiepisodetable")`" 这一行代码，尽管仅看代码可能会产生这种错觉。回想一下我们之前提到的：浏览器的开发者工具会成为你的最佳助手。检查页面上的剧集表格。注意它们都是通过 "`<table>`" 标签定义的。然而，页面也包含我们不想提取的表格。经过进一步调查，我们找到了一个解决方案：所有剧集表格都将 "wikiepisodetable" 作为其 class 属性值，而其他表格则没有。在得出可靠的方案之前，你通常需要先在页面上摸索一番。在许多情况下，你需要进行多次 `find` 和 `find_all` 迭代，才能到达目标位置。

* 对于每个表格，我们首先需要获取表头，用作 Python 字典中的键。为此，我们首先选中第一个 "`<tr>`" 标签，然后选取其中所有的 "`<th>`" 标签。

* 接下来，我们遍历所有行（"`<tr>`" 标签），但跳过第一行（表头行）。对于每一行，我们遍历 "`<th>`" 和 "`<td>`" 标签来提取列值（第一列包含在 "`<th>`" 标签内，其他列在 "`<td>`" 标签内，这就是为什么我们需要同时处理两者）。每处理完一行，我们就准备向 `episodes` 变量中添加一个新条目。为了存储每个条目，我们使用一个标准的 Python 字典（`episode_dict`）。如果你对 Python 不太熟悉，这个字典对象的构造方式可能看起来有点奇怪。也就是说，Python 允许我们通过在 "[...]" 或 "{...}" 括号内放入一个 "for" 结构，来“一步到位”地构造完整的列表或字典。在这里，我们利用这个特性，立即遍历 `headers` 和 `values` 列表来构建字典对象。请注意，这假定了这两个列表的长度相同，并且它们的顺序匹配，例如，`headers[2]` 处的表头对应于 `values[2]` 处的值。由于我们处理的是相当简单的表格，这是一个安全的假设。

#### 表格值得费心吗？

到目前为止，你可能对这个例子并不十分感冒。大多数现代浏览器允许你简单地选择或右键点击网页上的表格，并能直接将它们复制到诸如 Excel 之类的电子表格程序中。确实如此，如果你只需要提取一个表格，这绝对是更简单的途径。然而，一旦你需要处理大量表格，特别是当它们分布在多个页面，或者需要定期从特定网页刷新表格数据时，编写爬虫程序的好处就开始显现出来了。

多拿这个代码片段做做实验。你应该能够解决以下问题：

* 尝试从页面中提取所有链接以及它们指向的地址（提示：在 "`<a>`" 标签中查找 "href" 属性）。

* 尝试从页面中提取所有图片。

* 尝试从页面中提取 "ratings" 表格。这个有一点棘手。你可能会倾向于使用 "`find('table', class_="wikitable")`"，但你会发现，这匹配到了页面上的第一个表格，尽管它的 class 属性设置为 "wikitable plainrowheaders"。确实，对于可以接受多个空格分隔值的 HTML 属性（如 "class"），Beautiful Soup 会进行部分匹配。要获取我们想要的表格，你要么必须遍历页面上所有的 "wikitable" 表格，并检查其 `text` 属性以确保找到正确的那个，要么尝试找到一个唯一的父元素，然后从它开始向下钻取，例如，"`find('div', align="center").find('table', class_="wikitable")`" —— 至少目前是这样，你将在接下来的章节中学习到一些更高级的 Beautiful Soup 特性。

#### 类是特殊的

对于可以接受多个空格分隔值的 HTML 属性（如 "class"），Beautiful Soup 会进行部分匹配。这在以下情况下可能很棘手：你想进行精确匹配，例如“查找具有'myclass'类且仅有该类的元素”，或者你想选择匹配多个类的 HTML 元素。在这种情况下，你可以写类似 "`find(class_='class-one class-two')`" 的语句，不过这种工作方式相当脆弱且有风险（类名在 HTML 页面中必须按相同顺序且相邻出现，但这并不总是成立）。另一种方法是将你的过滤器包装在一个列表中，即 "`find(class_=['class-one', 'class-two'])`"，但这同样得不到想要的结果：这个语句不会匹配同时具有 "class-one" 和 "class-two" 类的元素，而是会匹配具有其中任意一个类的元素！为了稳健地解决这个问题，我们首先需要多学习一点关于 Beautiful Soup 的知识……

### 3.5 深入 Beautiful Soup

现在我们了解了 Beautiful Soup 的基础知识，可以准备进一步探索这个库了。首先，尽管我们已经了解 `find` 和 `find_all` 的基本用法，但重要的是要注意到这些方法实际上非常灵活。我们已经看到如何根据简单的标签名或标签名列表进行过滤：

```python
html_soup.find('h1')
html_soup.find(['h1', 'h2'])
```

然而，这些方法也可以接受其他类型的对象，例如正则表达式对象。下面的代码行使用 Python 的 "re" 模块构造了一个正则表达式，它将匹配所有以字母 "h" 开头的标签：

```python
import re
html_soup.find(re.compile('^h'))
```

#### 正则表达式

如果你之前没有听说过正则表达式，那么正则表达式（regex）定义了一系列模式（一个表达式），用于定义一种搜索模式。它经常被用于字符串搜索和匹配代码，以查找（和替换）字符串片段。尽管它们是非常强大的结构，但也会被误用。例如，最好不要过度使用冗长或复杂的正则表达式，因为它们的可读性很差，而且事后可能很难弄清楚某段正则表达式的作用。顺便提一下，这也是一个很好的时机来指出：你应该避免使用正则表达式来解析 HTML 页面。我们本可以更早地引入正则表达式，作为一种从 HTML soup 中解析内容片段的方法，完全不使用 Beautiful Soup。然而，这是一个糟糕的主意。正如我们所看到的，HTML 页面非常混乱，为了从页面中提取内容，你很快会陷入大量意大利面条式代码的泥潭。始终使用像 Beautiful Soup 这样的 HTML 解析器来执行繁重的工作。然后你可以使用小的正则表达式片段（如此处所示）来查找或提取内容片段。

除了字符串、列表和正则表达式，你还可以传递一个函数。这在其他方法行不通的复杂情况下很有帮助：

```python
def has_classa_but_not_classb(tag):
    cls = tag.get('class', [])
    return 'classa' in cls and not 'classb' in cls

html_soup.find(has_classa_but_not_classb)
```

请注意，你也可以将列表、正则表达式和函数传递给 `find` 和 `find_all` 的 `attrs` 字典值、`string` 和 `**keyword` 参数。

除了 `find` 和 `find_all`，还有一些其他用于搜索 HTML 树的方法，它们与 `find` 和 `find_all` 非常相似。区别在于它们会搜索 HTML 树的不同部分：

* `find_parent` 和 `find_parents` 沿着树向上搜索，使用标签的 `parents` 属性查看其父标签。请记住，`find` 和 `find_all` 是沿着树向下搜索，查看标签的后代。

* `find_next_sibling` 和 `find_next_siblings` 将使用 `next_siblings` 属性迭代并匹配标签的兄弟标签。

* `find_previous_sibling` 和 `find_previous_siblings` 做同样的事情，但使用的是 `previous_siblings` 属性。

* `find_next` 和 `find_all_next` 使用 `next_elements` 属性来迭代并匹配文档中某个标签之后的所有内容。

* `find_previous` 和 `find_all_previous` 则相反，使用 `previous_elements` 属性向后执行搜索。

* 请记住，当 `recursive` 参数设置为 False 时，`find` 和 `find_all` 作用于 `children` 属性；当 `recursive` 设置为 True 时，则作用于 `descendants` 属性。

#### 如此多的方法

虽然没有明确文档说明，但也可以使用 `findChild` 和 `findChildren` 方法（注意不是 `find_child` 和 `find_children`），它们分别被定义为 `find` 和 `find_all` 的别名。不过并没有 `findDescendant` 方法，所以请记住，使用 `findChild` 默认会搜索所有 `descendants`（就像 `find` 一样），除非你将 `recursive` 参数设置为 False。这确实令人困惑，所以最好避免使用这些方法。

尽管所有这些方法都可能派上用场，但你会发现 `find` 和 `find_all` 在导航 HTML 树时承担了大部分工作。然而，还有一个极其有用的方法：`select`。终于，我们之前看到的 CSS 选择器可以派上用场了。使用这个方法，你只需传递一个 CSS 选择器规则的字符串。

Beautiful Soup 会返回一个匹配该规则的元素列表：



##### 查找所有标签
`html_soup.select('a')`

##### 查找 ID 为 info 的元素
`html_soup.select('#info')`

##### 查找同时包含 classa 和 classb 这两个 CSS 类的标签
`html_soup.select(div.classa.classb)`

##### 查找 href 属性以 http://example.com/ 开头的标签
`html_soup.select('a[href^="http://example.com/"]')`

##### 查找属于 class 为 lst 的标签的子标签
`html_soup.select(ul.lst > li')`

一旦你开始习惯使用 CSS 选择器，这种方法会变得非常强大。例如，如果我们想从 **《权力的游戏》** 维基百科页面中找到引用链接，可以简单地运行：

```
for link in html_soup.select('ol.references cite a[href]'):
print(link.get('href'))
```

不过，Beautiful Soup 中的 CSS 选择器规则引擎并不像现代网页浏览器中的那么强大。以下规则是有效的选择器，但在 Beautiful Soup 中无法生效：

```
##### 这种方式将无法生效：
# cite a[href][rel=nofollow]

##### 你可以改用：
tags = [t for t in html_soup.select('cite a[href]') \
if 'nofollow' in t.get('rel', [])]

##### 这种方式将无法生效：
# cite a[href][rel=nofollow]:not([href*="archive.org"])
```



##### 可以改用：

```python
tags = [t for t in html_soup.select('cite a[href]') \
if 'nofollow' in t.get('rel', []) and 'archive.org' not in t.get('href')]
```

幸运的是，需要借助如此复杂选择器的情况并不多见，而且请记住，你仍然可以使用 `find()`、`find_all()` 以及相关方法（不妨尝试用上面两个示例练习一下，完全不使用 `select()` 来重写代码）。

#### 条条大路通元素

细心的读者会发现，编写 CSS 选择器来获取相同结果常有多条路径。也就是说，除了写 `cite a`，你还可以更进一步写成 `body div.reflist ol.references li cite.citation a`，结果也是一样的。不过，一般来说，写好实践规范是让选择器的粒度恰好能获取所需内容即可。网站经常变动，如果你打算长时间在生产环境使用网络爬虫，那么在精确性和鲁棒性之间找到良好的平衡，能省去不少麻烦。这样一来，即便网站所有者决定调整 HTML 结构、类名或属性等，你的爬虫也能尽可能保持对未来的兼容性。话虽如此，总会有那么一刻，网站变动的幅度之大足以破坏你的选择器。在代码中加入额外检查并提供早期预警信号，有助于构建鲁棒的爬虫。

最后，关于 Beautiful Soup 还有一个细节要提。到目前为止，我们主要讨论的是 `BeautifulSoup` 对象本身（即上文示例中的 `html_soup` 变量），以及通过 `find()`、`find_all()` 和其他搜索操作获取的 `Tag` 对象。Beautiful Soup 中还有另外两种对象类型，虽然不太常用，但了解它们也有好处：

- `NavigableString` 对象：用于表示标签内部的文本，而非标签本身。某些 Beautiful Soup 函数和属性会返回这类对象，例如标签的 `string` 属性。`descendants` 等属性在枚举时也会包含这些对象。此外，如果使用 `find()` 或 `find_all()` 方法时只提供了 `string` 参数而没有提供 `name` 参数，那么返回的将是 `NavigableString` 对象，而非 `Tag` 对象。

- `Comment` 对象：用于表示 HTML 注释（即位于注释标签 `<!-- ... -->` 中的内容）。在网络爬取时，这些内容几乎派不上用场。

如果你感兴趣，可以继续用 Beautiful Soup 多做一些练习。花些时间研究该库的文档，地址是 [`https://www.crummy.com/software/BeautifulSoup/bs4/doc/`](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)。请注意，Beautiful Soup 的文档结构不如 requests 的文档清晰，读起来更像是一份完整的教程，而非参考指南。在下一章中，我们将暂时离开 HTML，回到 HTTP 并进行更深入的探讨。

## 第二部分：高级网络爬取

