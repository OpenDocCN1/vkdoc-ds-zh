# 创建所有特征的向量
assembler = VectorAssembler(inputCols=[x for x in df.columns if x not in ignore], outputCol='features')
