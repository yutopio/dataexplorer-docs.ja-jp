---
title: database () (スコープ関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの database () (スコープ関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 03753cf7dcabb7637335d3dc71f0b9bca773e710
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030448"
---
# <a name="database-scope-function"></a>database () (スコープ関数)

::: zone pivot="azuredataexplorer"

クエリの参照をクラスタースコープ内の特定のデータベースに変更します。 

```kusto
database('Sample').StormEvents
cluster('help').database('Sample').StormEvents
```

**構文**

`database(`*stringConstant*`)`

**引数**

* *Stringconstant*: 参照されているデータベースの名前。 識別されるデータベースに`DatabaseName`は`PrettyName`、またはを指定できます。 引数は、クエリを実行する前に_定数_にする必要があります。つまり、サブクエリの評価から取得することはできません。

**メモ**

* リモートクラスターおよびリモートデータベースにアクセスするには、「 [cluster ()](clusterfunction.md) scope 関数」を参照してください。
* クラスター間およびデータベース間のクエリの詳細について[は、こちら](cross-cluster-or-database-queries.md)を参照してください。

## <a name="examples"></a>例

### <a name="use-database-to-access-table-of-other-database"></a>他のデータベースのテーブルにアクセスするには、database () を使用します。 

```kusto
database('Samples').StormEvents | count
```

|Count|
|---|
|59066|

### <a name="use-database-inside-let-statements"></a>Let ステートメント内で database () を使用する 

データベース () 関数に渡されるパラメーター `dbName`を受け取るインライン関数 (let ステートメント) を使用するように、上記と同じクエリを書き換えることができます。

```kusto
let foo = (dbName:string)
{
    database(dbName).StormEvents | count
};
foo('help')
```

|Count|
|---|
|59066|

### <a name="use-database-inside-functions"></a>関数内で database () を使用する 

上記と同じクエリは、データベース () 関数に渡されるパラメーター `dbName`を受け取る関数で使用するように書き直すことができます。

```kusto
.create function foo(dbName:string)
{
    database(dbName).StormEvents | count
};
```

**注:** このような関数は、クラスター間クエリではなくローカルでのみ使用できます。

::: zone-end

::: zone pivot="azuremonitor"

これは、ではサポートされていません Azure Monitor

::: zone-end
