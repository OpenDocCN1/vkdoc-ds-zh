# 此代码为每种处理类型创建结果变量列表
control_data_flag = control_df.select('converted').rdd.flatMap(lambda x: x).collect()
treatment_data_flag = treatment_df.select('converted').rdd.flatMap(lambda x: x).collect()
import scipy.stats as stats
