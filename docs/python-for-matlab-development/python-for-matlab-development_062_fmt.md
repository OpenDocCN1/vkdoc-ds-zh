# A = np.array(struct.unpack(f'{N*N}d', Bytes))

A = A.reshape(N,N)

print(f'received {N} x {N} from client')

inv = np.linalg.inv(A)

conn.send(inv.tobytes()) # 发送 inv(A)

print(f'sent inverse to client')

except KeyboardInterrupt:

break

conn.close()

if __name__ == "__main__": main()

```

### 7.17.3.3 MATLAB 客户端

MATLAB 客户端相对直接；唯一的陷阱是矩阵维度 `N` 必须强制转换为 Python 服务器期望的数据类型，即 64 位整数。如果不进行类型转换，`N` 将具有 MATLAB 默认的 “double” 类型，Python 接收后会将其视为一个巨大的、不正确的整数：

MATLAB 2020b:

```matlab

% file: code/IO/tcp/client.m

Host = 'localhost';

Port = 5006;

Socket = tcpclient(Host,Port);

for i = 1:10

N = int64(randi([3,10])); % 类型转换很重要！

A = rand(N);

write(Socket, N);

fprintf('sending A (%2d x %2d) ', N,N)

write(Socket, A(:) );

Ainv = read(Socket, N*N, 'double');

Ainv = reshape(Ainv, N,N);

err = max(eye(N) - A*Ainv, [], 'all');

fprintf('error = %e\n', err)

end

```

MATLAB 控制台中显示的一次典型运行（涉及 Python 服务器和 MATLAB 客户端）的输出为：

MATLAB:

```text

>> client

sending A ( 6 x  6)  error = 4.440892e-15

sending A ( 5 x  5)  error = 4.440892e-16

sending A ( 6 x  6)  error = 5.551115e-16

sending A ( 9 x  9)  error = 5.771870e-15

sending A (10 x 10)  error = 1.422013e-15

sending A (10 x 10)  error = 1.792961e-15

sending A ( 6 x  6)  error = 1.998401e-15

sending A ( 3 x  3)  error = 1.332268e-15

sending A ( 6 x  6)  error = 1.776357e-15

sending A ( 5 x  5)  error = 6.106227e-16

```

### 7.17.3.4 MATLAB 服务器

在 MATLAB 中创建 TCP 服务需要使用 Instrument Control Toolbox 或 Python 的 `socket` 模块。下一个配方展示了如何利用 MATLAB 中的 `py` 模块来使 `socket` 的服务器功能可用。

## 7.18 配方 7-7：TCP 服务器

Instrument Control Toolbox 函数 `tcpip()` 可以创建 TCP 服务。如果没有此工具箱，纯 MATLAB 程序只能配置为客户端。不过，我们可以编写一个混合 MATLAB/Python 程序，在 MATLAB 中使用 Python 的 `socket` 模块来创建 TCP 服务。

除了 `socket`，我们还将重用在第 7.17.3.1 节中为 Python 客户端编写的 `wait_for_nBytes()` 函数。我们通过将当前目录添加到 Python 路径，然后将 `client.py` 作为 MATLAB 变量 `pyclient` 导入来实现这一点。该服务器执行的操作与第 7.17.3.2 节的 Python 矩阵求逆服务器相同，并且可以与第 7.17.3.1 节和第 7.17.3.3 节中所示的 Python 或 MATLAB 客户端配合工作。

MATLAB 2020b:

```matlab

% code/IO/tcp/server.m

import py.list.*

import py.sys.path

append(py.sys.path, '.')

pyclient = py.importlib.import_module('client');

AF_INET = py.socket.AF_INET;

SOCK_STREAM = py.socket.SOCK_STREAM;

s = py.socket.socket(AF_INET,SOCK_STREAM);

addr = py.tuple([{"localhost"}, int64(5006)]); % 主机，端口

s.bind(addr)

s.listen(int64(1))

conn_addr = s.accept();

conn = conn_addr{1};

while 1

bytes = pyclient.wait_for_nBytes(int64(8), conn);

N = typecast(uint8(bytes), 'int64');

if isempty(N)

break

end

bytes = pyclient.wait_for_nBytes(int64(8*N*N), conn);

A = typecast(uint8(bytes), 'double');

A = reshape(A, N,N);

Ainv = inv(A);

bytes = typecast(Ainv(:), 'uint8');

conn.send(bytes);

end

conn.close()

```

细心的读者可能会疑惑，Python 服务器如何能同时正确地与 Python 客户端和 MATLAB 客户端协作，因为这两个客户端存储和发送矩阵项的方式不同：Python 客户端按行主序发送，而 MATLAB 客户端按列主序发送。如果两个客户端发送相同的矩阵，服务器将从 Python 客户端得到原始矩阵，从 MATLAB 客户端得到该矩阵的转置。

然而，在返回过程中，Python 服务器和 MATLAB 客户端之间也会发生转置。Python 按行主序发回逆矩阵，而 MATLAB 则按列主序获取这些项并填充其 `Ainv`。MATLAB 最终得到正确的逆矩阵，这得益于恒等式

*A*^(-1) ≡ [*A*^(-*T*)]^(*T*)

换句话说，MATLAB 得到的是 `inv(A')'`，它与 `inv(A)` 相同。

显然，并非所有处理多维矩阵的跨语言客户端/服务器应用程序都会有如此巧合的设置。理解矩阵的存储顺序对于编写正确的客户端/服务器应用程序至关重要。

## 7.19 与数据库交互

几乎每个现有的数据库引擎似乎都有对应的 Python 模块。在这里，我们将展示 PostgreSQL 和 MongoDB 的示例。（所有 Python 安装都支持 SQLite；与它的交互在第 7.11 节中介绍。）

### 7.19.1 PostgreSQL

Python 模块 `psycopg2` 可用于与 PostgreSQL 交互。该模块可从 Anaconda 仓库获取：

```text

> conda install psycopg2

```

在运行 Python 代码操作 PostgreSQL 数据库之前，我们需要在 PostgreSQL 服务器上拥有一个账户和一个数据库。^((30)) 如果你能访问 PostgreSQL 实例，请登录数据库账户（通常是 `postgres` 用户），启动客户端程序，然后用如下命令创建账户和数据库：

```text

> psql

postgres=# create user "Al" with login password "SuperSecret";

postgres=# create database pet_store;

postgres=# grant all privileges on database pet_store TO "Al";

postgres=# quit

```

通过下面这个小型 Python 程序，可以确认数据库引擎正在运行，并且用户和数据库可以访问：

```python

#!/usr/bin/env python3

import psycopg2

conn = psycopg2.connect(host="localhost",

user="Al", password="SuperSecret",

database="pet_store")

conn.close()
```

## 常见错误

如果程序无报错运行，则一切正常。如果出现错误，很可能是以下三种之一：

1.  `psycopg2.OperationalError: could not connect to server: Connection refused`
2.  `psycopg2.OperationalError: FATAL: password authentication failed for user Al`
3.  `psycopg2.OperationalError: FATAL: database pet_store does not exist`

第一种错误意味着数据库服务已停止，或存在网络问题（例如防火墙）阻止访问。第二种和第三种错误表明数据库服务可达，但你要使用的账户或数据库要么不存在，要么未正确配置。你需要或你的数据库管理员需要重新检查设置。

## CRUD 示例

下面的程序演示了 Python 中的增、改、读、删操作。在创建步骤中，我们使用 `psycopg2` 的 `executemany()` 函数来高效处理批量插入。此代码示例以 3000 条为一批，插入了 10000 条记录：

```python
#!/usr/bin/env python3
# code/IO/postgres_crud.py
import psycopg2
import random
import time

def record():
    """返回一个随机整数、浮点数和宠物类型。"""
    n = random.randint(1,15)
    price = 10.0 + 30*random.random()
    pet = random.choice(['dog', 'cat',
            'bunny', 'fish', 'horse'])
    return (n, price, pet)

def populate_table(conn, cur):
    T_start = time.time()
    cur.execute("""create table myvalues (id serial primary key,
                n integer, price float, pet varchar)""")
    batch_size = 3_000
    n_records = 10_000
    batch = []
    insert = "insert into myvalues (n,price,pet) values(%s,%s,%s)"
    for i in range(n_records):
        n,price,pet = record()
        batch.append((n, price, pet))
        if len(batch) % batch_size == 0 or i == n_records - 1:
            cur.executemany(insert, batch)
            conn.commit()
            batch.clear()
    print(f'populate_table耗时: {time.time() - T_start:.4f}s')

def query_results(cur):
    cur.execute('select n, price, pet from myvalues limit 10')
    rows = cur.fetchall()
    print('n\tprice\tpet')
    for row in rows:
        print(f'{row[0]}\t{row[1]:.2f}\t{row[2]}')
    print('-' * 60)

def rename_pets(cursor, before, after):
    update_cmd = f"update myvalues set pet='{after}' where pet='{before}'"
    cursor.execute(update_cmd)
    print(f'已执行: {update_cmd}')

def remove_pets(cursor, pet):
    delete_cmd = f"delete from myvalues where pet='{pet}'"
    cursor.execute(delete_cmd)
    print(f'已执行: {delete_cmd}')

def main():
    try:
        conn = psycopg2.connect( host="413.198.155.245",
            user="al", password="SuperSecret",
            database="pet_store")
    except psycopg2.OperationalError as e:
        print(f'连接失败: {e}')
        return
    cur = conn.cursor()
    # 如果表 'myvalues' 已存在，则删除它
    try:
        cur.execute('drop table myvalues')
    except:
        pass
    populate_table(conn, cur)
    query_results(cur)
    rename_pets(cur, 'bunny', 'rabbit')
    query_results(cur)
    remove_pets(cur, 'cat')
    query_results(cur)
    cur.execute('drop table myvalues')
    conn.close()

if __name__ == "__main__": main()
```

## MongoDB 示例

Python 模块 `pymongo` 可用于与 MongoDB 交互。该模块同样可从 Anaconda 仓库获取：

```
conda install pymongo
```

我们的示例使用附录 B 中描述的 `faker` 模块，生成了 10000 个包含假数据的采购订单，并填充到 MongoDB 数据库中。这些假采购订单包含订单 ID、订单日期、客户姓名和电话号码、所购物品列表以及订单总价。以下是一个典型条目：

```python
{'customer_name': 'Paul Reynolds',
'customer_phone': '418.583.4214x467',
'order_number': 'ba9b18f5-301a-4f75-8d44-587c3efca6ba',
'order': [{'item': 'glove', 'quantity': 3},
{'item': 'bike', 'quantity': 6},
{'item': 'golf club', 'quantity': 6},
{'item': 'glove', 'quantity': 7}],
'total_price': 1450.0,
'order_date': datetime.datetime(2021, 4, 6, 6, 51, 7)}
```

订单创建逻辑并不完美，因为一个包含多个物品的订单中，同一物品可能出现重复条目——如前面手套所示。

需要运行 MongoDB 服务器实例；此处的代码预期它在本地机器上运行，并使用默认端口 27017：

Python:

```python
#!/usr/bin/env python
# code/fake_data/mongo_fake_PO.py
from pymongo import MongoClient
from urllib.parse import quote_plus
from faker import Faker
from datetime import datetime
import time

fake = Faker()
Faker.seed(123) # 为了可复现性
start_date = datetime(1997, 1, 31, 12, 0, 0)
end_date = datetime(2025, 1, 31, 12, 0, 0)

def start_new_order():
    PO = {}
    PO['customer_name'] = fake.name()
    PO['customer_phone'] = fake.phone_number()
    PO['order_number'] = fake.uuid4()
    PO['order_date'] = fake.date_time_between_dates(
                            start_date, end_date)
    return PO
```

# 使用 MongoDB 进行数据操作示例

`order_items` 函数根据价格字典 `price` 随机生成订单。它使用 `fake` 模块（可能是 `faker` 库）生成订单项目数量和具体商品，并计算总价格。`main` 函数模拟了向 MongoDB 数据库批量插入订单的过程。它根据 `local` 标志决定是连接本地还是远程的 MongoDB 实例，然后生成 `n_orders`（10,000）个订单并插入到 `purchase_orders` 集合中。最后打印所用时间。

```python
def order_items(price):
    n_items = fake.random_int(min=1,max=len(price))
    order = []
    total_price = 0
    for i in range(n_items):
        item = fake.random_element(price.keys())
        quantity = fake.random_int(min=1,max=8)
        order.append({'item' : item, 'quantity' : quantity})
        total_price += price[item]*quantity
    return order, total_price

def main():
    T_start = time.time()
    n_orders = 10_000
    price = { 'bike' : 80.00, 'golf club' : 120.00,
              'ball' : 4.50, 'glove' : 25.00, 'hat' : 8.25}
    local = True
    if local:
        client = MongoClient('localhost:27017')
    else:
        uri = "mongodb://%s:%s@%s/purchase_orders" % (
                quote_plus('al'),
                quote_plus('SuperSecret'),
                quote_plus('413.198.231.158:27027'))
        client = MongoClient(uri)
    db = client.purchase_orders
    all_PO = []
    for i in range(n_orders):
        PO = start_new_order()
        PO['order'], PO['total_price'] = order_items(price)
        all_PO.append( PO )
    db.purchase_orders.insert_many( all_PO )
    print(f'在 {time.time()-T_start:.3f} 秒内添加了 {n_orders} 个订单')

if __name__ == "__main__": main()
```

我们可以使用 MongoDB 客户端程序查询数据库，例如查找最贵的订单：

```
mongo --port 27017 purchase_orders
db.purchase_orders.aggregate([ { "$group": {
"_id" : null, "Maxl": { "$max": "$total_price"} } } ]);
{ "_id" : null, "Max" : 1698 }
db.purchase_orders.find({"total_price" : {"$eq":1698} }).pretty();
{
"_id" : ObjectId("607b78452c748f4aacbdc34a"),
"customer_name" : "Jeffery Hampton",
"customer_phone" : "640.474.9845x311",
"order_number" : "3d5d8f38-c97f-4215-b425-ffda9234554b",
"order" : [ { "item" : "ball", "quantity" : 4 },
{ "item" : "golf club", "quantity" : 8 },
{ "item" : "golf club", "quantity" : 6 }
],
"total_price" : 1698,
"order_date" : ISODate("2021-01-21T13:10:02Z")
}
exit
bye
```

## 配方 7-8：使用 PostgreSQL 数据库进行 CRUD 操作

MATLAB 的 `py` 模块提供了对 `psycopg2` 的无缝访问，用于操作 PostgreSQL。

以下 MATLAB 程序重复了第 7.19.1 节的纯 Python 程序。唯一值得注意的方面是：(1) 保存待插入行集合的 `batch` 变量必须是一个 Python 容器，因为 Python 模块 `psycopg2` 需要这样；(2) 查询操作返回一个 Python 元组列表，该列表通过 `py2mat()` 转换为嵌套的 MATLAB 元胞数组。

MATLAB：

```matlab
% code/matlab_py/postgres_crud.m
Im = @py.importlib.import_module;
psycopg2 = Im('psycopg2');
conn = psycopg2.connect(pyargs('host','413.198.155.245', ...
    'user','al','password','SuperSecret', ...
    'database','pet_store'));
cur = conn.cursor();

% 如果表已存在则删除
try
    cur.execute('drop table myvalues');
    conn.commit();
catch EO
    fprintf('表 "myvalues" 不存在，跳过删除操作。\n')
end

populate_table(conn, cur);
query_results(cur);
rename_pets(cur, 'bunny', 'rabbit');
query_results(cur);
remove_pets(cur, 'cat');
query_results(cur);
cur.execute('drop table myvalues');
conn.close();

function [n, price, pet] = record()
    n = int64(randi([1,15]));
    price = 10.0 + 30*rand();
    pets = {'dog', 'cat','bunny', 'fish', 'horse'};
    pet = pets{randi([1,5])};
end

function populate_table(conn, cur)
    tic;
    cur.execute(['create table myvalues (id serial primary key, ' ...
        'n integer, price float, pet varchar)']);
    batch_size = 300;
    n_records = 1000;
    batch = py.list();
    insert = 'insert into myvalues (n,price,pet) values(%s,%s,%s)';
    for i = 1:n_records
        [n,price,pet] = record();
        batch.append(py.list({n, price, pet}));
        if ~(mod(i,100))
            fprintf('i = %5d\r', i);
        end
        if length(batch) < batch_size
            continue
        end
        fprintf('i = %5d 正在插入批次\n', i);
        cur.executemany(insert, batch)
        conn.commit()
        batch = py.list();
    end
    if ~isempty(batch)
        cur.executemany(insert, batch)
        conn.commit()
    end
    fprintf('在 %.3f 秒内插入了 %d 行\n', toc, n_records)
end

function query_results(cursor)
    query = 'select sum(price), sum(n), pet from myvalues group by pet';
    cursor.execute(query);
    fprintf('查询结果 %s:\n', query)
```

```
all = py2mat(cursor.fetchall());

for i = 1:length(all)

sum_price = all{i}{1};

sum_n     = all{i}{2};

pet       = all{i}{3};

fprintf(' %.2f %6d %7s\n', sum_price, sum_n, pet)

end

fprintf(repmat('-',1,60)); fprintf('\n')

end

function rename_pets(cursor, before, after)

update_cmd = sprintf("update myvalues set pet='%s' where pet='%s'",...

after, before);

cursor.execute(update_cmd);

fprintf('已执行：%s\n', update_cmd)

end

function remove_pets(cursor, pet)

delete_cmd = sprintf("delete from myvalues where pet='%s'",pet);

cursor.execute(delete_cmd)

fprintf('已执行：%s\n', delete_cmd)

end

```

## 7.21 配方 7-9：使用 MongoDB 数据库进行 CRUD 操作

与`psycopg2`不同，Python MongoDB 接口模块`pymongo`的数据库对象并未在 MATLAB 中暴露其所有方法。我们需要一个小型桥接模块来解决这个问题：

Python：

```