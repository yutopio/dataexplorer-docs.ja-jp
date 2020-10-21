---
title: cluster () (スコープ関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの cluster () (スコープ関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b2f9dd3fd3eede1f6d527c97b905353f9a331620
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253062"
---
# <a name="cluster-scope-function"></a>cluster () (スコープ関数)

::: zone pivot="azuredataexplorer"

クエリの参照をリモートクラスターに変更します。 

```kusto
cluster('help').database('Sample').SomeTable
```

## <a name="syntax"></a>構文

`cluster(`*stringConstant*`)`

## <a name="arguments"></a>引数

* *Stringconstant*: 参照されているクラスターの名前。 クラスター名には、完全修飾 DNS 名か、サフィックスが付けられた文字列を指定でき `.kusto.windows.net` ます。 引数は、クエリの実行前には _定数_ にする必要があります。つまり、サブクエリの評価から取得することはできません。

**ノート**

* 同じクラスター内のデータベースにアクセスする場合は、 [database ()](databasefunction.md) 関数を使用します。
* クラスター間およびデータベース間のクエリの詳細について[は、こちら](cross-cluster-or-database-queries.md)を参照してください。  

## <a name="examples"></a>例

### <a name="use-cluster-to-access-remote-cluster"></a>クラスター () を使用してリモートクラスターにアクセスする 

次のクエリは、任意の Kusto クラスターで実行できます。

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|Count|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>Let ステートメント内で cluster () を使用する 

上記と同じクエリは、 `clusterName` cluster () 関数に渡されるパラメーターを受け取るインライン関数 (let ステートメント) を使用するように書き換えることができます。

```kusto
let foo = (clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
foo('help')
```

|Count|
|---|
|59066|

### <a name="use-cluster-inside-functions"></a>Functions 内で cluster () を使用する 

上記と同じクエリは、 `clusterName` cluster () 関数に渡されるパラメーターを受け取る関数で使用するように書き直すことができます。

```kusto
.create function foo(clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
```

**注:** このような関数は、クラスター間クエリではなくローカルでのみ使用できます。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
