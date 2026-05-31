# 计算每组的转化率
control_conversions = control_df.filter(col("converted") == 1).count()
control_rate = control_conversions / control_df.count()
treatment_conversions = treatment_df.filter(col("converted") == 1).count()
treatment_rate = treatment_conversions / treatment_df.count()
