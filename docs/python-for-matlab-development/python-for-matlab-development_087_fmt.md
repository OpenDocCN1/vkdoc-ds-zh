# 将类型从 'object' 更改为 'int64'

```python
zipcode_geom['ZipCode'] = zipcode_geom['ZipCode'].astype(int)
```