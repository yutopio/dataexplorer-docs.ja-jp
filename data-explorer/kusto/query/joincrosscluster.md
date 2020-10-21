---
title: クロスクラスター参加-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのクロスクラスター参加について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: a7c8f89886a8c12941dbc218ad69b35eebd7f1c7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246584"
---
# <a name="cross-cluster-join"></a>クラスター間の結合

::: zone pivot="azuredataexplorer"

クロスクラスタークエリの一般的な説明について[は、「クラスター間またはデータベース間のクエリ](cross-cluster-or-database-queries.md)」を参照してください。

異なるクラスターに存在するデータセットに対して結合操作を行うことができます。 次に例を示します。

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

上の例では、結合操作はクラスター間結合であり、現在のクラスターが "SomeCluster" または "SomeCluster2" ではないと仮定しています。

次の例では

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

結合操作はクラスター間結合ではありません。両方のオペランドが同じクラスター上で発生しています。

Kusto がクラスター間の結合を検出すると、結合操作自体を実行する場所が自動的に決定されます。 この決定には、次の3つの結果のいずれかが考えられます。

* 左側のオペランドのクラスターで結合操作を実行すると、右オペランドがこのクラスターによって最初にフェッチされます。 (例では join **(1)** はローカルクラスターで実行されます)
* 右オペランドのクラスターで join 操作を実行すると、左側のオペランドがこのクラスターによって最初にフェッチされます。 (例では join **(2)** が "SomeCluster2" で実行されます)
* 結合操作をローカルで実行する (つまり、クエリを受け取ったクラスターで) ローカルクラスターによって、両方のオペランドが最初にフェッチされます。

実際の決定は、特定のクエリによって異なります。 自動結合リモート処理戦略は (簡略化されたバージョン) です。 "オペランドの1つがローカルの場合、結合はローカルで実行されます。 両方のオペランドがリモートの場合は、右側のオペランドのクラスターで join が実行されます。

自動リモート処理戦略に従わないと、クエリのパフォーマンスが向上する場合があります。 この場合、最大のオペランドのクラスターで結合操作を実行します。

例 **(1)** の場合、によって生成されるデータセット `T | ...` は、によって生成されるデータセットよりもはるかに小さいため `cluster("SomeCluster").database("SomeDB").T2 | ...` 、"SomeCluster" で結合操作を実行する方が効率的です。

この操作は、Kusto join リモート処理ヒントを指定することによって行うことができます。 の構文は次のとおりです。

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

に有効な値を次に示します。 `strategy`
* `left` -左オペランドのクラスターで join を実行します 
* `right` -右オペランドのクラスターで join を実行します
* `local` -現在のクラスターのクラスターで join を実行します
* `auto` -(既定) Kusto で自動リモート処理を決定します。

> [!Note]
> ヒント戦略が結合操作に適用されない場合、join remoting ヒントは Kusto によって無視されます。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
