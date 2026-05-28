# 开始二分法

`low`、`high`、`middle` = 0, 2, 1
`epsilon` = 0.001
`while high-low > epsilon`:
`reachable = sum((x-0)**2 + (y - (-1))**2 <= (middle)**2 for x, y in zip(xs, ys))`
`if reachable > num_valid_points//2`: # 需要缩短束缚长度 `low`, `high`, `middle` = `low`, `middle`, `(low+middle)/2`
`elif reachable < num_valid_points//2`: # 需要增加束缚长度 `low`, `high`, `middle` = `middle`, `high`, `(middle+high)/2`
`else`:
`break`
`print(middle)` # 1.15869140625

好的，我敢打赌你已经感受到了蒙特卡洛模拟的精髓。在计算 π 和约束山羊这两个问题中，我们面对的本质上都是不涉及任何随机性的确定性问题。然而，通过引入随机性，我们将分析问题转变成了模拟问题。这就是蒙特卡洛模拟的力量。在下一节中，我们将探讨另一类问题，这类问题*本身*就具有内在随机性。

现在，在进入火箭的第二阶段之前，请完成以下练习。