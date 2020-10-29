---
title: KQL クイック リファレンス
description: 有用な KQL 関数およびその定義と構文例の一覧。
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 2fa4cbd0b1cf7b034bc7ae3202afcde3866ca347
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356590"
---
# <a name="kql-quick-reference"></a>KQL クイック リファレンス

この記事では、Kusto クエリ言語の使用を開始するのに役立つ関数とその定義の一覧を示します。

| 演算子/関数                               | 説明                           | 構文                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**フィルター/検索/条件**                      |**_フィルターまたは検索によって関連データを検索します_** |                      |
| [where](kusto/query/whereoperator.md)                      | 特定の述語でのフィルター。           | `T | where Predicate`                         |
| [where contains/has](kusto/query/whereoperator.md)        | `Contains`: 部分文字列の一致が検索されます。 <br> `Has`: 特定の単語が検索されます (パフォーマンスの向上)。  | `T | where col1 contains/has "[search term]"`|
| [search](kusto/query/searchoperator.md)                    | テーブル内のすべての列で値が検索されます。 | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](kusto/query/takeoperator.md)                        | 指定された数のレコードが返されます。 クエリのテストに使用します。<br>**_注_** : `_take`_ と `_limit`_ はシノニムです。 | `T | take NumberOfRows` |
| [case](kusto/query/casefunction.md)                        | 他のシステムの if/then/elseif と同様、条件ステートメントが追加されます。 | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](kusto/query/distinctoperator.md)                | 入力テーブルの指定された列の個別の組み合わせを含むテーブルが生成されます。 | `distinct [ColumnName], [ColumnName]` |
| **日付/時刻**                                   |**_日付と時刻の関数を使用する演算_**               |                          |
|[ago](kusto/query/agofunction.md)                           | クエリが実行された時刻に対する時間オフセットが返されます。 たとえば、`ago(1h)` は、現在のクロックの読み取り値の 1 時間前になります。 | `ago(a_timespan)` |
| [format_datetime](kusto/query/format-datetimefunction.md)  | データが[さまざまな日付の形式](kusto/query/format-datetimefunction.md#supported-formats)で返されます。 | `format_datetime(datetime , format)` |
| [bin](kusto/query/binfunction.md)                          | 期間内のすべての値が丸めてグループ化されます。 | `bin(value,roundTo)` |
| **列の作成/削除**                   |**_テーブル内の列が追加または削除されます_** |                                                    |
| [print](kusto/query/printoperator.md)                      | 1 つ以上のスカラー式を使用して単一行が出力されます。 | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](kusto/query/projectoperator.md)                  | 指定された順序で含める列が選択されます | `T | project ColumnName [= Expression] [, ...]` <br> または <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](kusto/query/projectawayoperator.md)         | 出力から除外する列を選択します。 | `T | project-away ColumnNameOrPattern [, ...]` |
| [project-keep](kusto/query/project-keep-operator.md)         | 出力に保持する列を選択します | `T | project-keep ColumnNameOrPattern [, ...]` |
| [project-rename](kusto/query/projectrenameoperator.md)     | 結果の出力の列名を変更します | `T | project-rename new_column_name = column_name` |
| [project-reorder](kusto/query/projectreorderoperator.md)   | 結果の出力の列を並べ替えます | `T | project-reorder Col2, Col1, Col* asc` |
| [extend](kusto/query/extendoperator.md)                    | 計算列が作成され、それが結果セットに追加されます。 | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **データセットの並べ替えと集計**                 |**_並べ替えまたはグループ化により、データが意味のある方法で再構築されます_**|                  |
| [sort](kusto/query/sortoperator.md)                        | 入力テーブルの行が 1 つ以上の列で昇順または降順に並べ替えられます。 | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [top](kusto/query/topoperator.md)                          | `by` を使用してデータセットが並べ替えられた場合に、データセットの先頭の N 行が返されます。 | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](kusto/query/summarizeoperator.md)              | `by` グループ列に従って行がグループ化され、各グループの集計が計算されます。 | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](kusto/query/countoperator.md)                       | 入力テーブル内のレコード数がカウントされます (たとえば、T)。<br>この演算子は、`summarize count() ` の短縮形です。| `T | count` |
| [join](kusto/query/joinoperator.md)                        | 各テーブルの指定された列で値が照合され、2 つのテーブルの行がマージされ、新しいテーブルが形成されます。 結合型のすべての種類 (`flouter`、`inner`、`innerunique`、`leftanti`、`leftantisemi`、`leftouter`、`leftsemi`、`rightanti`、`rightantisemi`、`rightouter`、`rightsemi`) がサポートされます。 | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](kusto/query/unionoperator.md)                      | 複数のテーブルが受け取られ、それらすべてのテーブルの行が返されます。 | `[T1] | union [T2], [T3], …` |
| [range](kusto/query/rangeoperator.md)                      | 等差級数の値を含むテーブルが生成されます。 | `range columnName from start to stop step step` |
| **データの書式設定**                                 | **_有用な方法でデータが再構築され出力されます_** | |
| [lookup](kusto/query/lookupoperator.md)                    | ディメンション テーブルで検索された値が使用されて、ファクトテーブルの列が拡張されます。 | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](kusto/query/mvexpandoperator.md)               | 動的配列が行に変換されます (複数値の展開)。 | `T | mv-expand Column` |
| [parse](kusto/query/parseoperator.md)                      | 文字列式が評価され、その値が 1 つまたは複数の計算列に解析されます。 非構造化データを構造化するために使用されます。 | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](kusto/query/make-seriesoperator.md)          | 指定された軸に沿って、指定された集計値の系列が作成されます。 | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [let](kusto/query/letstatement.md)                         | バインドされた値を参照できる式に名前がバインドされます。 値をラムダ式にして、クエリの一部としてアドホック関数を作成することができます。 `let` を使用して、結果が新しいテーブルのように見えるテーブルに式が作成されます。 | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **全般**                                     | **_その他の演算と関数_** | |
| [invoke](kusto/query/invokeoperator.md)                    | 入力として受け取るテーブルで関数が実行されます。 | `T | invoke function([param1, param2])` |
| [evaluate pluginName](kusto/query/evaluateoperator.md)     | クエリ言語拡張機能 (プラグイン) が評価されます。 | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **視覚化**                               | **_データをグラフィック形式で表示する演算_** | |
| [render](kusto/query/renderoperator.md) | 結果がグラフィック出力としてレンダリングされます。 | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
