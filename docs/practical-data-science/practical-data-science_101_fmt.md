# isolate gre and class rank

group the code

```
grouped = pd.pivot_table(combos, values=['admit_pred'], index=[variable, 'prestige'],
aggfunc=np.mean)
```