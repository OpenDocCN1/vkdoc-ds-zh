# Function to create a table in the selected database
def create_table(table_name, table, engine):
    table.to_sql(table_name, engine, index=False, if_exists='replace', chunksize=1000)

