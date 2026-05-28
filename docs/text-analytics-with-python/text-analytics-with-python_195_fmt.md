# 定义符号（实体/函数）及其值

`rules` = """
rover => r
felix => f
garfield => g
alex => a
dog => {r, a}
cat => {g}
fox => {f}
runs => {a, f}
sleeps => {r, g}
jumps_over => {(f, g), (a, g), (f, r), (a, r)}
"""

`val` = `nltk.Valuation.fromstring(rules)`