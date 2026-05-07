# add a table called 'companies' with five fields
cursor.execute('''create table companies(
lon float,
lat float,
name text,
industry text,
revenue_mil float);''')
data = [
[-106.52, 35.079, "Acme, Inc.", "cosmetics", 12.3],
[-86.17, 41.717, "FlexMex", "restaurant", 0.5],
[-80.75, 41.214, "Titan Hercules, LLP", "sporting goods", 45.3],
[-95.54, 29.783, "The Chicken Palace", "restaurant", 22.3],
[-98.48, 29.355, "Joe's Diner", "restaurant", 0.0045],
[-78.99, 33.784, "Amalgamated Powders", "cosmetics", 1.9],
[-122.68, 45.525, "Pie and Cake", "restaurant", 2.2],
]
