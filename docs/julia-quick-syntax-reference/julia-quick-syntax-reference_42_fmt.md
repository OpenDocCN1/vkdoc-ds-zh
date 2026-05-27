# j 市场 / 纽约, 芝加卡, 托皮卡 / ;

## 第 10 章 数学库

```julia
plants = ["seattle","san-diego"] # 罐头厂
markets = ["new-york","chicago","topeka"] # 市场
```

### 定义参数

工厂的产能和市场的需求直接以字典的形式定义，而距离则首先从以空白字符分隔的表格中读取为一个`DataFrame`。然后将其转换为 `"(工厂, 市场) => 值"` 的字典。