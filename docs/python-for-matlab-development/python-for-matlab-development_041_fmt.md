# print updated restaurant values

```python
query = "select lat,name,revenue_mil from companies " \
       "where industry = 'restaurant' order by revenue_mil desc"

for row in cursor.execute(query):
    lat, name, revenue_mil = row
    print(f'Updated: {name:20s} {revenue_mil:9.5f}')

connection.commit() # necessary to preserve update
connection.close()
```

运行此程序将输出

```plaintext
lat=    41.21 Titan Hercules, LLP   45.30000
lat=    29.78 The Chicken Palace    26.76000
lat=    35.08 Acme, Inc.            12.30000
lat=    45.52 Pie and Cake           2.64000
lat=    33.78 Amalgamated Powders    1.90000
lat=    41.72 FlexMex                0.60000
lat=    29.36 Joe's Diner            0.00540
Updated:  The Chicken Palace        32.11200
Updated:  Pie and Cake          3.16800
Updated:  FlexMex               0.72000
Updated:  Joe's Diner           0.00648
```

虽然`sqlite3`模块能将浮点数和字符串字段加载为正确类型的Python变量（双精度浮点数和字符串），但更特殊的数据类型（例如半精度浮点数或无符号短整数，这些类型可在NumPy中表示）则必须手动转换。

作为最后一个SQLite代码示例，我们将删除收入低于1000万美元的企业：

```python
#!/usr/bin/env python3
```