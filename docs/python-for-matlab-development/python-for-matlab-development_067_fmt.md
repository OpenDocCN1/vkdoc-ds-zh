# 修改配置以启用键空间通知

```python
R.config_set('notify-keyspace-events', 'KEA')

retrieved_X = R.get('X')
retrieved_month = R.get('month')

print(f'Got X = {retrieved_X}')
print(f'Got month = {retrieved_month}')

print('等待订阅事件')

Sub = R.pubsub()
Sub.psubscribe('__keyspace@0__:*')

while True:
    try:
        message = Sub.get_message()
    except redis.exceptions.ConnectionError:
        print('与服务器连接丢失')
        sys.exit(1)

    if message is None:
        time.sleep(0.01)
        continue

    keyname = message['channel'].decode().replace('__keyspace@0__:', '')

    if keyname == '*':
```