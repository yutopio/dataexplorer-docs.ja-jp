---
title: mv-apply 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの mv-apply 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8380e26b01f74585b2c3e99bb3eb4cd8c51df01c
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103068"
---
# <a name="mv-apply-operator"></a>mv-apply 演算子

各レコードにサブクエリを適用し、すべてのサブクエリの結果の和集合を返します。

たとえば、テーブルに `T` 型の列があり、 `Metric` `dynamic` 値が数値の配列であるとし `real` ます。 次のクエリでは、各値の最大2つの値を検索し、これらの値 `Metric` に対応するレコードを返します。

```kusto
T | mv-apply Metric to typeof(real) on 
(
   top 2 by Metric desc
)
```

`mv-apply`演算子には、次の処理手順があります。

1. 演算子を使用し [`mv-expand`](./mvexpandoperator.md) て、入力の各レコードを subtables に展開します。
1. 各 subtables のサブクエリを適用します。
1. 結果のサブ行に0個以上の列を追加します。 これらの列には、展開されていないソース列の値が含まれ、必要に応じて繰り返されます。
1. 結果の和集合を返します。

`mv-apply`演算子は、次の入力を取得します。

1. 展開する動的配列として評価される1つ以上の式。
   各拡張サブ形式のレコード数は、各動的配列の最大長です。 複数の式が指定され、対応する配列の長さが異なる場合は、Null 値が追加されます。

1. 必要に応じて、展開後に式の値を割り当てる名前を指定します。
   これらの名前は、subtables 内の列名になります。
   指定しない場合、式が列参照であるときに、列の元の名前が使用されます。 それ以外の場合は、ランダムな名前が使用されます。 

   > [!NOTE]
   > 既定の列名を使用することをお勧めします。

1. 拡張後のこれらの動的配列の要素のデータ型。
   これらは、subtables 内の列の列の型になります。
   指定しない場合は、`dynamic` が使用されます。

1. 必要に応じて、サブ行レコードを生成した配列内の要素の0から始まるインデックスを指定する、subtables に追加する列の名前を指定します。

1. 必要に応じて、展開する配列要素の最大数を指定します。

演算子は、 `mv-apply` 演算子の汎化と考えることができます [`mv-expand`](./mvexpandoperator.md) (実際には、サブクエリにプロジェクションしか含まれていない場合は、前者の場合は後者を実装できます)。

## <a name="syntax"></a>構文

*T* `|` `mv-apply` [*itemindex*] *columnstoexpand* [*rowlimit*] `on` `(` *サブクエリ*`)`

ここで、 *Itemindex* には次の構文があります。

`with_itemindex``=` *Indexcolumnname*

*Columnstoexpand* は、次の形式の1つ以上の要素をコンマで区切ったリストです。

[*名前* `=` ]*Arrayexpression* [ `to` `typeof` `(` *Typename* `)` ]

*Rowlimit* は単に次のようになります。

`limit`*Rowlimit*

および *サブクエリ* の構文は、任意のクエリステートメントと同じです。

## <a name="arguments"></a>引数

* *Itemindex*: 使用する場合、 `long` 配列展開フェーズの一部として入力に追加される型の列の名前を示し、展開された値の0から始まる配列インデックスを示します。

* *Name*: 使用されている場合は、配列展開された各式の配列で展開された値を割り当てる名前。
  指定しない場合は、使用可能な場合は列の名前が使用されます。
  *Arrayexpression*が単純な列名でない場合は、ランダムな名前が生成されます。

* *Arrayexpression*: `dynamic` 値が配列展開される型の式。
  式が入力の列の名前である場合、入力列が入力から削除され、同じ名前の新しい列 (指定されている場合は *ColumnName* ) が出力に表示されます。

* *Typename*: 使用されている場合、 `dynamic` 配列 *arrayexpression* の個々の要素によって取得される型の名前。 この型に準拠していない要素は、null 値に置き換えられます。
  (指定されていない場合、 `dynamic` は既定で使用されます。)

* *Rowlimit*: 使用される場合、入力の各レコードから生成するレコードの数の制限。
  (指定されていない場合、2147483647が使用されます)。

* *サブクエリ*: 配列で展開された各サブテーブルに適用される、暗黙的な表形式ソースを持つ表形式クエリ式。

**メモ**

* 演算子とは異なり [`mv-expand`](./mvexpandoperator.md) 、 `mv-apply` 演算子は配列の拡張だけをサポートします。 プロパティバッグの拡張はサポートされていません。

## <a name="examples"></a>例

## <a name="getting-the-largest-element-from-the-array"></a>配列から最大の要素を取得する

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data =
range x from 1 to 8 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply element=l to typeof(long) on 
(
   top 1 by element
)
```

|`xMod2`|l           |要素|
|-----|------------|-------|
|1    |[1, 3, 5, 7]|7      |
|0    |[2、4、6、8]|8      |

## <a name="calculating-the-sum-of-the-largest-two-elements-in-an-array"></a>配列内の最大の2つの要素の合計を計算する

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data =
range x from 1 to 8 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply l to typeof(long) on
(
   top 2 by l
   | summarize SumOfTop2=sum(l)
)
```

|`xMod2`|l        |SumOfTop2|
|-----|---------|---------|
|1    |[1, 3, 5, 7]|12       |
|0    |[2、4、6、8]|14       |

## <a name="using-with_itemindex-for-working-with-a-subset-of-the-array"></a>`with_itemindex`配列のサブセットを操作するためのの使用

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data =
range x from 1 to 10 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply with_itemindex=index element=l to typeof(long) on 
(
   // here you have 'index' column
   where index >= 3
)
| project index, element
```

|インデックス|要素|
|---|---|
|3|7|
|4|9|
|3|8|
|4|10|

## <a name="using-the-mv-apply-operator-to-sort-the-output-of-make_list-aggregate-by-some-key"></a>演算子を使用して、 `mv-apply` 集計の出力を `make_list` いくつかのキーで並べ替えます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(command:string, command_time:datetime, user_id:string)
[
    'chmod',        datetime(2019-07-15),   "user1",
    'ls',           datetime(2019-07-02),   "user1",
    'dir',          datetime(2019-07-22),   "user1",
    'mkdir',        datetime(2019-07-14),   "user1",
    'rm',           datetime(2019-07-27),   "user1",
    'pwd',          datetime(2019-07-25),   "user1",
    'rm',           datetime(2019-07-23),   "user2",
    'pwd',          datetime(2019-07-25),   "user2",
]
| summarize commands_details = make_list(pack('command', command, 'command_time', command_time)) by user_id
| mv-apply command_details = commands_details on
(
    order by todatetime(command_details['command_time']) asc
    | summarize make_list(tostring(command_details['command']))
)
| project-away commands_details
```

|`user_id`|`list_command_details_command`|
|---|---|
|user1|[<br>  "ls"、<br>  "mkdir"、<br>  "chmod"、<br>  "dir"、<br>  "pwd"、<br>  ws-rm<br>]|
|user2|[<br>  "rm"、<br>  pwd<br>]|

## <a name="see-also"></a>関連項目

* [mv-展開](./mvexpandoperator.md) 演算子。
