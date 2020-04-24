---
title: mv-apply オペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの mv-apply 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f24bf7721707aa1ba3ae9f0aad49b247f08c2498
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512310"
---
# <a name="mv-apply-operator"></a>mv-apply 演算子

mv-apply 演算子は、入力テーブル内の各レコードをサブテーブルに展開し、サブクエリを各サブテーブルに適用し、すべてのサブクエリの結果の和集合を返します。

たとえば`T`、テーブルに、値が数値の`Metric`配列である`dynamic`型の`real`列があるとします。 次のクエリは、各`Metric`値の 2 つの最大の値を検索し、これらの値に対応するレコードを返します。

```kusto
T | mv-apply Metric to typeof(real) on (top 2 by Metric desc)
```

一般に、mv-apply 演算子は、以下の処理手順があると考えることができます。

1. [mv-expand](./mvexpandoperator.md)演算子を使用して、入力内の各レコードをサブテーブルに展開します。
2. サブテーブルごとにサブクエリを適用します。
3. このサブテーブルは、展開されていないソース列の値 (必要に応じて繰り返される) を含む、結果の各サブテーブルに 0 個以上の列を先頭に付けます。
4. 結果の和集合を返します。

mv-expand 演算子は、次の入力を取得します。

1. 拡張する動的配列に評価される 1 つ以上の式。
   各拡張サブテーブルのレコード数は、これらの動的配列の最大長です。 (複数の式が指定されているが、対応する配列の長さが異なる場合は、必要に応じて NULL 値が導入されます。

2. オプションで、式の値を割り当てる名前を、展開後に指定します。
   これらは、サブテーブルの列の名前になります。
   指定しない場合は、列の元の名前が使用されるか (式が列参照の場合)、ランダムな名前が使用されます (それ以外の場合)。

   > [!NOTE]
   > デフォルトの列名を使用することをお勧めします。

3. 拡張後の動的配列の要素のデータ型。
   これらは、サブテーブルの列の列タイプになります。
   指定しない場合は、`dynamic` が使用されます。

4. 必要に応じて、サブテーブル レコードの結果となった配列内の要素の 0 から始まるインデックスを指定する、サブテーブルに追加する列の名前。

5. 必要に応じて、展開する配列要素の最大数。

mv-apply 演算子は[、mv-expand](./mvexpandoperator.md)演算子の一般化と考えることができます (実際には、サブクエリに投影のみが含まれている場合は、後者は前者によって実装できます)。

**構文**

*T* `|` T `mv-apply` [*アイテムインデックス*]*列展開する [* *行制限*] `on` `(` *サブクエリ*`)`

*ここで ItemIndex には*次の構文があります。

`with_itemindex``=`*インデックス列名*

*列の展開*は、フォームの 1 つ以上の要素をコンマで区切ったリストです。

[*名前*`=`]*配列式*`to` `typeof` `(`[*型名*`)`]

*行制限*は単純です。

`limit`*行制限*

*SubQuery*は、任意のクエリ ステートメントと同じ構文を持っています。

**引数**

* *ItemIndex*: 使用する場合は、配列展開フェーズの`long`一部として入力に追加される型の列の名前を示し、展開された値の 0 から始まる配列インデックスを示します。

* *名前*: 使用する場合、配列展開式の配列展開値を割り当てる名前。
  (指定しない場合は、列の名前が使用できる場合は使用されるか *、ArrayExpression*が単純な列名でない場合はランダムな名前が生成されます。

* *配列式*: 値が`dynamic`配列展開される型の式。
  式が入力内の列の名前である場合、入力列は入力から削除され、同じ名前の新しい列 (または*ColumnName*を指定した場合) が出力に表示されます。

* *型名*: 使用する場合は、*配列 ArrayExpression*の各`dynamic`要素が受け取る型の名前。 この型に準拠していない要素は、null 値に置き換えられます。
  (指定されていない場合は`dynamic`、デフォルトで使用されます。

* *RowLimit*: 使用する場合、入力の各レコードから生成されるレコード数の制限。
  (指定されていない場合は、2147483647 が使用されます。

* *SubQuery*: 各配列展開されたサブテーブルに適用される、暗黙の表形式ソースを持つ表形式のクエリ式。

**メモ**

* [mv-expand](./mvexpandoperator.md)演算子とは異なり、mv-apply 演算子は配列の拡張のみをサポートします。 プロパティバッグの拡張はサポートしていません。

**使用例**

## <a name="getting-the-largest-element-from-the-array"></a>配列から最大の要素を取得する

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

|xMod2|l           |要素|
|-----|------------|-------|
|1    |[1, 3, 5, 7]|7      |
|0    |[2, 4, 6, 8]|8      |

## <a name="calculating-sum-of-largest-two-elments-in-an-array"></a>配列内の最大 2 つのエルメントの合計を計算する

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

|xMod2|l        |合計|
|-----|---------|---------|
|1    |[1,3,5,7]|12       |
|0    |[2,4,6,8]|14       |


## <a name="using-with_itemindex-for-working-with-subset-of-the-array"></a>配列`with_itemindex`のサブセットを操作するための使用

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

|インデックス (index)|要素|
|---|---|
|3|7|
|4|9|
|3|8|
|4|10|

## <a name="using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key"></a>演算子`mv-apply`を使用して、集計の`makelist`出力をキーでソートする

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

|user_id|list_command_details_command|
|---|---|
|user1|[<br>  "ls",<br>  "mkdir",<br>  "chmod",<br>  "dir",<br>  "pwd"、<br>  "rm"<br>]|
|user2|[<br>  "rm",<br>  "pwd"<br>]|


**参照**

* [mv-expand](./mvexpandoperator.md)演算子。