# 脚本示例：挖掘推特（Twitter）

## 介绍

本示例演示了一种挖掘推特的方法，并展示了从一次挖掘调用中获取的结果。该示例以脚本形式存储在硬盘上，并非一个函数。脚本中混合使用了 S3 和 S4 对象。`tm`（此处使用的文本挖掘包）中的大多数对象都是 S4 对象和方法。

## 设置推特开发者账户

要挖掘推特，您必须在推特上创建一个开发者账户并创建一个应用。要创建开发者账户，您必须拥有一个推特账户。如果您已有推特账户，请在 `https://developer.twitter.com` 创建开发者账户。否则，请先注册一个推特账户，然后再创建开发者账户。

要在开发者网站开通账户，请点击顶部菜单右侧的 `Apply` 按钮。按照指示操作。当账户获批后，您为开发者账户选择的名称应显示在开发者页面顶部菜单的右侧。点击名称下方的下拉菜单，选择 `Apps` 选项。

在打开的窗口中，选择 `Create an App` 按钮。按照指示创建一个应用。在应用创建后打开的页面上，点击 `Details` 并查看 `Keys and tokens` 选项卡。在此处，您将找到消费者 API 密钥和 API 秘密密钥：

```
Consumer API keys
############################################## (API key)
############################################## (API secret key)
```

（上述的井号在实际结果中会变成字母和数字。）

消费者 API 密钥下方有一个用于重新生成密钥的按钮。您可以随时重新生成密钥。该按钮下方是用于生成令牌的按钮。

选择生成令牌的按钮。结果将是：

```
Access token & access token secret
######################################### (Access token)
######################################### (Access token secret)
```

（井号在实际结果中大多会是字母和数字。）令牌下方是访问级别。默认访问权限为读写：

```
Read and write (Access level)
```

访问级别下方是用于撤销或重新生成令牌的按钮。令牌可以随时撤销或重新生成。R 语言中的 `twitteR` 包使用这些令牌和密钥来连接推特 API。

当运行 R 或 R Studio 时，推特开发者应用必须处于开启状态，否则当 R 尝试连接推特时程序会崩溃，您所有的工作都将丢失。

## R 脚本

脚本中首先加载了 `twitteR` 和 `tm`（用于文本挖掘）这两个库。脚本如下：

```r
library( twitteR )
library( tm )
# 连接推特；consumer_key、consumer_secret、
# access_token、access_secret 是由推特生成的。
setup_twitter_oauth(
consumer_key    = "#########################",
consumer_secret = "#########################################",
access_token    = "#########################################",
access_secret   = "##########################################"
)
# 获取最多 100 条关于"Clinton"且位于北纬 42 度、西经 95.5 度
# 70 英里范围内的推文。推文按时间倒序获取。
tweetsClinton = searchTwitter( "Clinton", n = 100, geocode = "42,-95.5,70mi" )
# tweetsClinton 及其元素（tweetsClinton 是一个列表）的类型和类。
print( typeof( tweetsClinton ) )
print( class(  tweetsClinton ) )
print( typeof( tweetsClinton[[1]] ) )
print( class(  tweetsClinton[[1]] ) )
# 将 tweetsClinton 中的 S4 对象处理成 S3 矩阵，
# 矩阵内容为每条推文中某个特定单词的出现次数。
# 单词被分配为行名。
tweetsClintonDF = twListToDF( tweetsClinton )
ClintonCorpus = Corpus( VectorSource( tweetsClintonDF$text ) )
ClintonTDM = TermDocumentMatrix( ClintonCorpus )
ClintonMatrix = as.matrix( ClintonTDM )
# 创建一个数据框，第一列为单词，
# 第二列为单词频率。仅保留
# 频率大于 12 的单词。输出结果。
ClintonFrqMat = data.frame( Word = rownames( ClintonMatrix ),
Frequency = rowSums( ClintonMatrix ) )
ClintonFrqMatReduced = ClintonFrqMat[ ClintonFrqMat[ , 2 ] > 12, ]
print( ClintonFrqMatReduced )
```

## 运行结果与分析

在 R 中运行该脚本得到：

```
> source('~/Documents/RQSRexample.R')
[1] "Using direct authentication"
[1] "list"
[1] "list"
[1] "S4"
[1] "status"
attr(,"package")
[1] "twitteR"
Word Frequency
and                   and        19
for                   for        13
hillary           hillary        23
https               https        77
clinton           clinton        43
that                 that        15
the                   the        58
was                   was        16
trump               trump        18
you                   you        14
georgepapa19 georgepapa19        15
```

这些推文未经任何方式清洗。通常，推文会被简化为有实际意义的单词。注意 `clinton` 在推文中只出现了 43 次。这些推文既包括与 Clinton 相关的推文，也包括包含单词 `Clinton` 的推文。

上述信息的来源包括 R 帮助页面和 Stack Overflow。

## 获取更多信息

关于上述函数的更多信息，可以在 R 提示符下输入 `??tm::tm` 或 `??twitteR::twitteR`，或使用 R Studio 中的 `Help` 选项卡找到。