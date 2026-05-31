# isolate gre and class rank
grouped = pd.pivot_table(combos, values=['admit_pred'], index=[variable, 'prestige'],
aggfunc=np.mean)
