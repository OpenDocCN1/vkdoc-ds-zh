# 西格尔-图基检验

西格尔-图基检验用于判断两组数据中，是否有一组的数据比另一组更倾向于具有更广泛分散的值。换句话说，该检验用于确定两组中的某一组是否倾向于偏离（序数尺度的）中心，有时偏右，有时偏左。原假设是两组具有相同的方差和中位数。备择假设是一组比另一组具有更大的方差。

假设有两组数据`A`和`B`，第一组有`n`个观测值，第二组有`m`个观测值（因此总观测值`N = n + m`）。如果将所有`N`个观测值按升序排列，可以预期，如果两组之间没有差异（遵循原假设`H[0]`），那么这两组的值将会随机混合或排序。这意味着，在极端（高和低）得分的排名中，来自`A`组和`B`组的值会相似。比如说，如果`A`组更倾向于极端值（备择假设`H[1]`），那么来自`A`组的观测值中，低值或高值的比例会更高，而中心值的比例则会降低。该检验统计量具有`Wilcoxon`秩和分布，请参阅类`WilcoxonRankSumDistribution`。

NM Dev类`SiegelTukey`实现了西格尔-图基检验。以下是一个示例：

```kotlin
val sample1: DoubleArray = doubleArrayOf(4.0, 16.0, 48.0, 51.0, 66.0, 98.0)
val sample2: DoubleArray = doubleArrayOf(33.0, 62.0, 84.0, 85.0, 88.0, 93.0, 97.0)
val test: SiegelTukey = SiegelTukey(
    sample1,
    sample2,
    0.0, // 假设均值差
    true // 使用精确的 Wilcoxon 秩和分布而非正态分布
)
println("H0: " + test.getNullHypothesis())
println("H1: " + test.getAlternativeHypothesis())
println("test statistics = " + test.statistics())
println("p-value = " + test.pValue())
println("p-value, right sided = " + test.rightOneSidedPvalue())
println("p-value, left sided = " + test.leftOneSidedPvalue())
println("is null rejected at 5% = " + test.isNullRejected(0.05))
```

输出如下：

```
H0: the two samples have the same variability and median
H1: the two samples have different variabilities
test statistics = 16.0
p-value = 0.5337995337995338
p-value, right sided = 0.7773892773892773
p-value, left sided = 0.2668997668997669
is null rejected at 5% = false
```