# code/IO/sqlite_read.py
import sqlite3
connection = sqlite3.connect('lat_lon_company.db')
cursor     = connection.cursor()
