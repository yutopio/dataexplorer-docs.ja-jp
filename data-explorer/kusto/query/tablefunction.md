---
title: テーブル()(スコープ関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの table() (スコープ関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 233baebaf51cdc8b07cdd32cec9bd10a54695c1c
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766160"
---
# <a name="table-scope-function"></a>テーブル()(スコープ関数)

table() 関数は、その名前を type の式として指定`string`してテーブルを参照します。

```kusto
table('StormEvent')
```

**構文**

`table``(`*テーブル名*`,` [*データスコープ*]`)`

**引数**

::: zone pivot="azuredataexplorer"

* *テーブル名*: 参照される`string`テーブルの名前を提供する型の式。 この式の値は、関数の呼び出し時点で定数である必要があります (つまり、データ コンテキストによって変化することはできません)。

* *DataScope*: このデータが`string`テーブルの有効[なキャッシュ ポリシー](../management/cachepolicy.md)に該当する方法に従って、テーブル参照をデータに制限するために使用できる、型のオプション のパラメータです。 使用する場合、実引数は、次のいずれかの`string`値を持つ定数式でなければなりません。

    - `"hotcache"`: ホット キャッシュとして分類されたデータのみが参照されます。
    - `"all"`: テーブル内のすべてのデータが参照されます。
    - `"default"`: これは、クラスター管理者`"all"`がデフォルトとして使用`"hotcache"`するように設定されている場合を除き、 と同じです。

::: zone-end

::: zone pivot="azuremonitor"

* *テーブル名*: 参照される`string`テーブルの名前を提供する型の式。 この式の値は、関数の呼び出し時点で定数である必要があります (つまり、データ コンテキストによって変化することはできません)。

* *DataScope*: このデータが`string`テーブルの有効なキャッシュ ポリシーに含まれる方法に従って、テーブル参照をデータに制限するために使用できる、型のオプション のパラメーター。 使用する場合、実引数は、次のいずれかの`string`値を持つ定数式でなければなりません。

    - `"hotcache"`: ホット キャッシュとして分類されたデータのみが参照されます。
    - `"all"`: テーブル内のすべてのデータが参照されます。
    - `"default"`: これは と同`"all"`じです。

::: zone-end

## <a name="examples"></a>例

### <a name="use-table-to-access-table-of-the-current-database"></a>table() を使用して現在のデータベースのテーブルにアクセスする

```kusto
table('StormEvent') | count
```

|Count|
|---|
|59066|

### <a name="use-table-inside-let-statements"></a>let ステートメント内で table() を使用する

上記と同じクエリを書き直して、table() 関数に渡されるパラメータ`tableName`を受け取るインライン関数 (let 文) を使用できます。

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

### <a name="use-table-inside-functions"></a>関数内で table() を使用する

上記と同じクエリを書き直して、table() 関数に渡されるパラメータ`tableName`を受け取る関数で使用することができます。

```kusto
.create function foo(tableName:string)
{
    table(tableName) | count
};
```

::: zone pivot="azuredataexplorer"

**注:** このような関数はローカルでのみ使用でき、クロスクラスター照会では使用できません。

::: zone-end

### <a name="use-table-with-non-constant-parameter"></a>非定数パラメータを指定して table() を使用する

スカラー定数文字列ではないパラメータは、関数に`table()`パラメータとして渡されません。

以下に、このような場合の回避策の例を示します。

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
