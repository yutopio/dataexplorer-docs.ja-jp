---
title: Azure Advisor の推奨事項を使用して Azure Data Explorer クラスターを最適化する
description: この記事では、Azure Data Explorer クラスターを最適化するために使用される Azure Advisor の推奨事項について説明します
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: lizlotor
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/14/2020
ms.openlocfilehash: c071e7d14005a7966e79f3629fe52c9c33324547
ms.sourcegitcommit: c140bc3bc984f861df0b85e672d2e685e6659a54
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2020
ms.locfileid: "90109868"
---
# <a name="use-azure-advisor-recommendations-to-optimize-your-azure-data-explorer-cluster-preview"></a>Azure Advisor の推奨事項を使用して Azure Data Explorer クラスターを最適化する (プレビュー)

Azure Advisor は、Azure Data Explorer クラスターの構成と使用状況テレメトリを分析し、クラスターの最適化に役立つ、パーソナライズされたアクションにつながる推奨事項を提供します。

## <a name="access-the-azure-advisor-recommendations"></a>Azure Advisor の推奨事項にアクセスする

Azure Advisor の推奨事項にアクセスするには、2 つの方法があります。

### <a name="view-azure-advisor-recommendations-for-your-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターへの Azure Advisor の推奨事項を表示する

1. Azure portal で、Azure Data Explorer クラスターのページに移動します。 
1. 左側のメニューの **[監視]** で、 **[Advisor recommendations]\(Advisor の推奨事項\)** を選択します。 そのクラスターについての推奨事項の一覧が表示されます。

    :::image type="content" source="media/azure-advisor/resource-group-advisor-recommendations.png" alt-text="Azure Data Explorer クラスターについての Azure Advisor の推奨事項"::: 

### <a name="view-azure-advisor-recommendations-for-all-clusters-in-your-subscription"></a>サブスクリプション内のすべてのクラスターについての Azure Advisor の推奨事項を表示する

1. Azure portal で、[Advisor リソース](https://ms.portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview)に移動します。 
1. **[概要]** で、推奨事項を表示するサブスクリプションを選択します。 
1. 2 番目のドロップダウンで **[Azure Data Explorer クラスター]** と **[Azure Data Explorer データベース]** を選択します。
 
    :::image type="content" source="media/azure-advisor/advisor-resource.png" alt-text="Azure Advisor リソース":::

## <a name="use-the-azure-advisor-recommendations"></a>Azure Advisor の推奨事項を使用する

Azure Advisor の推奨事項にはさまざまな種類があります。 クラスターを最適化できるように、適切な推奨事項の種類を使用します。 

1. コストに関する推奨事項については、**Advisor** で、 **[推奨]** の下の **[コスト]** を選択します。

    :::image type="content" source="media/azure-advisor/select-recommendation-type.png" alt-text="推奨事項の種類の選択":::

1. 一覧から推奨事項を選択します。 

    :::image type="content" source="media/azure-advisor/select-recommendation.png" alt-text="推奨事項の選択":::

1. 次のウィンドウには、推奨事項に関連するクラスターの一覧が表示されています。 推奨事項の詳細はクラスターごとに異なり、推奨されるアクションが含まれています。

    :::image type="content" source="media/azure-advisor/clusters-with-recommendations.png" alt-text="推奨事項のあるクラスターの一覧":::

## <a name="recommendation-types"></a>推奨事項の種類

現在、コストとパフォーマンスの両方に関する推奨事項を使用できます。

> [!IMPORTANT]
> 実際の年間削減額とは異なる場合があります。 表示される年間削減額は、"従量課金制" の価格に基づいています。 潜在的な削減額では、予約仮想マシン インスタンス (RI) の課金の割引が考慮されていません。

### <a name="cost-recommendations"></a>コストに関する推奨事項

**コスト**に関する推奨事項は、パフォーマンスを損なうことなくコストを削減するために変更できるクラスターで使用できます。 コストに関する推奨事項は次のとおりです。 

* [未使用の Azure Data Explorer クラスター](#azure-data-explorer-unused-cluster) 
* [コストを最適化するために Azure Data Explorer クラスターのサイズを適切に設定する](#correctly-size-azure-data-explorer-clusters-to-optimize-cost)
* [Azure Data Explorer のテーブルに対するキャッシュを減らす](#reduce-cache-for-azure-data-explorer-tables)

#### <a name="azure-data-explorer-unused-cluster"></a>未使用の Azure Data Explorer クラスター

クラスターは、過去 30 日間のデータ、クエリー、インジェスト イベントが少量であり、過去 2 日間の CPU 使用率が低く、過去 1 日間にフォロワーがいない場合、未使用と見なされます。 推奨事項 **[consider deleting empty / unused clusters]\(空または未使用のクラスターの削除を検討する\)** には、未使用クラスターを削除するための推奨されるアクションが表示されます。

#### <a name="correctly-size-azure-data-explorer-clusters-to-optimize-cost"></a>コストを最適化するために Azure Data Explorer クラスターのサイズを適切に設定する

推奨事項 **[right-size Azure Data Explorer clusters for optimal cost]\(最適なコストを実現するために Azure Data Explorer クラスターのサイズを適切に設定する\)** は、サイズまたは VM SKU がコストに最適化されていないクラスターに対して提供されます。 この推奨事項は、過去 1 週間のそのデータ容量、CPU とインジェストの使用率などのパラメーターに基づいています。 [スケールダウン](manage-cluster-vertical-scaling.md)と[スケールイン](manage-cluster-horizontal-scaling.md)を使用して、推奨されるクラスター構成にサイズ変更することで、コストを削減できます。

[最適化された自動スケーリング構成](manage-cluster-horizontal-scaling.md#optimized-autoscale)を使用することをお勧めします。 最適化された自動スケーリングを使用していて、クラスターに対してサイズに関する推奨事項が表示される場合は、現在の VM SKU、または最適化された自動スケーリングの最小および最大インスタンス数の境界のいずれかが最適化されていません。 推奨されるインスタンス数は定義した境界内に含まれている必要があります。

> [!TIP]
> 最適化された自動スケーリングによって、インスタンス数が直ちに変更されることはありません。 直ちに変更するには、[手動スケーリング](manage-cluster-horizontal-scaling.md#manual-scale)を使用して推奨されるインスタンス数をリセットし、今後の最適化のために、最適化された自動スケーリングを有効にします。

#### <a name="reduce-cache-for-azure-data-explorer-tables"></a>Azure Data Explorer のテーブルに対するキャッシュを減らす

推奨事項 **[reduce Azure Data Explorer table cache period for cluster cost optimization]\(クラスターのコスト最適化のために Azure Data Explorer テーブルのキャッシュ期間を減らす\)** は、テーブルのキャッシュ ポリシーを減らすことができるクラスターに対して提供されます。 この推奨事項は、過去 30 日間のクエリーのルックバック期間に基づいています。 キャッシュを削減できる可能性のある上位 10 個のテーブルが表示されます。 この推奨事項は、ポリシーの変更に従ってスラスターをスケールインまたはスケールダウンできる場合にのみ提供されます。 Advisor は、クラスターが "データによって制限される" かどうかを確認します。これは、クラスターの CPU およびインジェスト使用率が低いが、データ容量が高いため、クラスターをスケールインまたはスケールダウンできないことを意味します。

### <a name="performance-recommendations"></a>パフォーマンスに関する推奨事項

**パフォーマンス**に関する推奨事項は、Azure Data Explorer クラスターのパフォーマンスの向上に役立ちます。 パフォーマンスに関する推奨事項は次のとおりです。 
* [パフォーマンスを最適化するために Azure Data Explorer クラスターのサイズを適切に設定する](#correctly-size-azure-data-explorer-clusters-to-optimize-performance)
* [Azure Data Explorer のテーブルのキャッシュ ポリシーを更新する](#update-cache-policy-for-azure-data-explorer-tables)

#### <a name="correctly-size-azure-data-explorer-clusters-to-optimize-performance"></a>パフォーマンスを最適化するために Azure Data Explorer クラスターのサイズを適切に設定する

推奨事項 **[right-size Azure Data Explorer clusters for optimal performance]\(最適なパフォーマンスを実現するために Azure Data Explorer クラスターのサイズを適切に設定する\)** は、サイズまたは VM SKU がパフォーマンスに最適化されていないクラスターに対して提供されます。 この推奨事項は、過去 1 週間のそのデータ容量、CPU とインジェストの使用率などのパラメーターに基づいています。 [スケールアップ](manage-cluster-vertical-scaling.md)と[スケールアウト](manage-cluster-horizontal-scaling.md)を使用して、推奨されるクラスター構成に適切にサイズ設定することで、パフォーマンスを向上させることができます。

[最適化された自動スケーリング構成](manage-cluster-horizontal-scaling.md#optimized-autoscale)を使用することをお勧めします。 最適化された自動スケーリングを使用していて、クラスターに対してサイズに関する推奨事項が表示される場合は、現在の VM SKU、または最適化された自動スケーリングの最小および最大インスタンス数の境界のいずれかが最適化されていません。 推奨されるインスタンス数は定義した境界内に含まれている必要があります。

> [!TIP]
> 最適化された自動スケーリングによって、インスタンス数が直ちに変更されることはありません。 直ちに変更するには、[手動スケーリング](manage-cluster-horizontal-scaling.md#manual-scale)を使用して推奨されるインスタンス数をリセットし、今後の最適化のために、最適化された自動スケーリングを有効にします。

#### <a name="update-cache-policy-for-azure-data-explorer-tables"></a>Azure Data Explorer のテーブルのキャッシュ ポリシーを更新する

推奨事項 **[review Azure Data Explorer table cache-period (policy) for better performance]\(パフォーマンス向上のために Azure Data Explorer テーブルのキャッシュ期間 (ポリシー) を確認する\)** は、異なるルックバック期間 (時間フィルター) またはより大きいキャッシュ ポリシーを必要とするクラスターに対して提供されます。 この推奨事項は、過去 30 日間のクエリーのルックバック期間に基づいています。 過去 30 日間に実行されたクエリのほとんどは、キャッシュに含まれていないデータにアクセスしました。これにより、クエリの実行時間が長くなる可能性があります。  キャッシュ外のデータにアクセスした上位 10 のテーブルが、クエリの割合順に表示されます。

## <a name="next-steps"></a>次のステップ

* [需要の変化に対応するために Azure Data Explorer のクラスターの水平スケーリング (スケールアウト) を管理する](manage-cluster-horizontal-scaling.md)
* [需要の変化に対応するために Azure Data Explorer のクラスターの垂直スケーリング (スケールアップ) を管理する](manage-cluster-vertical-scaling.md)