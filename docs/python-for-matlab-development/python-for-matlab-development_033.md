# create the database file lat_lon_company.db
db_file = 'lat_lon_company.db'
if os.path.exists(db_file):
os.remove(db_file)
connection = sqlite3.connect(db_file)
cursor     = connection.cursor()
