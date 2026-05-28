# Raw to Retrieve

################################################################

你上传包含扁平结构的 CSV 文件。

```

sFileName=Base + '/' + Company + '/00-RawData/' + InputRawFileName

print('###########')

print('Loading :',sFileName)

AnimalRaw=pd.read_csv(sFileName,header=0,low_memory=False, encoding = "ISO-8859-1")

AnimalRetrieve=AnimalRaw.copy()

print(AnimalRetrieve.shape)

```

################################################################

你现在存储检索步骤的数据。

```

sFileName=sFileRetrieveDir + '/' + InputRetrieveFileName

print('###########')

print('Storing Retrieve :',sFileName)

AnimalRetrieve.to_csv(sFileName, index = False)

```

你现在存储评估步骤的数据。

################################################################