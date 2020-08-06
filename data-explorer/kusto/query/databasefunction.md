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
ms.openlocfilehash: 6511006373cd1f6245a0dcc04537f3994183d63e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803762"
---
# <a name="database-scope-function"></a>database () (スコープ関数)

::: zone pivot="azuredataexplorer"

クエリの参照をクラスタースコープ内の特定のデータベースに変更します。 

```kusto
database('Sample').StormEvents
cluster('help').database('Sample').StormEvents
```

> [!NOTE]
> * 詳細については、「[データベース間およびクロスクラスタークエリ](cross-cluster-or-database-queries.md)」を参照してください。
> * リモートクラスターおよびリモートデータベースにアクセスするには、「 [cluster ()](clusterfunction.md) scope 関数」を参照してください。

## <a name="syntax"></a>構文

`database(`*stringConstant*`)`

## <a name="arguments"></a>引数

* *Stringconstant*: 参照されているデータベースの名前。 識別されるデータベースに `DatabaseName` は、またはを指定でき `PrettyName` ます。 引数は、クエリを実行する前に_定数_にする必要があります。つまり、サブクエリの評価から取得することはできません。

## <a name="examples"></a>例

### <a name="use-database-to-access-table-of-other-database"></a>データベース () を使用して、他のデータベースのテーブルにアクセスする

```kusto
database('Samples').StormEvents | count
```

|Count|
|---|
|59066|

### <a name="use-database-inside-let-statements"></a>Let ステートメント内で database () を使用する 

データベース () 関数に渡されるパラメーターを受け取るインライン関数 (let ステートメント) を使用するように、上記と同じクエリを書き換えることができ `dbName` ます。

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

上記と同じクエリは、 `dbName` データベース () 関数に渡されるパラメーターを受け取る関数で使用するように書き直すことができます。

```kusto
.create function foo(dbName:string)
{
    database(dbName).StormEvents | count
};
```

> [!NOTE]
> このような関数は、クラスター間クエリではなくローカルでのみ使用できます。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
