# read the contents of each row in descending order of revenue
query = 'select lat,name,revenue_mil from companies ' \
'order by revenue_mil desc'
for row in cursor.execute(query):
lat, name, revenue_mil = row
print(f'lat={lat:9.2f} {name:20s} {revenue_mil:9.5f}')
