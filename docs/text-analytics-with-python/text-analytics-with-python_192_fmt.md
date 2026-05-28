# 获取结果

```
prover.prove(goal=test_outcome1,
             assumptions=[event1, rule],
             verbose=True)
```

```
[1] {-pass(John,exam)}                  A
[2] {-studies(John,exam)}               A
[3] {-studies(z6,exam), pass(z6,exam)}  A
[4] {-studies(John,exam)}               (1, 3)
Out[10]: False
```