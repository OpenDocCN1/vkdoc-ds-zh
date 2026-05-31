# print('################')
sTable = 'Assess_FirstName'
print('存储中:', sDatabaseName, '表:', sTable)
FirstNameData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
################################################################
print('################')
sTable = 'Assess_FirstName x2 & Assess_Surname'
print('加载中:', sDatabaseName, '表:', sTable)
sSQL = "select distinct"
sSQL = sSQL + " A.FirstName,"
sSQL = sSQL + " B.FirstName AS SecondName,"
sSQL = sSQL + " C.LastName,"
sSQL = sSQL + " A.Gender"
sSQL = sSQL + " from"
sSQL = sSQL + " Assess_FirstName as A"
sSQL = sSQL + " ,"
sSQL = sSQL + " Assess_FirstName as B"
sSQL = sSQL + " ,"
sSQL = sSQL + " Assess_Surname as C"
sSQL = sSQL + " WHERE"
sSQL = sSQL + " A.Gender = B.Gender"
sSQL = sSQL + " AND"
sSQL = sSQL + " A.FirstName <> B.FirstName;"
PeopleRawData = pd.read_sql_query(sSQL, conn)
People1Data = PeopleRawData.sample(10000)
sTable = 'Assess_FirstName & Assess_Surname'
print('加载中:', sDatabaseName, '表:', sTable)
sSQL = "select distinct"
sSQL = sSQL + " A.FirstName,"
sSQL = sSQL + " '' AS SecondName,"
sSQL = sSQL + " B.LastName,"
sSQL = sSQL + " A.Gender"
sSQL = sSQL + " from"
sSQL = sSQL + " Assess_FirstName as A"
sSQL = sSQL + " ,"
sSQL = sSQL + " Assess_Surname as B;"
PeopleRawData = pd.read_sql_query(sSQL, conn)
People2Data = PeopleRawData.sample(10000)
PeopleData = People1Data.append(People2Data)
print(PeopleData)
print('################')
#################################################################
