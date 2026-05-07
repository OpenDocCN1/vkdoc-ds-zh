# 为洞察数据库创建 PostgreSQL 客户端
username = config.database_credentials['username']
password = config.database_credentials['password']
port = config.database_credentials['port']
engine = db_engine(username, password, port)
