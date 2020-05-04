---
title: クロスクラスター参加-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのクロスクラスター参加について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: bd2ebaa35de1997a96c6646c0fe0f7e248af2240
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737319"
---
# <a name="cross-cluster-join"></a>クラスター間結合

::: zone pivot="azuredataexplorer"

クロスクラスタークエリに関する一般的な説明については、「[クラスター間またはデータベース間のクエリ](cross-cluster-or-database-queries.md)」を参照してください。

異なるクラスターに存在するデータセットに対して結合操作を実行することができます。 次に例を示します。 

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

上の例では、現在のクラスターが "SomeCluster" でも "SomeCluster2" でもないという前提で、結合操作はクロスクラスター結合です。

次の例では、に注意してください。

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

結合操作はクラスター間結合ではありません。両方のオペランドが同じクラスター上で発生しています。

Kusto がクラスター間結合を検出すると、結合操作自体を実行する場所が自動的に決定されます。 この決定には、次の3つの結果のいずれかが考えられます。
* 左側のオペランドのクラスターで結合操作を実行すると、右オペランドがこのクラスターによって最初にフェッチされます。 (例では join **(1)** はローカルクラスターで実行されます)
* 右オペランドのクラスターで join 操作を実行すると、左側のオペランドがこのクラスターによって最初にフェッチされます。 (例では join **(2)** が "SomeCluster2" で実行されます)
* ローカルクラスターで結合操作をローカルに実行する (つまり、クエリを受け取ったクラスターで)。両方のオペランドがローカルクラスターによって最初にフェッチされます。

実際の決定は、特定のクエリによって異なります。自動結合リモート処理戦略は (簡略化されたバージョン) です。 "いずれかのオペランドがローカルの場合、結合はローカルで実行されます。 両方のオペランドがリモート結合の場合は、右側のオペランドのクラスターで実行されます。 "です。

自動リモート処理戦略に従わないと、クエリのパフォーマンスが大幅に向上することがあります。 一般に、最大のオペランドのクラスターで結合操作を実行する場合は (パフォーマンスの観点から) 最適です。

例 **(1)** の場合、によって```T | ...```生成されるデータセットは、 ```cluster("SomeCluster").database("SomeDB").T2 | ...``` "SomeCluster" で結合操作を実行するよりもはるかに小さくなります。

これは、Kusto join リモート処理ヒントを指定することによって実現できます。 の構文は次のとおりです。

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

に有効な値を次に示します。*`strategy`*
* **`left`**-左オペランドのクラスターで join を実行します 
* **`right`**-右オペランドのクラスターで join を実行します
* **`local`**-現在のクラスターのクラスターで join を実行します
* **`auto`**-(既定) Kusto で自動リモート処理を決定します。

**注:** 結合操作にヒント戦略が適用されない場合、join remoting ヒントは Kusto によって無視されます。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
