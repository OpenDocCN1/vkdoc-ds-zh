# code/matlab_py/bridge_mongo.py

def insert_many(db, database, document):

db[database].insert_many(document)

def aggregate(db, database, pipeline):

result = []

for x in db[database].aggregate(pipeline):

result.append(x)

return result

def find_one(db, database, kwargs):

x = db[database].find_one(**kwargs)

return x

def delete_many(db, database, kwargs):

x = db[database].delete_many(**kwargs)

return x

def drop(client, database):

client.drop_database(database)

def update_one(client, database):

client.update_one(database)

```

以下代码重复了第 7.19.2 节的 Python 采购订单示例。与 Python 版本一样，此 MATLAB 程序创建一个名为 `purchase_orders` 的数据库，然后向其中填入 10000 个模拟订单。除了 `bridge_mongo.py` 之外，此 MATLAB 程序还导入了一个 Python 程序 `mongo_fake_PO.py`，以便调用其 `start_new_order()` 函数。

MATLAB 2020b：

```

1   % code/matlab_py/mongo_fake_PO.m

2   Im = @py.importlib.import_module;

3   pymongo = Im('pymongo');

4   fake_PO = Im('mongo_fake_PO');

5   bridge_mongo = Im('bridge_mongo');

7   tic;

8   price.bike       = 80.00;

9   price.golf_club  = 120.00;

10   price.ball       = 4.50;

11   price.glove      = 25.00;

12   price.hat        = 8.25;

13   price_items = fieldnames(price);

15   local = true;

17   if local

18       client = pymongo.MongoClient('localhost:27017');

19   else

20       db_user = 'al';

21       db_passwd = 'SuperSecret';

22       db_host = '413.198.231.158';

23       db_port = 27027;

24       uri = sprintf("mongodb://%s:%s@%s:%d/purchase_orders", ...

25              db_user, db_passwd, db_host, db_port);

26       client = pymongo.MongoClient(uri);

27   end

28   db = client.get_database('purchase_orders');

29   n_orders = 10000;

30   all_PO = py.list();

31   for i = 1:n_orders

32       PO = fake_PO.start_new_order();

33       [order, total_price] = order_items(price, price_items);

34       PO.update(pyargs('order', order));

35       PO.update(pyargs('total_price', total_price));

36       all_PO.append( PO );

37   end

38   bridge_mongo.insert_many( db, 'purchase_orders', all_PO );

39   fprintf('在 %.3f 秒内添加了 %d 个订单\n', toc, n_orders)

41   function [order, total_price] = order_items(price, price_items)

42       n_prices = length(price_items);

43       n_items = randi(n_prices,1,1);

44       order = py.list();

45       total_price = 0;

46       for i = 1:n_items

47           i_item = randi(n_prices,1,1);

48           item = price_items{i_item};

49           quantity = randi(8,1,1);

50           this_order = py.dict(pyargs('item',item,'quantity',quantity));

51           order.append(this_order);

52           total_price = total_price + price.(item)*quantity;

53       end

54   end

```

插入性能比纯 Python 解决方案慢大约 2 倍：

```

>> mongo_fake_PO

在 23.459 秒内添加了 10000 个订单

```

### 7.21.1 读取操作

在这里，我们获取总成本最大的文档。我们不执行 `$max` 聚合，而是简单地在按成本降序排序后返回第一个文档。

MATLAB 2020b：

```

>> total_price =  py.tuple({"total_price", pymongo.DESCENDING});

>> total_price =  py.list( { total_price } )

>> sort_on_price = py.dict(pyargs('sort',total_price))

>> x = bridge_mongo.find_one(db,'purchase_orders',sort_on_price)

x =

Python dict with no properties .

{'_id': ObjectId('607cb7fb705a307702b591a0'), 'customer_name':

'John Ford', 'customer_phone': '971-016-8700x4084', 'order_number':

'7bd40678-3d1c-4381-959c-d35f87f74e8a', 'order_date':

datetime.datetime(2021, 3, 28, 20, 16, 4), 'order': [

{'item': 'bike', 'quantity': 8.0},

{'item': 'golf_club', 'quantity': 7.0},

{'item': 'hat', 'quantity': 4.0},

{'item': 'golf_club', 'quantity': 8.0},

{'item': 'golf_club', 'quantity': 8.0}], 'total_price': 3433.0}

```

变量 `total_price` 和 `sort_on_price` 是 Python 原生 MATLAB 变量，用于构建一个在 Python 程序中看起来像这样的表达式：

Python：

```

db.purchase_order.find_one({'sort': [('total_price', pymongo.DESCENDING)]})

```

返回的变量 `x` 是 Python 原生的，因此在 MATLAB 中不能直接使用。如果不是因为 `_id` 键，我们可以使用 `struct()` 函数将 `x` 转换为 MATLAB 结构体。然而，结构体字段名必须是合法的 MATLAB 变量，而前导下划线违反了该规则。我们需要删除此键或使用 `py2mat()` 函数（附录 D）。这里我们采用后者：

MATLAB 2020b：

```

>> y = py2mat(x)
```

K000001__id: [1×1 py.bson.objectid.ObjectId]

customer_name: "John Ford"

customer_phone: "971-016-8700x4084"

order_number: "7bd40678-3d1c-4381-959c-d35f87f74e8a"

order_date: 28-Mar-2021 20:16:04

order: {[1×1 struct]  [1×1 struct]  [1×1 struct]

[1×1 struct]  [1×1 struct]}

total_price: 3433

>> y.order{2}

item: "golf_club"

quantity: 7

### 7.21.2 更新

假设我们的大客户约翰·福特（`John Ford`）用一个新的电话号码更新了他的账户信息。在 MongoDB 中，数据库的更新操作如下所示：

**MATLAB 2020b**：

```
> mongo purchase_order
> do findOne based on name
> db.purchase_orders.updateOne({'customer_name':'John Ford'},
{'$set': {"customer_phone":"abc"}})
{ "acknowledged" : true, "matchedCount" : 0, "modifiedCount" : 0 }
```

在 MATLAB 中实现如下：

**MATLAB 2020b**：

```matlab
>> name = "John Ford";
>> new_phone = "971-255-9876";
>> phone_dict = py.dict(pyargs("customer_phone", new_phone));
>> pipeline = py.dict(pyargs("$set", phone_dict));
>> bridge_mongo.update_one(db,'purchase_orders',pipeline);
```

### 7.21.3 删除

整个 `purchase_orders` 数据库可以通过以下方式删除：

**MATLAB 2020b**：

```matlab
>> bridge_mongo.drop(client,'purchase_orders')
```

## 7.22 方案 7-10：与 Redis 交互

`Redis`，即“远程字典服务器”，是一个基于网络的快速内存数据库，用于存储键/值对。除了高性能和存储结构化值的能力外，`Redis` 还实现了发布/订阅服务，以便应用程序可以收到其所订阅键值发生变更的通知。`MATLAB Production Server` 具有 `Redis` 接口。Python 也有；conda 仓库中既包含完整的 `Redis` 应用程序，也包含 Python 接口模块。可以通过以下命令安装它们：

```bash
conda install redis redis-py
```

如果你已经安装了 `Redis` 应用程序，则只需安装 `redis-py`。

以下示例程序是等效的 MATLAB 和 Python 程序，展示了以下功能：
*   使用主机名和端口值配置 `Redis` 连接
*   通过 `Redis` `ping` 检查是否能够连接到 `Redis` 服务器
*   调用 `mset` 函数一次设置多个键值
*   使用 `get` 检索键值
*   以编程方式设置 `Redis` 客户端连接以接收键空间事件的通知
*   订阅键空间变更
*   打印已发生变更的键的值
*   在与服务器的连接结束时干净地退出

要运行任一演示程序，您需要三个终端会话来执行以下操作：
1.  运行一个 `Redis` 服务器实例。
2.  运行一个 MATLAB 或 Python 演示程序实例。
3.  使用 `Redis` 命令行工具提交任意的 `set` 命令。

这三个终端会话可以在同一台计算机上，也可以在同一网络中的不同计算机上。如果您在不同的计算机上运行它们，则需要向演示程序和命令行工具显式提供运行服务器的主机名。

首先启动一个 `Redis` 服务器实例：

```bash
redis-server
```

默认情况下，服务器会期望客户端连接在 6379 端口上；例如，在上面的命令中添加 `--port 12345` 开关可以将端口值更改为 12345。

接下来，运行以下其中一个程序：

**MATLAB 2020b**：

```matlab
% code/matlab_py/redis_demo.m
main()

function [clean] = redis_str(x)
% 输入是由 Python Redis 模块返回的 Python 字节数组。
% 返回一个 MATLAB 字符串。
% 例如 x = py.bytes([uint8(56), uint8(46), uint8(57), uint8(53)])
%      clean = "8.95"
    clean = char( x );
    clean = string(clean(3:end-1)); % 去除开头的 'b' 和结尾的 '
end

function main()
    redis = py.importlib.import_module('redis');
    R = redis.Redis(pyargs('host','localhost','port',int64(6379)));
    try
        R.ping();
    catch EO
        fprintf('Redis 在运行吗？无法连接: %s\n', EO.message)
        return
    end
    fprintf('已连接到服务器.')
    R.config_set('notify-keyspace-events', 'KEA');
    mapping = struct;
    mapping.X = 8.9655;
    mapping.month = 'Feb';
    R.mset(mapping)
    retrieved_X     = redis_str(R.get('X'));
    retrieved_month = redis_str(R.get('month'));
    fprintf('Got X = %s\n', retrieved_X);
    fprintf('Got month = %s\n', retrieved_month);
    Sub = R.pubsub();
    Sub.psubscribe('__keyspace@0__:*')
    i = 1;
    while 1
        try
            message = Sub.get_message();
        catch EO
            fprintf('与服务器连接丢失: %s\n', EO.message)
            break
        end
        if message == py.None
            pause(0.01)
            continue
        end
        keyname = replace(redis_str(message{'channel'}), '__keyspace@0__:', '');
        if keyname == '*'
            % 初始订阅值
            continue
        end
        value = redis_str(R.get(keyname));
        fprintf('%s = %s\n', keyname, value)
    end
end
```

**Python**：

```python
#!/usr/bin/env python
"""code/matlab_py/redis_demo.py
https://tech.webinterpret.com/redis-notifications-python/
```