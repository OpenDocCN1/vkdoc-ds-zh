# Ainv = np.array(struct.unpack(f'{N*N}d',Bytes))
Ainv = Ainv.reshape(N,N)
err = np.max(np.eye(N) - A@Ainv)
print(f'err = {err:e}')
s.close()
if __name__ == "__main__": main()
```

TCP 套接字的 `.send()` 和 `.recv()` 函数处理原始字节序列的方式与二进制文件读写类似。这意味着发送的数据必须先使用`struct`模块的`pack()`函数（如第 7.6 节所示）或 NumPy 数组的 `.tobytes()` 方法，从其原生存储格式转换为字节。

同样，传入的 TCP 数据以字节组的形式到达，最大不超过声明的缓冲区大小，本例中为 2048 字节。^(²⁹) 如果预期消息大于缓冲区大小，我们会持续累积字节，直到达到预期大小。空消息表示连接已断开。在接收到完整消息后，必须使用 `struct.unpack()` 或（如果要创建 NumPy 数组）`np.frombuffer()` 将字节解码为所需的类型。

### 7.17.3.2 Python 服务器

Python 服务器程序重用了在客户端程序中定义的 `wait_for_nBytes()` 函数：

Python:

```python
#!/usr/bin/env python3
