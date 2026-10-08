# 索引

## A

- `accumulate()` 函数

## B

- `Broom` 包：实现 `tidy()`、`glance()` 和 `augment()` 函数

## C

- **列类型（Column types）**
    - 字符（characters）
    - `col_` 函数
    - 函数调用（function call）
    - 函数规范（function specification）
    - 整数类型（integer type）
    - 逻辑类型（logical type）
    - 有序参数（ordered argument）
    - 字符串规范格式（string specification format）
    - 时间解析器（time parser）
    - 时间点类型规范（time point type specification）
    - 警告消息（warning messages）

## D, E

- **数据框操作（Data frame manipulation）**
    - `arrange()` 函数
    - 列选择（column selection）
    - 列名（column names）
    - 移除列（remove columns）
    - `rename()` 函数
    - `select()` 函数
        - `Sepal.Length`
        - 源代码（source code）
        - `desc()` 函数
    - `dplyr`
        - `Seedplyr` 函数
        - 虚构国家（fictional countries）
        - `dplyr` 工具
        - 收入（income）
        - `mean_income`
        - `mutate()` 函数
        - 管道（pipeline）
            - `filter()` 函数
            - 分组/汇总（grouping/summarization）
            - 数据宽汇总（data-wide summarization）
            - `distinct()` 函数
            - `group_by()` 函数
            - 分组变量（grouping variables）
            - `group_vars()` 函数
            - `mutate()` 函数
            - `summarise()` 函数
            - `ungroup()` 函数
        - 连接表（joining tables）
            - `anti_join()` 函数
            - `bind_rows()` 和 `bind_columns()` 函数
            - `distinct()` 函数
            - `full_join()` 函数
            - `inner_join()` 函数
            - `left_join()`/`right_join()` 函数
            - `reduce()` 函数
            - `rename_at()` 函数
            - `semi_join()` 函数
            - `suffix` 参数修改
    - `abs()` 函数
    - `case_when()`
    - 厘米（centimeters）
    - `ifelse()`
    - `mutate()` 函数参数
    - `transmute()` 函数
    - `units` 包
    - `Vectorize()` 函数
    - 排序（sorting）
- **数据导入（Data importing）**
    - 列（Column）参见列类型（Column types）
    - 注释选项（comment option）
    - 文件头部（file headers）
    - 解析时间/日期（parsing time/dates）
        - 代码日期规范（codes date specifications）
        - 显式格式字符串（explicit format string formats）
        - `locale()` 函数
        - `parse_datetime()` 函数
        - 解析时间快捷方式（parsing time shortcuts）
    - 字符串（strings）
    - 读取表格数据（reading tabular data）
    - 空格分隔列（space-separated columns）
    - `spec()` 函数
    - 表格数据（tabular data）
    - 写入数据（writing data）
    - `dplyr` 函数
        - 列选择（column selection）
        - 过滤（filtering）

## F

- **因子（Factors）**
    - `as_factor()` 函数
    - 连接（concatenation）
    - 创建（creation）
    - `expand()` 函数
    - `explicit_na()` 函数
    - `factor()` 函数
    - `fct_unify()` 函数
    - `infreq()` 函数
    - `inorder()` 函数
    - `levels` 参数
    - `levels()` 函数
    - 投影（projection）
        - `collapse()` 函数
        - `drop()` 函数
        - `lump()` 函数
        - `other_level` 参数
        - `prop` 参数
    - `recode()` 函数
    - 重新排序水平（reorder levels）
- **过滤（Filtering）**
    - `compact()` 函数
    - 数据框（data frames）
        - `any_var()`/`all_var()` 函数
        - `between()` 函数
        - `distinct()` 函数
        - `filter()` 函数
        - `filter_all()` 函数
        - `filter_at()` 函数
        - `filter_if()` 函数
        - `iris_df()` 函数
        - `select()` 函数
        - `stringr` 包
    - 高阶函数（high-order function）
        - `negate()` 函数
    - `forcats` 包（参见 Factors）

## G, H

- `ggplot2` 包（参见 Plotting）

## I, J, K

- **索引 tibble（Indexing tibbles）**
    - 数据框（data frames）
    - 行/列子集（rows/columns subset）
        - 单括号索引（single-bracket index）
        - 单索引（single index）
        - 整洁选择向量（tidy selection vector column）

## L

- **Lambda 表达式（Lambda expressions）**

## M

- **`magrittr` 管道运算符（magrittr pipe operator）**
    - 数据框数据问题（data frame data problems）
    - `%<>%` 运算符
    - `pivot_longer()` 和 `drop_na()`
    - 绘图函数（plotting function）
    - 重新赋值运算符（reassignment operator）
    - tee 运算符（tee operator）
    - `tidyverse` 包
- **映射函数（Mapping function）**
    - 数据框（data frames）
        - `invisible()` 函数
        - 列表（lists）
        - `lmap()` 函数
        - `map()` 和 `length()` 函数
        - `map_chr()` 和 `map_int()` 函数
        - `map_depth()`
        - `map_df()` 函数
        - `map_dfr()` 和 `map_dfc()` 函数
        - `map_if()` 函数
        - `map_lgl()` 函数
        - `modify()` 函数
        - `pmap()` 函数
        - `rnorm()` 函数
        - 向量表达式（vector expressions）
- **`modelr` 包**
    - `add_predictions()`/`add_residuals()` 函数
    - 自举数据集（bootstrap data sets）
        - `bootstrap()` 函数
        - `crossv_mc()` 函数
    - 数据框拟合/检查模型（data frame fitting/inspecting models）
        - `fit_with()` 函数
        - `formulae()` 函数
        - `gather_predictions()` 和 `spread_predictions()`
    - 泛化（generalization）
        - `glance()` 函数
    - 均方根误差（root mean square error）
    - `seq_range()` 函数

## N, O

- **嵌套数据（Nesting data）**

## P, Q

- **管道（Pipelines）**
    - 组合运算符（composition operator）
    - 虚构国家（fictional countries）
    - 函数式编程（functional programming）
    - 函数参数符号（function arguments notation (|> and %>%)）
    - magrittr（参见 magrittr pipe operator）
    - 嵌套函数（nested function）
- **数据透视（Pivoting data）**
    - 整数（integers）
    - 逻辑/数值（logical/numeric values）
    - `mean_income`
    - `names_to`/`values_to` 参数
    - 参数（parameters）
    - `pivot_wider()` 函数
    - 范围规范（range specification）
    - 整洁选择语言（tidy selection language）
- **绘图（Plotting）**
    - 添加数据（add data）
    - 美学（aesthetics）
    - 组件（components）
    - 分面（facets）
    - 几何对象（geometries）
        - 美学（aesthetics）
        - `arrange()` 函数
        - 离散颜色（discrete color）
        - 离散颜色美学（discrete color aesthetics）
    - 几何图形（geometry plot）
        - `geom_histogram()` 函数
        - `geom_point()` 函数
    - 直方图（histogram plot）
    - 折线图（line plot）
    - 统计（statistics）
        - `summary()` 函数
    - `ggplot()` 对象
    - 绘图对象（plot object）
    - 极坐标（polar coordinates）
    - 刻度修改（scale modification）
    - `summary()` 函数
- **`purrr` 函数（purrr functions）**
    - 特性（features）
        - 过滤（filtering）
        - Lambda 表达式（lambda expressions）
        - 映射（mapping）
        - 偏应用/组合（partial evaluation/composition）
        - 归约/累加器（reduce/accumulator）

## R

- **范围（Ranges）**
    - `reduce()` 函数
- **改革（Reformation）**
    - 列编码（column encodings）
        - 字母字符和空格（alphabet characters and spaces）
        - `convert` 参数
        - 正则表达式（regular expression）
        - `remove` 参数
        - `separate()`/`extract()` 函数
        - `separate()` 函数
        - `separate_rows()`
        - `unite()` 函数
    - 数据属性（data properties）
        - 展开/交叉/补全（expanding/crossing/completing）
            - `complete()` 函数
            - `crossing()` 函数
            - `expand()` 函数
            - `nesting()` 函数
        - 缺失值（missing values）
            - `drop_na()` 函数
            - `fill()` 函数
            - `mean_income` 表
        - 嵌套数据（nesting data）
        - 数据透视（pivoting data）
        - `tidyr` 包
- **R 编程语言（R programming language）**
    - 数据结构（data structures）
    - 函数调用（function calls）
    - `magrittr` 包
    - 模型规范（model specifications）
    - `read.table()` 函数
    - Tidyverse

## S

- **字符串操作（String manipulation）**
    - `boundary()` 函数
    - 列 （参见 Column types）
        - `convert` 参数
        - `count()` 函数
        - `detect()` 函数
        - `extract` 选项
        - 过滤器（filters）
        - `indent` 参数
        - `invert_match()` 函数
        - `locate_all()` 函数
        - `locate()` 函数
        - 小写/大写（lowercase/uppercase）
        - 嵌套数据（nesting data）
        - 数据透视（pivoting data）
        - `separate()`/`extract()` 函数
        - `side` 参数
        - `split()` 函数
        - `squish()` 函数
        - `stringr` 包
    - 整洁选择（tidy selection）
    - `tidyverse` 包
    - `title()` 函数
    - 转换（transformation）
        - `dup()` 函数
        - `glue()` 函数
        - `replace_all()`
        - `str_sub()` 函数
        - 子串（substring）
        - `trim()` 函数
        - `trunc()` 函数
        - `width` 参数
        - `wrap()` 函数

## T, U, V, W, X, Y, Z

- **表格表示（Table representation）**
    - 改革（参见 Reformation）
    - `tibble` 对象
- **`Tibble` 包（Tibble packages）**
    - `as_tibble()` 函数
    - `data.frame` 版本（`data.frame` version）
    - 错误消息（error message）
    - 实现（implementation）
    - 索引（参见 Indexing tibbles）
    - `print()` 函数
    - 表格数据（tabular data）
    - `tibble()` 函数
    - 整洁选择（tidy selection）
    - 宽度选项（width option）
- **整洁选择（Tidy selection）**
    - 补集运算符（complement operator）`!`
    - `all_of()`/`any_of()` 函数
    - `&` (交集)/`|` (并集)
    - 列（columns）
        - 列选择（column selection）
        - `contains()` 函数
    - 数据属性（data properties）
        - `ends_with()` 函数
        - `everything()` 函数
        - 索引语言（indexing language）
        - `matches()` 函数
        - 范围（range）
        - 选择函数（select function）
        - `starts_with()` 函数
        - 字符串选择（string selection）
        - `tidyr` 包
- **`Tidyverse` 包（Tidyverse packages）**
    - 数据分析（data analysis）
    - 数据导入（data importing）
    - 开发（development）
    - `forcats` 包
    - `ggplot2` 包
    - Lambda 表达式（lambda expressions）
    - `lubridate` 包
    - 编程接口（programming interfaces）
    - 字符串操作（string manipulation）
    - 表格（tables）
    - tibble
- **时间格式（Time formats）**
    - `force_tz()` 函数
    - `interval()` 函数
    - `int_aligns()` 函数
    - `int_flip()` 函数
    - `int_length()` 函数
    - `int_overlaps()` 函数
    - `int_standardize()` 函数
    - `int_start()`/`int_end()`
    - 源代码（source code）
    - 时间点（points）
    - `with_tz()` 函数
    - 时区（zones）