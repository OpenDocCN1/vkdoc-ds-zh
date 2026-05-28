# 重命名邮政编码列以便进行连接

```python
zipcode_geom.rename(columns={'ZCTA5CE10':'ZipCode'}, inplace=True)

home_prices.rename(columns={'RegionName':'ZipCode'}, inplace=True)
```