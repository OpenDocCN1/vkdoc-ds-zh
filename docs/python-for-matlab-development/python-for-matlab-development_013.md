#    从当前时间创建协调世界时的更好方法
In : datetime.now(tz=tz.UTC)
Out: datetime.datetime(2019, 11, 10, 2, 20, 57, 118448, tzinfo=tzutc())
In : epoch_sec = time.time()
In : epoch_sec
Out: 1573352547.984918
In : datetime.fromtimestamp(epoch_sec)
Out: datetime.datetime(2019, 11, 9, 18, 22, 27, 984918)
