# `code/IO/tcp/client.py`

```python
import socket
import numpy as np
import struct

def wait_for_nBytes(n, Sock):
    Bytes = b''
    received = 0
    block_size = 2048
    while received < n:
        data = Sock.recv(min(n-received, block_size))
        if not data:
            break
        Bytes += data
        received += len(data)
        if len(Bytes) == n:
            break
    return Bytes

def main():
    Host, Port = '127.0.0.1', 5006
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((Host, Port))
    for i in range(10):
        N = np.random.randint(3,10) # 3 <= N < 10
        A = np.random.rand(N,N)
        s.send(struct.pack('q',N)) # send N
        print(f'sending A ({N:2d} x {N:2d}) ',end='')
        s.send(A.tobytes()) # send A
        Bytes = wait_for_nBytes(8*N*N, s)
        Ainv = np.frombuffer(Bytes, dtype=np.float64)
```