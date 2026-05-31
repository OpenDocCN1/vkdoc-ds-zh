#    Python 写入的文件
In : with open('Xp.bin', 'rb') as fh:
...:     raw = fh.read() # 将全部内容加载到 raw 中
...:     n_terms  = len(raw) // 8 # 整数除法
...:     Ints = struct.unpack(f'{n_terms}q', raw)
In : Ints
Out: (-4, -3, -2, -1, 0, 1, 2, 3, 4)
In : X = np.array(Ints).reshape(3,3)
In : X
array([[-4, -3, -2],
[-1,  0,  1],
[ 2,  3,  4]])
