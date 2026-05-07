# -*- coding: utf-8 -*-
################################################################
import sys
import os
import pandas as pd
import networkx as nx
import sqlite3 as sq
import numpy as np
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + '/VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
ReaderCode='SuperDataScientist'
```

请将下一行中的`'Practical Data Scientist'`替换为你的名字。

```
ReaderName='Practical Data Scientist'
```

现在，你设置所有代码输出文件的位置。

```
################################################################
Company='03-Hillman'
InputRawFileName='Animals.csv'
EDSRetrieveDir='01-Retrieve/01-EDS'
InputRetrieveDir=EDSRetrieveDir + '/02-Python'
InputRetrieveFileName='Retrieve_All_Animals.csv'
EDSAssessDir='02-Assess/01-EDS'
InputAssessDir=EDSAssessDir + '/02-Python'
InputAssessFileName='Assess_All_Animals.csv'
InputAssessGraphName='Assess_All_Animals.gml'
```

你现在创建所有代码输出文件的位置。

```
################################################################
sFileRetrieveDir=Base + '/' + Company + '/' + InputRetrieveDir
if not os.path.exists(sFileRetrieveDir):
    os.makedirs(sFileRetrieveDir)
################################################################
sFileAssessDir=Base + '/' + Company + '/' + InputAssessDir
if not os.path.exists(sFileAssessDir):
    os.makedirs(sFileAssessDir)
################################################################
sDataBaseDir=Base + '/' + Company + '/03-Process/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)
################################################################
sDatabaseName=sDataBaseDir + '/Hillman.db'
conn = sq.connect(sDatabaseName)
################################################################
