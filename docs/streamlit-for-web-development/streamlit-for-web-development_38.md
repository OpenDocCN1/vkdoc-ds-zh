# Function to rename a selected database
def update_database(database_name_old, database_name_new, connection):
    connection.set_isolation_level(ISOLATION_LEVEL_AUTOCOMMIT)
    cursor = connection.cursor()
    try:
        cursor.execute(f"""SELECT pg_terminate_backend (pg_stat_activity.pid)
            FROM pg_stat_activity WHERE pg_stat_activity.datname = '{"warehouse_db_" + database_name_old}';""")
        cursor.execute(f"""ALTER DATABASE {'warehouse_db_' + database_name_old} RENAME TO {'warehouse_db_' + database_name_new};""")
        cursor.close()
        return True
    except Exception as e:
        print(e)

