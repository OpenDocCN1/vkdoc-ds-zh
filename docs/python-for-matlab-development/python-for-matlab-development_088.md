# 筛选出仅洛杉矶县的数据
LA_prices = home_prices[
home_prices['CountyName'] == 'Los Angeles County'][
['ZipCode','2020-05-31','2021-05-31']]
LA_price_map = zipcode_geom.merge(LA_prices,on='ZipCode')
LA_price_map['2020-05-31'] /= 1.0e6  # 将单位更改为百万美元
LA_price_map.plot(column='2020-05-31',cmap='plasma',
legend=True,figsize=(10,14))
plt.xlim(left=-118.55,right=-118.17)
plt.ylim(top=34.14,bottom=33.7)
plt.title('Los Angeles County Home Prices 2020-05-31, $M')
plt.ylabel('Latitude [deg]')
plt.xlabel('Longitude [deg]')
