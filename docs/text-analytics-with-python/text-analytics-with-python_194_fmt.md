# 获取结果

```
prover.prove(goal=test_outcome2,
             assumptions=[event2, rule],
             verbose=True)
```

```
[1] {-pass(Pierre,exam)}                A
[2] {studies(Pierre,exam)}              A
[3] {-studies(z8,exam), pass(z8,exam)}  A
[4] {-studies(Pierre,exam)}             (1, 3)
[5] {pass(Pierre,exam)}                 (2, 3)
[6] {}                                  (1, 5)
```

Out[11]: True

因此，从这些评估中你可以看到，Pierre 确实通过了考试，因为他为考试学习了。然而，John 没有通过考试，因为他没有为考试学习。让我们考虑一个更复杂的例子，涉及多个执行多个动作的实体，如下所示。

* 有两只狗，Rover（`r`）和 Alex（`a`）
* 有一只猫，Garfield（`g`）
* 有一只狐狸，Felix（`f`）
* 两只动物——Alex（`a`）和 Felix（`f`）——在跑，由 `runs()` 函数表示
* 两只动物——Rover（`r`）和 Garfield（`g`）——在睡觉，由 `sleeps()` 函数表示
* 两只动物——Felix（`f`）和 Alex（`a`）——可以跳过另外两只动物，由 `jumps_over()` 函数表示

基于所有这些假设，以下代码片段构建了一个基于 FOL 的模型，其中包含基于实体和函数的域和赋值。一旦我们构建了这个模型，我们就评估各种基于 FOL 的表达式以确定它们的结果并证明定理，就像我们之前做的那样。

```