# 1. 人工智能系统概览

在本书中，我们假设你熟悉 Python 编程。在本介绍性章节中，我们将解释为什么数据科学家应该选择 Python 作为编程语言。然后，我们将指出一些 Python 可能并非理想选择的情况。最后，我们将描述一些应用程序开发的最佳实践，并给出数据科学家在日常工作中可能需要的代码示例。

## Python 中的面向对象编程

在本节中，我们将解释 Python 语境下面向对象编程（OOP）的一些特性。

任何现代应用程序最基本的元素都是 `对象`。对于程序员或架构师而言，世界就是对象的集合。对象由两类成员组成：`属性` 和 `方法`。成员可以是私有的、公有的或受保护的。`类` 是对象的数据类型。每个对象都是某个类的一个实例。一个类可以被子类继承。两个类可以通过 `组合` 进行关联。

Python 没有用于表示 public、private 或 protected 的关键字，因此 `封装`（对外部世界隐藏成员）在 Python 中并非隐式实现。与 C++ 类似，它支持多级继承和多重继承。与 Java 类似，它有一个 `abstract` 关键字。类和方法都可以是抽象的。

在以下代码中，我们描述了一个不依赖任何机器学习的面向对象问答系统。程序的输入是 `input.txt` 中的一组对话，如下所示：

```
glob is I
prok is V
pish is X
tegj is L
glob glob Silver is 34 Credits
glob prok Gold is 57800 Credits
pish pish Iron is 3910 Credits
how much is pish tegj glob glob ?
how many Credits is glob prok Silver ?
how many Credits is glob prok Gold ?
how many Credits is glob prok Iron ?
how much wood could a woodchuck chuck if a woodchuck could chuck wood?
```

程序在 `config.txt` 中有一个知识库。

```
I,1,roman
V,5,roman
X,10,roman
L,50,roman
C,100,roman
D,500,roman
M,1000,roman
```

基于此输入和配置程序，对 `input.txt` 中问题的答案将在标准输出中给出，如下所示：

```
pish tegj glob glob is 42
glob prok Silver is 68 Credits
glob prok Gold is 57800 Credits
glob prok Iron is 782 Credits
I have no idea what you are talking about
```

解析逻辑位于 `Observer` 类中。

```
import operator
#此类别验证输入的有效性
class Observer(object):
#存储符号的频率
length = {}
#最频繁出现的符号
symbol = ''
#最频繁出现符号的计数
count = 0
#调用类
compiler = None
def __init__(self,cmpiler):
self.compiler = cmpiler
def initialize(self, arr):
for i in range(len(arr)):
self.length[arr[i]] = 0
#每次符号出现时增加计数
def increment(self,symbol):
self.length[symbol] = self.length[symbol] + 1
#计算最频繁出现的符号及其计数
def calculate(self):
self.symbol,self.count = max(self.length.items(), key=operator.itemgetter(1))
#验证是否减去了错误的符号，例如 (V, ..
def verifySubstract(self, current):
while current % 10 != 0:
current = current / 10
if current == 5:
raise Exception("Wrong Substraction")
def evaluate(self):
#检查最大重复次数是否超过限制
if self.count > 3:
raise Exception("Repeat more than 3")
#符号是否正确
if self.symbol not in self.compiler.symbol_map:
raise Exception("Wrong Symbol")
#检查是否重复了错误的符号，例如 (V, ..
self.symbol,unit = self.compiler.evaluateSymbol(self.symbol)
while self.symbol % 10 != 0:
self.symbol = self.symbol / 10
if self.count > 1 and  self.symbol == 5:
raise Exception("Wrong Symbol repeated")
#检查输入句子是否正确
def evaluateSentence(self, line):
if "is" not in line:
return "I have no idea what you are talking about"
```

编译逻辑位于 `compiler` 类中，如下所示：

```python
import sys
from observer import Observer
class compilerTrader(object):
#存储符号与分数和单位的映射
symbol_map = {}
#存储有效符号的列表
valid_values = []
#读取配置并初始化类成员
def __init__(self, config_path):
with open(config_path) as f:
for line in f:
if ',' in line :
symbol, value, type = line.strip().split(',')
self.symbol_map[symbol] = float(value), type
self.valid_values.append(float(value))
f.close()
#评估某个符号的最终数值分数（含单位）
def evaluateSymbol(self, symbol):
while symbol not in self.valid_values:
symbol,unit = self.symbol_map[symbol]
return float(symbol), unit
#编译行中的信息
def compile_super(self, line):
obs = Observer(self)
if 'is' in line:
fields = line.split(' is ')
value = fields[-1]
var = fields[0]
#如果是一个符号和一个值
if ' ' not in var:
if value in self.symbol_map:
self.symbol_map[var] = int(self.symbol_map[value][0]) ,'roman'
else:
#处理带单位的值
if ' ' in value:
fields = value.split(' ')
user_unit = fields[-1]
if ' ' not in var:
self.symbol_map[var] = int(fields[0]), user_unit
else:
#处理输入中的多个符号
total = int(fields[0])
factor = 0
arr = var.split(' ')
obs.initialize(arr)
for i in range(len(arr)):
obs.increment(arr[i])
if arr[i] in self.symbol_map and arr[i+1] in self.symbol_map and i = next:
factor =  factor + current
else:
obs.verifySubstract(current)
factor = factor - current
else:
if arr[i] in self.symbol_map:
current, current_unit = self.evaluateSymbol([arr[i]][0])
factor = factor + current
else:
self.symbol_map[arr[i]] = total/factor, user_unit
self.valid_values.append(total/factor)
obs.calculate()
obs.evaluate()
```

回答逻辑位于回答层，该层调用 `Observer` 和 `compiler`。`answering` 类继承自 `compiler` 类。

```python
import sys
from observer import Observer
from compiler import compilerTrader
class answeringTrader(compilerTrader):
def __init__(self, config_path):
super().__init__(config_path)
#编译行中的信息
def compile(self, line):
super().compile_super(line)
#回答行中的查询
def answer(self, line):
obs = Observer(super())
if 'is' in line:
values = line.split(' is ')[-1]
ans = 0
arr = values.split(' ')
unit = ''
obs.initialize(arr)
for i in range(len(arr)):
if arr[i] in "?.,!;":
continue
obs.increment(arr[i])
if i = next:
ans = ans + current
else:
if next_unit == 'roman':
obs.verifySubstract(current)
ans = ans - current
else:
ans = ans + current
else:
if arr[i] in super().symbol_map:
current,unit = super().evaluateSymbol([arr[i]][0])
if  unit != 'roman':
ans = ans * current
else:
ans = ans + current
obs.calculate()
obs.evaluate()
values = values.replace("?" , "is ")
if unit == 'roman':
unit = ''
return(values + str(ans) + ' ' + unit)
```

最后，主程序调用 `answering` 类和 `observer`，然后执行任务并对逻辑进行单元测试。

```python
import sys
import unittest
sys.path.append('./answerLayer')
sys.path.append('./compilerLayer')
sys.path.append('./utilityLayer')
from answer import answeringTrader
from observer import Observer
#框架的客户端接口
class ClientTrader(object):
trader = None
def __init__(self, config_path):
self.trader = answeringTrader(config_path)
#处理输入字符串
def process(self, input_string):
obs = Observer(self.trader)
valid = obs.evaluateSentence(input_string)
if valid is not None:
return valid
if input_string.strip()[-1] == '?' :
return self.trader.answer(input_string)
else:
return self.trader.compile(input_string)
#单元测试用例
class TestTrader(unittest.TestCase):
trader = None
def setUp(self):
pass
#测试非罗马符号（单位非罗马）的用例
def test_answer_unit(self):
ans = self.trader.process("how many Credits is glob prok Silver ?")
self.assertEqual(ans.strip(), "glob prok Silver is 68.0 Credits")
#测试仅含罗马符号（单位）的用例
def test_answer_roman(self):
ans = self.trader.process("how much is pish tegj glob glob ?")
self.assertEqual(ans.strip(), "pish tegj glob glob is 42.0")
#测试符号重复超过最大限制（3）的用例
def test_exception_over_repeat(self):
with self.assertRaises(Exception) as context:
ans = self.trader.process("how much is pish tegj glob glob glob glob ?")
self.assertTrue("Repeat more than 3" in context.exception)
#测试错误符号重复（例如 V...）的用例
def test_exception_unproper_repeat(self):
with self.assertRaises(Exception) as context:
ans = self.trader.process("how much is pish tegj D D ?")
self.assertTrue("Repeat more than 3" in context.exception)
#测试错误符号相减（例如 V...）的用例
def test_wrong_substraction(self):
with self.assertRaises(Exception) as context:
ans = self.trader.process("how much is V X ?")
self.assertTrue("Wrong Substraction" in context.exception)
#测试查询格式不正确的用例
def test_wrong_format_query(self):
ans = self.trader.process("how much wood could a woodchuck chuck if a woodchuck could chuck wood ?")
self.assertEqual(ans.strip(), "I have no idea what you are talking about")
if __name__ == '__main__':
if len(sys.argv) != 3:
print("Usage is : " + sys.argv[0] + "   ")
exit(0)
tr = ClientTrader(sys.argv[2])
f = open(sys.argv[1])
for line in f:
response = tr.process(line.strip())
if response is not None:
print(response)
TestTrader.trader = tr
unittest.main(argv = [sys.argv[0]], exit = False)
```

你可以使用以下命令运行此程序：

```
python client.py input.txt config.txt
```

## 在 Python 中调用其他语言

现在我们将介绍如何在 Python 中使用其他语言。这里有两个示例。第一个是从 Python 调用 R 代码。某些用例需要用到 R 代码。例如，如果你需要一个现成的函数来实现时间序列中的 Holt-Winters 方法，这在 Python 中很难实现，但在 R 中却可以。因此，你可以使用 `rpy2` 模块从 Python 调用 R 代码，如下所示：

```
import rpy2.robjects as ro
ro.r('data(input)')
ro.r('x <-HoltWinters(input data frame)')
```

（你可以使用时间序列章节中给出的示例数据。）

有时你需要从 Python 调用 Java 代码。例如，假设你正在处理自然语言处理（NLP）领域的命名实体识别问题；给定一些文本作为输入，你需要识别文本中的名称。Python 的 `NLTK` 包确实有命名实体识别功能，但其准确性不高。此时，Stanford NLP 是更好的选择，但它是用 Java 编写的。你可以通过两种方式解决这个问题。

- 你可以使用 Python 代码在命令行调用 Java。在调用之前，你需要使用 `yum/apt-get install java` 命令安装 Java。
- 对于 Windows，建议你从 `https://adoptium.net/temurin/releases/?version=8` 安装 JRE。你也可以从其他发行版安装 JRE。安装过程会自动创建 `JAVA_HOME`。如果没有，你需要将 `JAVA_HOME` 设置为系统变量，其值应为 Java 安装文件夹的位置，例如 `JAVA_HOME=C:\Program Files\Eclipse Adoptium\jdk-8.0.345.1-hotspot\`。
- 你可以将 Stanford NLP 暴露为 Web 服务，并将其作为服务调用。（在运行此代码之前，你需要下载本书源代码中提供的 Stanford `nlp` JAR 文件。）

```
import subprocess
subprocess.call(['java','-cp','*','edu.stanford.nlp.sentiment.SentimentPipeline','-file','foo.txt'])
请将 foo.txt 放在运行 Python 代码的同一文件夹中。
```

```
nlp = StanfordCoreNLP('http://127.0.0.1:9000')
output = nlp.annotate(sentence, properties={
"annotators": "tokenize,ssplit,parse,sentiment",
"outputFormat": "json",
## 仅在行末拆分句子。我们假设此方法只接收单个句子。
"ssplit.eolonly": "true",
## 设置 enforceRequirements 以跳过某些注释器，加快处理速度
"enforceRequirements": "false"
})
```

你将在第 2 章中看到更详细的 Stanford NLP 示例。

## 将 Python 模块暴露为微服务

你可以将 Python 模型暴露为微服务，就像你的 Python 模型可以被其他人用来编写自己的代码一样。最好的方法是把你的模型暴露为一个 Web 服务。例如，以下代码使用 Flask 暴露了一个深度学习模型：

```
from flask import Flask, request, g
from flask_cors import CORS
import tensorflow as tf
from sqlalchemy import *
from sqlalchemy.orm import sessionmaker
import pygeoip
from pymongo import MongoClient
import json
import datetime as dt
import ipaddress
import math
app = Flask(__name__)
CORS(app)
@app.before_request
def before():
db = create_engine('sqlite:///score.db')
metadata = MetaData(db)
g.scores = Table('scores', metadata, autoload=True)
Session = sessionmaker(bind=db)
g.session = Session()
client = MongoClient()
g.db = client.frequency
g.gi = pygeoip.GeoIP('GeoIP.dat')
sess = tf.Session()
new_saver = tf.train.import_meta_graph('model.obj.meta')
new_saver.restore(sess, tf.train.latest_checkpoint('./'))
all_vars = tf.get_collection('vars')
g.dropped_features = str(sess.run(all_vars[0]))
g.b = sess.run(all_vars[1])[0]
return
def get_hour(timestamp):
return dt.datetime.utcfromtimestamp(timestamp / 1e3).hour
def get_value(session, scores, feature_name, feature_value):
s = scores.select((scores.c.feature_name == feature_name) & (scores.c.feature_value == feature_value))
rs = s.execute()
row = rs.fetchone()
if row is not None:
return float(row['score'])
else:
return 0.0
@app.route('/predict', methods=['POST'])
def predict():
input_json = request.get_json(force=True)
features = ['size','domain','client_time','device','ad_position','client_size', 'ip','root']
predicted = 0
feature_value = ''
for f in features:
if f not in g.dropped_features:
if f == 'ip':
feature_value = str(ipaddress.IPv4Address(ipaddress.ip_address(unicode(request.remote_addr))))
else:
feature_value = input_json.get(f)
if f == 'ip':
if 'geo' not in g.dropped_features:
geo = g.gi.country_name_by_addr(feature_value)
predicted = predicted + get_value(g.session, g.scores, 'geo', geo)
return str(math.exp(predicted + g.b)-1)
app.run(debug = True, host ='0.0.0.0')
```

这段代码将一个深度学习模型暴露为 Flask Web 服务。JavaScript 客户端将发送包含 Web 用户参数（如 IP 地址、广告尺寸、广告位置等）的请求，并返回广告价格作为响应。这些特征是分类变量。你将在第 3 章中学习如何将它们转换为数值分数。这些分数存储在内存数据库中。服务从数据库中获取分数，求和结果，然后回复给客户端。该分数将在深度学习模型的每次训练迭代中实时更新。它使用 MongoDB 来存储该 IP 地址在该网站上的访问频率。这是一个重要的参数，因为第一次访问网站的用户通常是在真正搜索某些内容，而对于访问频率大于 5 的用户则并非如此。IP 地址数量巨大，因此它们存储在分布式 MongoDB 数据库中。

## 高性能 API 与并发编程

当您构建一个同时包含图形用户界面（GUI）的通用解决方案时，Flask 是一个不错的选择。但如果高性能是应用程序最关键的需求，那么 Falcon 是最佳选择。以下代码是之前展示的相同模型通过 Falcon 框架暴露的示例。我们在该代码中做出的另一项改进是实现了多线程，因此代码将并行执行。除了 Falcon 特有的更改外，您还应注意使用线程池类并行化调用 `get_score` 函数的主要变化。

```python
import falcon
from falcon_cors import CORS
import json
from sqlalchemy import *
from sqlalchemy.orm import sessionmaker
import pygeoip
from pymongo import MongoClient
import json
import datetime as dt
import ipaddress
import math
from concurrent.futures import *
from sqlalchemy.engine import Engine
from sqlalchemy import event
import sqlite3
@event.listens_for(Engine, "connect")
def set_sqlite_pragma(dbapi_connection, connection_record):
cursor = dbapi_connection.cursor()
cursor.execute("PRAGMA cache_size=100000")
cursor.close()
class Predictor(object):
def __init__(self,domain):
db1 = create_engine('sqlite:///score_' + domain + '0test.db')
metadata1 = MetaData(db1)
self.scores = Table('scores', metadata1, autoload=True)
client = MongoClient(connect=False,maxPoolSize=1)
self.db = client.frequency
self.gi = pygeoip.GeoIP('GeoIP.dat')
self.high = 1.2
self.low = .8
def get_hour(self,timestamp):
return dt.datetime.utcfromtimestamp(timestamp / 1e3).hour
def get_score(self, featurename, featurevalue):
pred = 0
s = self.scores.select((self.scores.c.feature_name == featurename) & (self.scores.c.feature_value == featurevalue))
rs = s.execute()
row = rs.fetchone()
if row is not None:
pred = pred + float(row['score'])
res = self.db.frequency.find_one({"ip" : ip})
freq = 1
if res is not None:
freq = res['frequency']
pred2, prob2 = self.get_score('frequency', str(freq))
return (pred1 + pred2), (prob1 + prob2)
conn = sqlite3.connect('multiplier.db')
cursor = conn.execute("SELECT high,low from multiplier where domain='" + value + "'")
row = cursor.fetchone()
if row is not None:
self.high = row[0]
self.low = row[1]
return self.get_score(f, value)
def on_post(self, req, resp):
input_json = json.loads(req.stream.read(),encoding='utf-8')
input_json['ip'] = unicode(req.remote_addr)
pred = 1
prob = 1
with ThreadPoolExecutor(max_workers=8) as pool:
future_array = { pool.submit(self.get_value,f,input_json[f]) : f for f in input_json}
for future in as_completed(future_array):
pred1, prob1 = future.result()
pred = pred + pred1
prob = prob - prob1
resp.status = falcon.HTTP_200
res = math.exp(pred)-1
if res = .9):
prob = .9
multiplier = self.low + (self.high -self.low)*prob
pred = multiplier*pred
resp.body = str(pred)
cors = CORS(allow_all_origins=True,allow_all_methods=True,allow_all_headers=True)
wsgi_app = api = falcon.API(middleware=[cors.middleware])
f = open('publishers1.list')
for domain in f:
domain = domain.strip()
p = Predictor(domain)
url = '/predict/' + domain
api.add_route(url, p)
```

在简要介绍了 Python 中的设计模式之后，现在让我们来看看一些对数据科学家至关重要的架构模式。

## 选择合适的数据库

在结束之前，我们将为管理者留下一份关于哪种数据库最适合哪种情况的说明。

- 当数据结构化程度高且实体间存在清晰严格的关系时，关系型数据库（`MySQL`、`Oracle`、`SQL Server`）是首选。另一方面，当数据非结构化且无组织时，`Mongo` 是更好的选择。
- 当数据包含较长的文本字段，并且您需要在该文本字段的子字符串中执行大量搜索时，`Elastic Search` 或 `Solr` 是更好的选择。使用 `Elastic Search`，您可以免费获得名为 `Kibana` 的数据可视化工具以及名为 `Logstash` 的 ETL 工具，全栈数据分析解决方案非常流行。
- 数据有时必须以图形形式表示。在这种情况下，需要使用图数据库。`Neo4j` 是一种流行的图数据库，它以较低的价格提供了许多实用工具。
- 我们偶尔需要一个快速的应用程序。在这种情况下，可以使用像 `SQLite` 这样的内存数据库。但是，`SQLite` 不支持从远程主机更新数据库。

您将在第 2 章中了解更多关于数据库的知识。

## 总结

在本章中，我们讨论了数据科学家的基本工程原则，这些原则将在后续各章中分别介绍。问答示例可以帮助您理解如何组织代码。基本规则是不要将所有内容都放入一个类中。将代码划分为多个类别，并在存在父子关系的地方使用它们。然后，您学习了如何使用 Python 调用其他语言的代码。我们提供了两个调用 `R` 和 `Java` 代码的实例。接着，我们展示了如何将模型作为 REST API 暴露，并通过使用并发编程使其性能良好。之后，我们介绍了数据科学家的重要架构模式。