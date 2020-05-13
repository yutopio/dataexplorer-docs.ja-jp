---
title: table () (スコープ関数)-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの table () (スコープ関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 0d5f44d621612e90d83a93f2f5831630520d4ba0
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83371750"
---
# <a name="table-scope-function"></a>table () (スコープ関数)

Table () 関数は、型の式として名前を指定することで、テーブルを参照し `string` ます。

```kusto
table('StormEvent')
```

**構文**

`table``(` *TableName* [ `,` *datascope*]`)`

**引数**

::: zone pivot="azuredataexplorer"

* *TableName*: `string` 参照されているテーブルの名前を提供する型の式。 この式の値は、関数の呼び出しの時点で定数である必要があります (つまり、データコンテキストによって異なることはありません)。

* *Datascope*: テーブル `string` の有効な[キャッシュポリシー](../management/cachepolicy.md)でのデータの分類に従ってテーブル参照をデータに制限するために使用できる、型の省略可能なパラメーター。 使用する場合、実際の引数は、 `string` 次のいずれかの値を持つ定数式である必要があります。

    - `"hotcache"`: ホットキャッシュとして分類されたデータのみが参照されます。
    - `"all"`: テーブル内のすべてのデータが参照されます。
    - `"default"`: クラスター `"all"` `"hotcache"` 管理者によって既定値として使用するように設定されている場合を除き、これはと同じです。

::: zone-end

::: zone pivot="azuremonitor"

* *TableName*: `string` 参照されているテーブルの名前を提供する型の式。 この式の値は、関数の呼び出しの時点で定数である必要があります (つまり、データコンテキストによって異なることはありません)。

* *Datascope*: テーブル `string` の有効なキャッシュポリシーでのデータの分類に従ってテーブル参照をデータに制限するために使用できる、型の省略可能なパラメーター。 使用する場合、実際の引数は、 `string` 次のいずれかの値を持つ定数式である必要があります。

    - `"hotcache"`: ホットキャッシュとして分類されたデータのみが参照されます。
    - `"all"`: テーブル内のすべてのデータが参照されます。
    - `"default"`: これはと同じ `"all"` です。

::: zone-end

## <a name="examples"></a>例

### <a name="use-table-to-access-table-of-the-current-database"></a>Table () を使用して、現在のデータベースのテーブルにアクセスする

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
table('StormEvent') | count
```

|Count|
|---|
|59066|

### <a name="use-table-inside-let-statements"></a>Let ステートメント内で table () を使用する

テーブル () 関数に渡されるパラメーターを受け取るインライン関数 (let ステートメント) を使用するように、上記と同じクエリを書き換えることができ `tableName` ます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo = (tableName:string)
{
    table(tableName) | count
};
foo('help')
```

|Count|
|---|
|59066|

### <a name="use-table-inside-functions"></a>関数内で table () を使用する

上記と同じクエリは、 `tableName` table () 関数に渡されるパラメーターを受け取る関数で使用するように書き直すことができます。

```kusto
.create function foo(tableName:string)
{
    table(tableName) | count
};
```

::: zone pivot="azuredataexplorer"

**注:** このような関数は、クラスター間クエリではなくローカルでのみ使用できます。

::: zone-end

### <a name="use-table-with-non-constant-parameter"></a>定数以外のパラメーターを指定して table () を使用する

スカラー定数文字列ではないパラメーターを、関数にパラメーターとして渡すことはできません `table()` 。

次に、このような場合の回避策の例を示します。

```kusto
let T1 = print x=1;
let T2 = print x=2;
let _choose = (_selector:string)
{
    union
    (T1 | where _selector == 'T1'),
    (T2 | where _selector == 'T2')
};
_choose('T2')

```

|x|
|---|
|2|
