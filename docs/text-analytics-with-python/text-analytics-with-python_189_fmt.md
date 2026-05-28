# 获取结果

`prover.prove(goal=test_outcome, assumptions=[event, rule], verbose=True)`

```
[1] {-jumps_over(dog,fox)}                    A
[2] {jumps_over(fox,dog)}                     A
[3] {-jumps_over(z4,z3), -jumps_over(z3,z4)}  A
[4] {-jumps_over(dog,fox)}                    (2, 3)
Out[9]: False
```

此输出显示，我们的目标 `test_outcome` 的最终结果为 `False`，即如果 `fox` 已经跳过了 `dog`，那么 `dog` 就不能跳过 `fox`。这基于我们的规则表达式以及分配给证明器中 `assumptions` 参数的事件。输出中还显示了通向该结果的步骤序列。

现在让我们考虑另一个 FOL 表达式规则：`∀x studies(x, exam) → pass(x, exam)`。这告诉我们，对于所有 `x` 的实例，如果 `x` 为考试而学习，他/她将通过考试。让我们表示这个规则，并考虑两名学生——John 和 Pierre，假设 John 没有为考试学习，但 Pierre 学习了。那么，我们能根据这个表达式规则判断他们是否会通过考试吗？以下代码片段显示了结果。

```