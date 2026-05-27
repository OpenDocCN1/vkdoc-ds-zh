# Simple Random Sampling without replacement

`df`=`df`.`select`('Geography','NumOfProducts','Age','Gender','Tenure','Exited')
`df_srs_without_rep` = `df`.`sample`(`False`, `0.5`, `23`)
`df_srs_without_rep`.`show`(`10`,`False`)

```python
无放回 SRS：
```