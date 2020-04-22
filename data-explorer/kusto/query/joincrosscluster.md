---
title: クロスクラスター結合 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクロスクラスター参加について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1199b148fa295ac17417bbf590a73bfc9400a710
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765939"
---
# <a name="cross-cluster-join"></a>クロスクラスタ結合

::: zone pivot="azuredataexplorer"

クラスタ間クエリに関する一般的な説明については、[クロスクラスタ クエリまたはクロスデータベース クエリを](cross-cluster-or-database-queries.md)参照してください。

異なるクラスターに存在するデータセットに対して結合操作を実行できます。 次に例を示します。 

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

上記の例では、現在のクラスタが "SomeCluster" でも "SomeCluster2" でもないと仮定した場合のクラスタ間結合があります。

次の例では、注意してください。

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

両方のオペランドが同じクラスター上にあるため、join 操作はクラスター間結合ではありません。

Kusto がクロスクラスター結合を検出すると、結合操作自体を実行する場所が自動的に決定されます。 この決定は、次の 3 つの結果のうちの 1 つを持つことができます。
* 左オペランドのクラスタで結合操作を実行すると、右オペランドはこのクラスタによって最初にフェッチされます。 (例で参加 **(1)** はローカル クラスターで実行されます)
* 右オペランドのクラスタで結合操作を実行すると、左オペランドはこのクラスタによって最初にフェッチされます。 (例で参加 **(2)** は"SomeCluster2"で実行されます。
* ローカルでの結合操作の実行 (クエリを受け取ったクラスター上での操作) は、両方のオペランドが最初にローカル クラスターによってフェッチされます。

実際の決定は特定のクエリに依存し、自動結合リモート処理戦略は (簡略化されたバージョン): "オペランドの 1 つがローカルの場合、結合はローカルで実行されます。 両方のオペランドがリモート結合の場合は、右オペランドのクラスタで実行されます。

自動リモート処理の方法に従わない場合、クエリのパフォーマンスが大幅に向上する場合があります。 一般的に言うと、最大オペランドのクラスタで (パフォーマンスの観点から) 結合操作を実行するのが最適です。

例えば **(1)** で生成されたデータセット```T | ...```が```cluster("SomeCluster").database("SomeDB").T2 | ...```、"SomeCluster"で結合操作を実行する方が効率的です。

これは、Kusto がリモート処理のヒントに参加することで実現できます。 の構文は次のとおりです。

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

以下は、法的な値です。*`strategy`*
* **`left`**- 左オペランドのクラスタで結合を実行する 
* **`right`**- 右オペランドのクラスタで結合を実行する
* **`local`**- 現在のクラスタのクラスタで参加を実行
* **`auto`**- (デフォルト)クストに自動リモート処理の決定をさせる

**注:** 結合操作にヒントを与えるストラテジーが適用されない場合、Join リモート処理ヒントは Kusto によって無視されます。

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
