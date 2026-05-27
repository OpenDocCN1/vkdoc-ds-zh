# 将 DataFrame 拆分为两个：一个用于对照组，一个用于处理组

```
control_df = data.filter(data.group == "control")
treatment_df = data.filter(data.group == "treatment")
```