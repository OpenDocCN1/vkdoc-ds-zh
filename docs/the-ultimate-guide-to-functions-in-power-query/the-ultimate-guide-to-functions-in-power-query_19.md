# 目录

-   关于作者
-   关于技术审校
-   致谢
-   引言

## 第 1 章：Power Query 简介

-   Power Query 简介
-   Excel 中的 Power Query
-   Power BI 中的 Power Query
    -   Power Query 界面
-   M 语言
-   本章小结

## 第 2 章：数据类型

-   Power Query 中的数据类型
    -   Null 值
    -   逻辑值
    -   数字
    -   时间和日期
    -   文本
    -   二进制数据
    -   列表
    -   记录
    -   表
    -   函数
-   类型转换
    -   将值转换为数字格式
    -   将值转换为文本格式
    -   将值转换为日期和时间格式
    -   将值转换为记录格式
-   本章小结

## 第 3 章：数字函数

-   常量值
-   信息函数
-   舍入操作
-   数学运算
-   随机数生成
-   三角函数
-   本章小结

## 第 4 章：文本函数

-   提取文本的一部分
    -   `Text.At`
    -   `Text.Start`
    -   `Text.End`
    -   `Text.Range`
    -   `Text.AfterDelimiter`
    -   `Text.BeforeDelimiter`
    -   `Text.BetweenDelimiters`
    -   `Text.Select`
    -   `Text.Split`
-   删除、替换与合并文本
    -   `Text.Remove`
    -   `Text.RemoveRange`
    -   `Text.PadEnd`
    -   `Text.PadStart`
    -   `Text.Replace`
    -   `Text.ReplaceRange`
    -   `Text.Insert`
    -   `Text.Repeat`
    -   `Text.Combine`
-   文本校正
-   字符搜索
    -   `Text.StartsWith`
    -   `Text.EndsWith`
    -   `Text.Contains`
    -   `Text.PositionOf`
    -   `Text.PositionOfAny`
-   文本转换
-   本章总结

## 第 5 章：日期和时间函数

-   日期信息
-   日期时间信息
-   为日期添加数值
-   日期偏移
-   转换为记录
-   日期具体值
    -   `Date.Day`
    -   `Date.DayOfWeek`
    -   `Date.DayOfWeekName`
    -   `Date.WeekOfMonth`
    -   `Date.WeekOfYear`
    -   `Date.Month`
    -   `Date.MonthName`
    -   `Date.DayOfYear`
    -   `Date.Year`
-   本章总结

## 第 6 章：列表函数

-   在 Power Query 中定义列表
    -   `List.Numbers`
    -   `List.Generate`
    -   `List.Times`
    -   `List.Dates`
    -   `List.DateTimes`
    -   `List.Random`
-   对列表中的值进行排序
    -   `List.Reverse`
    -   `List.Sort`
-   从列表中提取
    -   `List.First` 和 `List.Last`
    -   `List.FirstN` 和 `List.LastN`
    -   `List.Range`
    -   `List.Max` 和 `List.Min`
    -   `List.MaxN` 和 `List.MinN`
    -   `List.Select`
    -   `List.FindText`
-   删除列表中的值
    -   `List.RemoveNull`
    -   `List.Distinct`
    -   `List.RemoveFirsN` 和 `List.RemoveLastN`
    -   `List.RemoveRange`
    -   `List.RemoveItems`
    -   `List.Skip`
    -   `List.Alternate`
-   列表合并
    -   `List.Combine`
    -   `List.InsertRange`
    -   `List.Zip`
    -   `List.Union`
    -   `List.Intersect`
    -   `List.Difference`
-   对列表数据进行数学计算
-   列表信息函数
    -   `List.IsEmpty`
    -   `List.IsDistinct`
    -   `List.Contains`
    -   `List.ContainsAll`
    -   `List.ContainsAny`
    -   `List.AllTrue`
    -   `List.AnyTrue`
-   列表中的位置
    -   `List.Positions`
    -   `List.PositionOf`
    -   `List.PositionOfAny`
-   替换和转换值
    -   `List.ReplaceValue`
    -   `List.ReplaceMatchingItems`
    -   `List.ReplaceRange`
    -   `List.Transform`
    -   `List.TransformMany`

*   其他函数
    *   `List.Single`
    *   `List.SingleOrDefault`
    *   `List.Repeat`
    *   `List.Split`
    *   总结
*   第 7 章：记录
    *   定义记录
    *   记录属性
        *   `Record.FieldCount`
        *   `Record.FieldNames`
        *   `Record.FieldValues`
        *   `Record.ToList`
        *   `Record.Field`
        *   `Record.FieldOrDefault`
        *   `Record.HasFields`
    *   记录修正
        *   `Record.AddField`
        *   `Record.Combine`
        *   `Record.SelectFields`
        *   `Record.RemoveFields`
        *   `Record.RenameFields`
        *   `Record.ReorderFields`
        *   `Record.TransformFields`
    *   记录转换
        *   `Record.FromList`
        *   `Record.FromTable`
        *   `Record.ToTable`
    *   总结
*   第 8 章：表
    *   创建表
        *   `Table.FromColumns`
        *   `Table.FromRows`
        *   `Table.FromRecords`
        *   `Table.FromValue`
    *   表转换
        *   `Table.ToRecords`
        *   `Table.ToColumns`
        *   `Table.ToRows`
        *   `Table.ToList`
        *   `Table.Column`
        *   `Table.TransformRows`
        *   `Table.Split`
        *   `Table.SplitAt`
    *   信息
    *   选择表行
        *   `Table.FirstValue`
        *   `Table.First` 和 `Table.Last`
        *   `Table.FirstN` 和 `Table.LastN`
        *   `Table.Max` 和 `Table.Min`
        *   `Table.MaxN` 和 `Table.MinN`
        *   `Table.Range`
        *   `Table.SelectRows`
        *   `Table.SelectRowsWithErrors`
    *   删除表行
        *   `Table.RemoveFirstN` 和 `Table.RemoveLastN`
        *   `Table.Distinct`
        *   `Table.Skip`
        *   `Table.RemoveRows`
        *   `Table.AlternateRows`
        *   `Table.RemoveRowsWithErrors`
        *   `Table.RemoveMatchingRows`
    *   插入和替换表行
        *   `Table.InsertRows`
        *   `Table.ReplaceRows`
        *   `Table.ReplaceMatchingRows`
        *   `Table.Repeat`
    *   行排序
        *   `Table.ReverseRows`
        *   `Table.Sort`
    *   检查所有行中的条件
        *   `Table.IsDistinct`
        *   `Table.MatchesAllRows`
        *   `Table.MatchesAnyRows`
        *   `Table.Contains`
        *   `Table.ContainsAll` 和 `Table.ContainsAny`
        *   `Table.SingleRow`
    *   列名
        *   `Table.ColumnNames`
        *   `Table.DemoteHeaders`
        *   `Table.PromoteHeaders`
        *   `Table.HasColumns`
        *   `Table.ColumnsOfType`
        *   `Table.PrefixColumns`
        *   `Table.RenameColumns`
    *   修改列
        *   `Table.RemoveColumns`
        *   `Table.SelectColumns`
        *   `Table.DuplicateColumn`
        *   `Table.TransformColumnTypes`
        *   `Table.TransformColumns`
        *   `Table.AddIndexColumn`
        *   `Table.AddColumn`
        *   `Table.AddRankColumn`
        *   `Table.SplitColumn`
        *   `Table.CombineColumns`
        *   `Table.CombineColumnsToRecord`
        *   `Table.ReorderColumns`
        *   `Table.Pivot`
        *   `Table.Unpivot`
        *   `Table.Group`
        *   `Table.Transpose`
    *   合并表
        *   `Table.Combine`
        *   `Table.Join`
        *   `Table.NestedJoin`
    *   展开列
        *   `Table.ExpandListColumn`
        *   `Table.ExpandRecordColumn`
        *   `Table.ExpandTableColumn`
        *   `Table.AggregateTableColumn`
    *   替换列值
        *   `Table.FillDown` 和 `Table.FillUp`
        *   `Table.ReplaceErrorValues`
        *   `Table.ReplaceValue`
    *   在表中查找值
        *   `Table.FindText`
        *   `Table.PositionOf`
        *   `Table.PositionOfAny`
    *   总结
*   第 9 章：从数据源提取
    *   `Folder.Contents`
    *   `Csv.Document`
    *   `Excel.Workbook`
    *   `Pdf.Tables`
    *   `Sql.Database`
    *   `Excel.CurrentWorkbook`
    *   总结
*   第 10 章：其他函数
    *   `if`
        *   组合条件
    *   `try` 函数
        *   `let-in` 命令
    *   定义新函数
*   在其他查询中使用已定义的函数
*   索引