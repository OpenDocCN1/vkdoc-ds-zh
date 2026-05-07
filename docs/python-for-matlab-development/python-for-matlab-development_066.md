# 从命令行执行 set/get：
redis-cli set price 3.21
redis-cli get price
"""
import time
import sys
import redis
R = redis.Redis(host='localhost',port=6379) # 默认值
try:
R.ping()
except redis.exceptions.ConnectionError as e:
print(f'Redis 在运行吗？无法连接 {e}')
sys.exit(1)
print(f'已连接到服务器.')
