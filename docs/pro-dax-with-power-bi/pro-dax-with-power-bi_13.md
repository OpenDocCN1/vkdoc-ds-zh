# 查询计划

How can you determine if a function is an iterator? Can you tell by looking at the query plan? Chapter 13, "Query Plans," will provide a detailed introduction to query plans, what they mean, and how to interpret them. In short, reading the query plan alone does not always make it clear whether a function is an iterator.

The `DAX` engine is smart enough to design efficient plans that avoid iteration, even if you correctly use functions known to be iterators (such as `SUMX`). Three different queries might result in the same physical plan, differing only slightly in the logical plan. If the underlying engine operates in an iterative (row-by-row) fashion, the logical plan may offer some clues.

## 总结

This chapter provided a detailed introduction to iterator functions. Examples were used to demonstrate how iterator functions operate and how they differ from their non-iterator counterparts, especially in terms of calculation processing order.

Iterator functions can be nested to create multiple layers of loops, while functions like `EARLIER` and `EARLIEST` can be used to help inner loops reference the current value from a higher level in the call stack.

The `CONCATENATEX` function is useful for debugging calculations that use other iterator functions.

All in all, when used appropriately, they are a highly practical set of functions, and...

...if you don't use them, you're just being "dense." Ha!

