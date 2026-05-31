# 6. 利用后端服务器

在本章中，我们将介绍一种更复杂、更具可扩展性的 Web 应用程序设计方法。具体来说，我们将探讨如何将管理数据库的开销从 Streamlit 服务器卸载到独立的后端服务器，这也是全栈环境中的典型做法。本章将引导开发者完成在 Python 中配置后端服务器的过程，该服务器将充当前端与数据库之间的中介。最后，开发者将了解一种高度模块化、多功能且安全的架构，该架构在应用程序和数据库之间增加了安全层。



## 6.1 后端服务器的必要性

在构建可扩展且稳健的 `Streamlit` 应用时，某些在 `Streamlit` 应用内执行的任务，更适合在一个易于通信的隔离环境中处理。这个环境被称为后端，它负责管理认证、授权、数据库及其他网关连接。此外，它还处理那些不应该在前端（即 `Streamlit`）上执行的核心业务逻辑。虽然 `Streamlit` 本身是一个服务端 Web 框架，但将其与其他系统组件隔离，能够提高模块化和安全性。如果从身份验证到数据库管理的一切都直接在 `Streamlit` 中运行，而未妥善设计，可能会引入多种安全风险，例如 XSS、SSRF，甚至潜在的 RCE。

尽管前后端架构并非无懈可击，但它通过使用 API 增加了额外的保护层。这使得恶意行为者更难绕过安全措施来访问受保护的系统组件。现代的 API 设计方法论还能确保每个请求都通过一致的机制进行路由，从而减少因人为错误导致漏洞的可能性。

## 6.2 前后端通信

后端通常被称为服务器，而前端则被称为客户端。在这种架构中，客户端根据用户的操作触发对资源或信息的请求。然后，服务器响应并返回请求的数据。这种请求-响应通信模型是 HTTP 协议的基础，它由两个主要部分组成：标头（header）和主体（body）。

请求和响应的标头包含关于请求以及主体中有效载荷的元数据。这些元数据包括但不限于：Cookie、请求标识符、密钥、令牌、主体的内容类型、服务器的主机或 IP 地址，以及数据的编码或压缩机制。基于本书的定位，我们将重点关注关键方面，如密钥、令牌、Cookie 和主体的内容类型。例如，请求主体类型如表 6-1 所示。由于后端主要处理信息的发送和接收，JSON 是最广泛使用的通信格式。

**表 6-1** 常见的 Content-Type 标头值

| Content-Type 值 | 描述 |
| --- | --- |
| `text/HTML` | 文本格式，但可解析为 HTML 以便渲染成网页 |
| `application/JSON` | JSON 格式 |
| `application/xml` | XML 格式 |
| `image/png` | PNG 类型的图像二进制数据 |

### 6.2.1 HTTP 方法

如第 5.1 节所述，HTTP 请求带有方法。与 SQL 不同，HTTP 方法的语法变化不大。主要的方法包括 `GET`、`POST`、`PUT` 和 `DELETE`，分别对应于检索、添加、修改和删除一个资源。添加和修改资源可以包含一个主体来更改资源，而 `GET` 和 `DELETE` 通常使用请求 URL 中的资源标识符。这些是 RESTful API 的核心功能，它作为 HTTP 网关来管理服务器端的资源或数据。

访问一个网站，本质上就是向该 URL 发送一个 `GET` 请求，该请求在其标头、主体和响应码中包含了与请求相关的特定数据。例如，仅使用域名请求一个页面时，默认会请求其 HTTP 版本，但后端服务器可能会重定向到 HTTPS 版本。例如，谷歌会从 `google.com` 重定向到 `https://google.com`。你可以通过检查页面的网络流量或使用 `Curl` 来验证这一点。`Curl` 是一个用于从终端或 CMD 发起 HTTP 请求的工具，允许你查看响应主体、标头和状态码。它还有助于理解后端命令，例如跟随重定向。图 6-1 通过命令 `curl -i -L google.com` 演示了这一点，其中 `-i` 用于显示响应标头，`-L` 用于跟随重定向。

![一个命令提示符窗口，显示使用 curl 命令成功向 www.google.com 发起 HTTP 请求。响应包含 HTTP 状态 200 OK、日期和时间、内容类型、缓存控制以及各种安全标头。文本显示在黑色背景上的绿色字符，这是命令行界面的典型风格。](img/523242_2_En_6_Fig1_HTML.jpg)

**图 6-1** 获取 `google.com` 并观察响应标头和重定向过程

## 6.3 使用 JSON 文件

JSON 文档可用于表示和解析数据。它们可以包含列表/数组或键/值对，类似于 Python 中的字典。它们还可以包含其他原始数据类型，例如整数、浮点数、字符串和布尔值。通过这些简单的数据类型，可以表示复杂的数据结构。清单 6-1 展示了一个示例 JSON，它表示之前介绍的一个例子。

```json
[
{
"Name": "Adam",
"DOB": "01/01/1990",
"Paygrade ID": "2"
},
{
"Name": "Sara",
"DOB": "01/01/1980",
"Paygrade ID": "1"
},
{
"Name": "Bob",
"DOB": "01/01/1970",
"Paygrade ID": "1"
},
{
"Name": "Alice",
"DOB": "01/01/2000",
"Paygrade ID": "3"
}
]
```

**清单 6-1** `sample_json.json`

## 6.4 配置后端服务器

为了与本书的 Python 主题保持一致，我们将构建一个 Python 风格的后端服务器。在众多可选方案中，`Flask` 和 `Django` 都是完成此任务的绝佳选择。虽然两者都可以通过提供供浏览器渲染的 HTML 来充当前端应用服务器，但 `Django` 正是为此目的而设计的，这归功于其内置的 Web 模板引擎 `Jinja`。另一方面，`Flask` 更加灵活且可根据开发者的需求进行配置，并且重量更轻。要开始使用，请使用 `pip` 安装 `Flask`。



### 6.4.1 API 构建

后端会执行一个或多个方法或函数，以响应发往服务器的请求。这些方法和函数依赖于 URL，并且可以配置为同时考虑请求头。清单 6-2 展示了一个简单的例子：请求 `http://<SERVER-HOST>/server_status` 会返回图 6-2 中的页面，而任何其他请求则会返回一个 *404 Not Found* 错误。

![网页浏览器截图，显示在 URL "http:/127.0.0.1:5000/server_status" 上来自 Flask 服务器的 JSON 响应。该 JSON 数据包含三个键值对："message" 的值为 "Welcome to your Flask Server"，"random" 的值为 2，"status" 的值为 "up"。"Pretty-print" 选项已勾选。](img/523242_2_En_6_Fig2_HTML.jpg)

图 6-2

当在运行清单 6-2 的服务器上请求 `/server_status` 路由时返回的页面

```
from flask import Flask
app = Flask(__name__)
@app.route('/server_status')
def welcome_controller():
return {
'message': 'Welcome to your Flask Server',
'status': 'up',
'random': 1 + 1
}
app.run()
清单 6-2
flask_sample.py
```

为了在请求某个路由时触发特定的函数调用，需要在该函数之前添加一个包含路由名称的函数装饰器。路由不必是静态的；通过使用特定的字符串格式可以使它们成为动态的，Flask 会将这些动态路由映射到相应的函数。例如，`/text/1` 和 `/text/3` 可以映射到 `/text/<id>`。此外，可以通过在同一个函数装饰器中添加一个额外的参数来指定 HTTP 方法，如下所示：

```
@app.route('/text/', methods=['GET', 'PUT'])
```

接着前面关于员工和薪资等级的例子，我们将在本例中重用同一个数据库，但使用 *SQLAlchemy* 代替 *psycopg2*，以便利用 *ORM*（对象关系映射）将 SQL 命令映射到 Python 类对象。首先，我们需要将表表示为类，以及一个 Base 类，另外两个类将从该类继承 SQL 属性。这些属性包括 SQL 查询参数化，以防止 SQL 注入。清单 6-4 和 6-5 中的类将指向数据库中已有的表，其中 `__tablename__` 属性代表表名。

```
from sqlalchemy import Column, Integer, String
from DataBase.Base import Base
class Employees(Base):
    __tablename__ = 'persons'
    id = Column(Integer, primary_key=True)
    name = Column(String)
    date_of_birth = Column(String, default=True)
    paygrade_id = Column(Integer, unique=True, index=True)
    def to_dict(self):
        return {
            "id": self.id,
            "name": self.name,
            "date_of_birth": self.date_of_birth,
            "paygrade_id": self.paygrade_id
        }
清单 6-5
DataBase/Employees.py
```

```
from sqlalchemy import Column, Integer, String
from DataBase.Base import Base
class PayGrades(Base):
    __tablename__ = 'paygrades'
    id = Column(Integer, primary_key=True)
    base_salary = Column(String)
    reimbursement = Column(String, default=True)
    bonuses = Column(String)
    def to_dict(self):
        return {
            "id": self.id,
            "base_salary": self.base_salary,
            "reimbursement": self.reimbursement,
            "bonuses": self.bonuses
        }
清单 6-4
DataBase/PayGrades.py
```

```
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base()
清单 6-3
DataBase/Base.py
```

然后，我们可以使用 Flask 通过 HTTP 请求来支持添加和检索员工数据，如清单 6-6、6-7 和 6-8 所示。这个后端服务器包含两个路由：第一个使用通过 SQLAlchemy 建立的数据库连接查询所有员工；第二个使用用户通过 HTTP 请求体（JSON 文档形式）提供的属性，向员工表中插入或添加新员工。

```
from flask import Flask, request
from DataBase import Employees
from DataBase.Connection import session_manager
app = Flask(__name__)
@app.route('/employees')
def get_all_employees():
    with session_manager() as session:
        employees = session.query(Employees).all()
        employees = [employee.to_dict() for employee in employees]
        return {"data": employees}
@app.route('/employee', methods=["POST"])
def add_employee():
    body = request.json
    with session_manager() as session:
        session.add(Employees(**body))
        session.commit()
        return {"message": "New employee added successfully"}
app.run()
清单 6-8
main.py
```

```
from contextlib import contextmanager
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, Session
engine = create_engine("postgresql://postgres:admin@127.0.0.1:5432/CompanyData")
DBSession = sessionmaker(bind=engine)
@contextmanager
def session_manager() -> Session:
    session = DBSession()
    try:
        yield session
    except Exception as e:
        session.rollback()
        raise e
    finally:
        session.close()
清单 6-7
DataBase/Connection.py
```

```
from .Employees import Employees
from .PayGrades import PayGrades
清单 6-6
DataBase/__init__.py
```

### 6.4.2 API 测试

在这个例子中，用户将使用像 Postman 这样的 API 测试平台与后端交互，如图 6-3 和图 6-4 所示。在后面的一节中，Streamlit 将直接与此服务器交互，无需 API 测试平台。

![Web 应用程序界面截图，显示向 URL "127.0.0.1:5000/employees" 发送 GET 请求后返回的 JSON 响应。该响应包含员工数据，字段包括 "date_of_birth"、"id"、"name" 和 "paygrade_id"。状态为 200 OK，响应时间为 22 毫秒，大小为 499 B。该界面显示了用于 Params、Authorization、Headers 等的选项卡，并且选择了 JSON 响应的 "Pretty" 视图。](img/523242_2_En_6_Fig4_HTML.jpg)

图 6-4

获取所有员工

![Postman 界面截图，显示向 "127.0.0.1:5000/employee" 发送 POST 请求。请求体包含 JSON 数据，字段为："name" 值为 "Karen" ，"date_of_birth" 值为 "01/01/1975"，"paygrade_id" 值为 2。响应部分显示一条消息："New employee added successfully"，状态码为 200 OK。](img/523242_2_En_6_Fig3_HTML.jpg)

图 6-3

添加新员工

最后，需要注意的是，我们没有为 `POST /employee` 路由设置任何请求头（包括使用 JSON 负载的内容类型），因为 Postman 会自动处理这些。Flask 也负责将 JSON 内容类型添加到响应中，因为正如前面提到的，Python 的列表和字典很容易解析为 JSON。



## 6.5 多线程与多进程请求

当应用程序扩展时，或当其初始需求涉及大量独立计算或处理过程时，无论是在后端 Flask 端还是 Streamlit 端，都会浪费大量算力。这是因为现代 CPU 的完整性能并未得到充分利用。现代 CPU 的强大之处不在于更快的时钟频率，而在于拥有更多核心。默认情况下，Streamlit 和 Flask 都是单线程、单进程的应用程序。为了加速它们，我们可以利用*多进程*和*多线程*技术，从而实现真正的并行化。开发者可以控制使用这两种技术来多次运行某个函数，使其能被 CPU 并行执行。清单 6-9 展示了在 Streamlit 中使用这两种方法的示例，如图 6-5 所示。

```
import streamlit as st
from multiprocessing import Pool, cpu_count
import threading
import time
def func(iterations, id):
i = 0
for i in range(iterations):
i += 1
print('Finished job id =', id)
if __name__ == '__main__':
pool = Pool(cpu_count())
st.title('Speed You Code! ')
jobs_count = 5
iterations = 10 ** 3
c1, c2 = st.columns(2)
with c1:
if st.button('multiprocess'):
inputs = [(iterations, i) for i in range(jobs_count)]
t11 = time.time()
pool.starmap(func, inputs)
t21 = time.time()
st.write(f'Finished after {t21 - t11} seconds')
with c2:
if st.button('multithread'):
threads = [threading.Thread(target=func, args=(iterations, i)) for i in range(10)]
t12 = time.time()
for thread in threads:
thread.start()
for thread in threads:
thread.join()
t22 = time.time()
st.write(f'Finished after {t22 - t12} seconds')
Listing 6-9
streamlit_main.py
```

![页面顶部显示文字 "Speed You Code!"，下方是两个分别标有 "multiprocess" 和 "multithread" 的按钮，底部显示文字 "Finished after 0.04653143882751465 seconds"](img/523242_2_En_6_Fig5_HTML.jpg)

**图 6-5** 清单 6-9 的输出

注意在清单 6-9 中，首先执行的代码在第 13 行之后，这对于整个示例无错运行至关重要。这确保了 Streamlit 知道该代码块只应执行一次，这意味着在重新运行时处理池不会被再次初始化。Flask 应用程序也应采取类似的预防措施。

多进程和多线程的主要区别在于，多线程重用现有的内存空间，在同一进程中生成新线程，这比生成全新进程（会增加 CPU 上下文切换的开销）更快。相比之下，多进程池中的每个新进程都需要独立的内存空间。此外，每个进程的输入都需要被复制或克隆，这会增加内存消耗。虽然多线程看起来是更优的选择，但事实并非总是如此。多进程在处理繁重任务时 CPU 效率更高，因为 CPU 调度程序会为这些进程分配更多时间。图 6-6 显示了在清单 6-9 中执行时间与任务迭代次数（以 10 倍递增）之间的相关性。

![图表显示了多进程和多线程的执行时间（秒）与迭代次数（10^x）的关系。红线代表多进程，蓝线代表多线程。两条线开始时执行时间都很低，但多线程在 7 次迭代后急剧上升，超过了多进程。](img/523242_2_En_6_Fig6_HTML.jpg)

**图 6-6** 随着迭代次数增加，五个任务的多进程与多线程对比

## 6.6 将 Streamlit 连接到后端服务器

一旦我们通过多进程和/或多线程优化了后端服务器，就可以将 Streamlit 应用程序连接到它了。为此，我们需要使用一个 HTTP 客户端库来与后端 API 通信。清单 6-10 使用了流行的 *requests* 库来实现这一点，其输出如图 6-7 所示。

![一个添加新员工的网页表单界面截图。表单包含 "Name"（姓名）、"DOB"（出生日期）和 "paygrade"（薪资等级）字段，以及一个 "Add new Employee"（添加新员工）按钮。表单下方，一个绿色框中显示确认消息 "Employee Added"（员工已添加）。一个表格列出了员工信息，列标题为 "date_of_birth"（出生日期）、"id"（编号）、"name"（姓名）和 "paygrade_id"（薪资等级编号）。表格中有六条记录，最后一条显示新添加的员工，姓名为 "Ahmed"，出生日期为 "1985-01-01"，薪资等级编号为 "1"。](img/523242_2_En_6_Fig7_HTML.jpg)

**图 6-7** 清单 6-10 的输出

```
import streamlit as st
import requests
import datetime
url = 'http://127.0.0.1:5000'
def add_employee(name, dob, paygrade):
data = {
'name': name,
'date_of_birth': dob,
'paygrade_id': paygrade
}
response = requests.post(url + '/employee', json=data)
if response.status_code == 200:
return True
return False
def get_employees():
response = requests.get(url + '/employees')
return response.json()['data']
form = st.form('new_employee')
name = form.text_input('Name')
dob = str(form.date_input('DOB', min_value=datetime.datetime(year=1920, day=1, month=1)))
paygrade = form.number_input('paygrade', step=1)
if form.form_submit_button('Add new Employee'):
if add_employee(name, dob, paygrade):
st.success('Employee Added')
else:
st.error('Error adding employee')
st.write('___')
employees = get_employees()
st.table(employees)
Listing 6-10
streamlit_api.py
```

## 6.7 总结

在本章中，我们了解到后端服务器是每个不断扩展的 Web 应用程序的支柱，它为前端增加了层层的模糊性和安全性。我们还探讨了 HTTP 通信（即 API 的语言）及其结构。为了确保高效且有组织的 API 通信，我们介绍了 JSON（API 中使用最广泛的数据格式）。有了这些基础构建块，我们接着将 Flask 作为 Python 后端框架，创建了一个结构良好的服务器，它暴露特定的端点以从本地数据库提供数据。最后，我们演示了通过利用多进程和多线程技术，在 Streamlit 和后端服务器中加速 Python 代码执行的方法，并比较了每种技术的性能和使用场景。

