# code/IO/tcp/server.py
import struct
import numpy as np
import socket
from client import wait_for_nBytes
def main():
Host, Port = 'localhost', 5006
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind((Host, Port))
s.listen(1)
conn, addr = s.accept()
while True:
try:
Bytes = wait_for_nBytes(8, conn) # 获取 N 的字节
if not Bytes:
break
N = struct.unpack(f'q', Bytes)[0] # 字节 -> N
print(f'got N={N}')
Bytes = wait_for_nBytes(8*N*N, conn) # 获取 A 的字节
A = np.frombuffer(Bytes, dtype=np.float64)
