---
title: cluster () (スコープ関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの cluster () (スコープ関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 092915c08b4b3d1e72722a4303e911403b2defd2
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737200"
---
# <a name="cluster-scope-function"></a>cluster () (スコープ関数)

::: zone pivot="azuredataexplorer"

クエリの参照をリモートクラスターに変更します。 

```kusto
cluster('help').database('Sample').SomeTable
```

**構文**

`cluster(`*stringConstant*`)`

**引数**

* *Stringconstant*: 参照されているクラスターの名前。 クラスター名には、完全修飾 DNS 名か、サフィックスが付け`.kusto.windows.net`られた文字列を指定できます。 引数は、クエリの実行前には_定数_にする必要があります。つまり、サブクエリの評価から取得することはできません。

**メモ**

* 同じクラスター内のデータベースにアクセスする場合は、 [database ()](databasefunction.md)関数を使用します。
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

上記と同じクエリは、cluster () 関数に渡されるパラメーター `clusterName`を受け取るインライン関数 (let ステートメント) を使用するように書き換えることができます。

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

上記と同じクエリは、cluster () 関数に渡されるパラメーター `clusterName`を受け取る関数で使用するように書き直すことができます。

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
