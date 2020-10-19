---
title: array_sort_desc ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの array_sort_desc () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 09/22/2020
ms.openlocfilehash: 83be80a7eb440e73fd19f213839cef7d58ec1512
ms.sourcegitcommit: 62476f682b7812cd9cff7e6958ace5636ee46755
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169649"
---
# <a name="array_sort_desc"></a>array_sort_desc ()

1つ以上の配列を受け取ります。 最初の配列を降順で並べ替えます。 並べ替えられた最初の配列に一致するように、残りの配列を並べ替えます。

## <a name="syntax"></a>Syntax

`array_sort_desc(`*配列*1 [,、..., *argumentn*]`)`

`array_sort_desc(`*配列*1 [,、..., *argumentn*] `,` *nulls_last*`)`

*Nulls_last*が指定されていない場合は、既定値の `true` が使用されます。

## <a name="arguments"></a>引数

* *配列の配列...arrayN*: 入力配列。
* *nulls_last*: `null` が最後である必要があるかどうかを示すブール値

## <a name="returns"></a>戻り値

入力と同じ数の配列を返します。最初の配列は昇順に並べ替えられ、残りの配列は並べ替えられた最初の配列と一致するように並べ替えられます。

`null` は、最初の配列と長さが異なるすべての配列に対して返されます。

配列に異なる型の要素が含まれている場合は、次の順序で並べ替えられます。

* Numeric、 `datetime` 、および `timespan` 要素
* 文字列要素
* Guid 要素
* その他のすべての要素

## <a name="example-1---sorting-two-arrays"></a>例 1-2 つの配列の並べ替え

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let array1 = dynamic([1,3,4,5,2]);
let array2 = dynamic(["a","b","c","d","e"]);
print array_sort_desc(array1,array2)
```

|`array1_sorted`|`array2_sorted`|
|---|---|
|[5、4、3、2、1]|["d"、"c"、"b"、"e"、"a"]|

> [!Note]
> 出力列の名前は、関数の引数に基づいて自動的に生成されます。 出力列に別の名前を割り当てるには、次の構文を使用します。 `... | extend (out1, out2) = array_sort_desc(array1,array2)`

## <a name="example-2---sorting-substrings"></a>例 2-部分文字列の並べ替え

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Names = "John,Paul,George,Ringo";
let SortedNames = strcat_array(array_sort_desc(split(Names, ",")), ",");
print result = SortedNames
```

|`result`|
|---|
|Ringo, Paul, John, ジョージ|

## <a name="example-3---combining-summarize-and-array_sort_desc"></a>例 3-集計と array_sort_desc の結合

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable(command:string, command_time:datetime, user_id:string)
[
    'chmod',   datetime(2019-07-15),   "user1",
    'ls',      datetime(2019-07-02),   "user1",
    'dir',     datetime(2019-07-22),   "user1",
    'mkdir',   datetime(2019-07-14),   "user1",
    'rm',      datetime(2019-07-27),   "user1",
    'pwd',     datetime(2019-07-25),   "user1",
    'rm',      datetime(2019-07-23),   "user2",
    'pwd',     datetime(2019-07-25),   "user2",
]
| summarize timestamps = make_list(command_time), commands = make_list(command) by user_id
| project user_id, commands_in_chronological_order = array_sort_desc(timestamps, commands)[1]
```

|`user_id`|`commands_in_chronological_order`|
|---|---|
|user1|[<br>  "rm"、<br>  "pwd"、<br>  "dir"、<br>  "chmod"、<br>  "mkdir"、<br>  avl<br>]|
|user2|[<br>  "pwd"、<br>  ws-rm<br>]|

> [!Note]
> データに値を含めることができる場合は `null` 、 [make_list](makelist-aggfunction.md)ではなく[make_list_with_nulls](make-list-with-nulls-aggfunction.md)を使用します。

## <a name="example-4---controlling-location-of-null-values"></a>例 4-値の位置を制御する `null`

既定で `null` は、値は並べ替えられた配列の最後に配置されます。 ただし、の最後の引数として値を追加することで、明示的に制御でき `bool` `array_sort_desc()` ます。

既定の動作を使用する例:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print array_sort_desc(dynamic([null,"blue","yellow","green",null]))
```

|`print_0`|
|---|
|["黄"、"緑"、"青"、null、null]|

既定以外の動作の例:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print array_sort_desc(dynamic([null,"blue","yellow","green",null]), false)
```

|`print_0`|
|---|
|[null、null、"黄"、"green"、"blue"]|

## <a name="see-also"></a>こちらもご覧ください

最初の配列を昇順に並べ替えるには、 [array_sort_asc ()](arraysortascfunction.md)を使用します。
