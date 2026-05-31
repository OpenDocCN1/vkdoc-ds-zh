# 评估各种表达式
m.evaluate('jumps_over(felix, rover) & dog(rover) & runs(rover)', None)
False
m.evaluate('jumps_over(felix, rover) & dog(rover) & -runs(rover)', None)
True
m.evaluate('jumps_over(alex, garfield) & dog(alex) & cat(garfield) & sleeps(garfield)', None)
True
