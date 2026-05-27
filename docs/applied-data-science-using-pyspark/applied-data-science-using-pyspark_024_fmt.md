# 删除原始列，因为我们创建了 `_imputed` 列
`df_imputed` = `df_imputed`.drop(`input_cols`)