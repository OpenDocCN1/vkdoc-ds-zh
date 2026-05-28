# 列标题

```python
category = ['Name', 'Animal', 'weight [kg]']

row, col = 4, 2

for i in range(len(category)):

cell = sheet.cell(row,col+i,category[i])

cell.fill = bg_green

pets = [['Nutmeg', 'Rabbit', 2.5],

['Annabel', 'Dog', 4.3],

['Sunny', 'Bird', 0.02],

['Harley', 'Dog', 17.1],

['Toby', 'Dog', 24.0],

['Mr Socks', 'Cat', 3.9]]

for P in pets:

row += 1

for j in range(len(category)):

cell = sheet.cell(row,col+j,P[j])

if j == 2 and P[j] < 0.1:

cell.font = ft_red
```