---
title: クラスター() (スコープ関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの cluster() (スコープ関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c5f537b47006af4035c9db26388c1d4110c69b55
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766110"
---
# <a name="cluster-scope-function"></a>クラスター()(スコープ関数)

::: zone pivot="azuredataexplorer"

クエリの参照をリモート クラスターに変更します。 

```kusto
cluster('help').database('Sample').SomeTable
```

**構文**

`cluster(`*文字列定数*`)`

**引数**

* *文字列定数*: 参照されるクラスターの名前。 クラスタ名には、完全修飾 DNS 名、または サフィックスが付いた文字列を`.kusto.windows.net`指定できます。 引数はクエリの実行前に_定数_である必要があります。

**メモ**

* 同じクラスタ内のデータベースにアクセスする場合は[、database()](databasefunction.md)関数を使用します。
* クラスタ間クエリとクロスデータベース クエリの詳細[については、こちらを参照してください。](cross-cluster-or-database-queries.md)  

## <a name="examples"></a>例

### <a name="use-cluster-to-access-remote-cluster"></a>cluster() を使用してリモート・クラスターにアクセスする 

次のクエリは、Kusto クラスターのいずれかで実行できます。

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|Count|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>let ステートメント内で cluster() を使用する 

上記と同じクエリを書き直して、cluster() 関数に渡されるパラメータ`clusterName`を受け取るインライン関数 (let 文) を使用するように書き換えることができます。

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

### <a name="use-cluster-inside-functions"></a>関数内で cluster() を使用する 

上記と同じクエリを書き直して、パラメータ`clusterName`を受け取る関数で使用することができます 。

```kusto
.create function foo(clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
```

**注:** このような関数はローカルでのみ使用でき、クロスクラスター照会では使用できません。

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
