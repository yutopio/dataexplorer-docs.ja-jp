---
title: Azure Data Explorer を使用して事業継続とディザスター リカバリー ソリューションを作成する
description: この記事では、Azure Data Explorer を使用して事業継続とディザスター リカバリー ソリューションを作成する方法について説明します
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 0b37d65d095806d108a632c315820d164f45a520
ms.sourcegitcommit: 39a055e246539ac64d651abb42531892dd4393e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88028896"
---
# <a name="create-business-continuity-and-disaster-recovery-solutions-with-azure-data-explorer"></a>Azure Data Explorer を使用して事業継続とディザスター リカバリー ソリューションを作成する

この記事では、さまざまな Azure リージョンに Azure Data Explorer のリソース、管理、インジェストをレプリケートすることによって、Azure リージョンの停止に備える方法について詳しく説明します。 Event Hub を使用したデータ インジェストの例が示されています。 また、さまざまなアーキテクチャ構成のコストの最適化についても説明されています。 アーキテクチャに関する考慮事項とリカバリー ソリューションの詳細については、[事業継続の概要](business-continuity-overview.md)に関するページを参照してください。

## <a name="prepare-for-azure-regional-outage-to-protect-your-data"></a>データを保護するために Azure リージョンの停止に備える

Azure Data Explorer では、Azure リージョン全体の停止に対する自動保護はサポートされていません。 この停止は、地震などの自然災害時に発生する可能性があります。 ディザスター リカバリー状況に対処するためのソリューションが必要な場合は、次の手順を行って事業継続を確保します。 これらの手順では、ペアになっている 2 つの Azure リージョンにクラスター、管理、およびデータ インジェストをレプリケートします。

1. ペアになっている Azure リージョンに[複数の独立したクラスターを作成](#create-multiple-independent-clusters)します。
1. 新しいテーブルの作成や各クラスターでのユーザー ロールの管理など、[すべての管理アクティビティをレプリケート](#replicate-management-activities)します。
1. 各クラスターにデータを並列で取り込みます。

### <a name="create-multiple-independent-clusters"></a>複数の独立したクラスターを作成する

複数のリージョンに [Azure Data Explorer クラスター](create-cluster-database-portal.md)を複数作成します。
これらのクラスターのうち少なくとも 2 つが、[ペアになっている Azure リージョン](/azure/best-practices-availability-paired-regions)に作成されていることを確認します。

次の図は、3 つの異なるリージョンにあるレプリカの 3 つのクラスターを示しています。 

:::image type="content" source="media/business-continuity-create-solution/independent-clusters.png" alt-text="独立したクラスターを作成する":::

### <a name="replicate-management-activities"></a>管理アクティビティをレプリケートする

すべてのレプリカのクラスター構成が同じになるように、管理アクティビティをレプリケートします。

1. 各レプリカで以下の同じものを作成します。 
    * データベース: [Azure portal](create-cluster-database-portal.md#create-a-database)、または [SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/kusto/Microsoft.Azure.Management.Kusto) のいずれかを使用して、新しいデータベースを作成することができます。
    * [テーブル](kusto/management/create-table-command.md)
    * [マッピング](kusto/management/create-ingestion-mapping-command.md)
    * [ポリシー](kusto/management/policies.md)

1. 各レプリカの [認証と承認](kusto/management/security-roles.md)を管理します。

    :::image type="content" source="media/business-continuity-create-solution/regional-duplicate-management.png" alt-text="管理アクティビティを複製する":::    

### <a name="configure-data-ingestion"></a>データ インジェストを構成する

すべてのクラスターで一貫してデータ インジェストを構成します。 次のインジェスト方法では、以下の高度な事業継続機能が使用されます。

|インジェスト方法  |ディザスター リカバリー機能  |
|---------|---------|
|[Iot Hub](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr)  |[Microsoft が開始するフェールオーバーと手動フェールオーバー](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr) |
|[イベント ハブ](ingest-data-event-hub.md) | [プライマリおよびセカンダリ ディザスター リカバリーの名前空間](/azure/event-hubs/event-hubs-geo-dr)を使用したメタデータ ディザスター リカバリー     |
|[Event Grid サブスクリプションを使用したストレージからの取り込み](ingest-data-event-grid.md) |  Event Hub に送信される、BLOB によって作成されたメッセージの [geo ディザスター リカバリー](/azure/event-hubs/event-hubs-geo-dr)、および[ディザスター リカバリーとアカウントのフェールオーバー戦略](/azure/storage/common/storage-disaster-recovery-guidance)を実装します       |

## <a name="disaster-recovery-solution-using-event-hub-ingestion"></a>Event Hub インジェストを使用したディザスター リカバリー ソリューション

[データを保護するために Azure リージョンの停止に備える](#prepare-for-azure-regional-outage-to-protect-your-data)作業が完了すると、データと管理が複数のリージョンに分散されます。 1 つのリージョンで機能が停止した場合、Azure Data Explorer で他のレプリカを使用できます。 

### <a name="set-up-ingestion-using-event-hub"></a>Event Hub を使用したインジェストを設定する

次の例では、Event Hub を介したインジェストを使用します。 [フェールオーバー フロー](/azure/event-hubs/event-hubs-geo-dr#setup-and-failover-flow)が設定されており、Azure Data Explorer によってエイリアスからデータが取り込まれます。 クラスター レプリカごとに一意のコンシューマー グループを使用して、[Event Hub からデータを取り込みます](ingest-data-event-hub.md)。 そうしない場合は、トラフィックをレプリケートするのではなく、分散させることになります。

> [!NOTE] 
> Event Hub、IoT Hub、ストレージを介したインジェストは堅牢です。 クラスターが一定期間使用できない場合は、後でキャッチ アップされ、保留中のメッセージまたは BLOB が挿入されます。 このプロセスは、[チェックポイント処理](/azure/event-hubs/event-hubs-features#checkpointing)に依存します。

:::image type="content" source="media/business-continuity-create-solution/event-hub-management-scheme.png" alt-text="Event Hub を介して取り込む":::

下図に示すように、データ ソースにより、フェールオーバーで構成された Event Hub に対するイベントが生成され、各 Azure Data Explorer レプリカによってイベントが使用されます。 Power BI、Grafana、SDK を利用した WebApps などのデータ視覚化コンポーネントによって、いずれかのレプリカに対してクエリを実行することができます。

:::image type="content" source="media/business-continuity-create-solution/data-sources-visualization.png" alt-text="データ ソースからデータ視覚化まで":::

## <a name="optimize-costs"></a>コストを最適化する

これで、次の方法のいくつかを使用してレプリカを最適化できるようになりました。

* [アクティブ/ホット スタンバイ構成を作成する](#create-an-active-hot-standby-configuration)
* [レプリカを起動および停止する](#start-and-stop-the-replicas)
* [高可用性アプリケーション サービスを実装する](#implement-a-highly-available-application-service)
* [アクティブ/アクティブ構成でコストを最適化する](#optimize-cost-in-an-active-active-configuration)

### <a name="create-an-active-hot-standby-configuration"></a>アクティブ/ホット スタンバイ構成を作成する

Azure Data Explorer の設定をレプリケートして更新すると、レプリカの数に比例してコストが増加します。 コストを最適化する場合、時間、フェールオーバー、およびコストのバランスを取るためのアーキテクチャ バリアントを実装できます。
アクティブ/ホット スタンバイ構成では、パッシブな Azure Data Explorer レプリカを導入することによって、コストの最適化が実装されています。 これらのレプリカは、プライマリ リージョン (たとえば、リージョン A) で災害が発生した場合にのみ有効になります。 リージョン B および C のレプリカは毎日 24 時間アクティブである必要がなく、コストが大幅に削減されます。 しかし、ほとんどの場合、これらのレプリカのパフォーマンスはプライマリ クラスターほど良好ではありません。 詳細については、「[アクティブ/ホット スタンバイの構成](business-continuity-overview.md#active-hot-standby-configuration)」を参照してください。

下図では、1 つのクラスターによってのみ、Event Hub からデータが取り込まれています。 リージョン A のプライマリ クラスターによって、ストレージ アカウントへのすべてのデータの[継続的データ エクスポート](kusto/management/data-export/continuous-data-export.md)が行われます。 セカンダリ レプリカからは、[外部テーブル](kusto/query/schema-entities/externaltables.md)を使用してデータにアクセスすることができます。

:::image type="content" source="media/business-continuity-create-solution/active-hot-standby-scheme.png" alt-text="アクティブ/ホット スタンバイのアーキテクチャ":::

### <a name="start-and-stop-the-replicas"></a>レプリカを起動および停止する 

次のいずれかの方法を使用して、セカンダリ レプリカを起動および停止することができます。

* [Power Automate に接続する Azure Data Explorer コネクタ (プレビュー)](flow.md)

* Azure portal の **[概要]** タブにある **[停止]** ボタン。 詳細については、「[クラスターを停止して再起動する](create-cluster-database-portal.md#stop-and-restart-the-cluster)」を参照してください。

* Azure CLI: 

```kusto
az kusto cluster stop --name=<clusterName> --resource-group=<rgName> --subscription=<subscriptionId>” 
```

### <a name="implement-a-highly-available-application-service"></a>高可用性アプリケーション サービスを実装する

#### <a name="create-the-azure-app-service-bcdr-client"></a>Azure App Service BCDR クライアントを作成する

このセクションでは、1 つのプライマリと複数のセカンダリ Azure Data Explorer クラスターへの接続をサポートする [Azure App Service](https://azure.microsoft.com/services/app-service/) を作成する方法を示します。 下図は、Azure App Service の設定を示しています。

:::image type="content" source="media/business-continuity-create-solution/app-service-setup.png" alt-text="Azure App Service の作成":::

> [!TIP]
> 同じサービス内のレプリカ間に複数の接続を確立すると、可用性が向上します。 この設定は、リージョンの停止以外の場合でも役立ちます。  

1. [App Service ではこの定型コード](https://github.com/Azure/azure-kusto-bcdr-boilerplate)を使用します。 マルチクラスター クライアントを実装するために、[AdxBcdrClient](https://github.com/Azure/azure-kusto-bcdr-boilerplate/blob/master/webapp/ADX/AdxBcdrClient.cs) クラスが作成されています。 このクライアントを使用して実行される各クエリは、[最初にプライマリ クラスターに](https://github.com/Azure/azure-kusto-bcdr-boilerplate/blob/26f8c092982cb8a3757761217627c0e94928ee07/webapp/ADX/AdxBcdrClient.cs#L69)送信されます。 エラーが発生した場合、クエリはセカンダリ レプリカに送信されます。

1. [カスタムの Application Insights メトリック](/azure/azure-monitor/app/api-custom-events-metrics)を使用して、パフォーマンスを測定し、プライマリおよびセカンダリ クラスターへの分散を要求します。 

#### <a name="test-the-azure-app-service-bcdr-client"></a>Azure App Service BCDR クライアントをテストする

複数の Azure Data Explorer レプリカを使用してテストを行いました。 プライマリおよびセカンダリ クラスターの停止をシミュレートした後、App Service BCDR クライアントが意図したとおりに動作していることがわかります。

:::image type="content" source="media/business-continuity-create-solution/simulation-verify-service.png" alt-text="App Service BCDR クライアントを確認する":::

Azure Data Explorer クラスターは、西ヨーロッパ (2xD14v2 プライマリ)、東南アジア、および米国東部 (2xD11v2) に分散されています。 

:::image type="content" source="media/business-continuity-create-solution/performance-test-query-time.png" alt-text="世界にまたがるクエリの応答時間":::

> [!NOTE]
> 応答時間が長くなるのは、SKU が異なり、クエリが世界にまたがって実行されるためです。

#### <a name="perform-dynamic-or-static-routing"></a>動的または静的ルーティングを実行する

要求の動的または静的ルーティングでは、[Azure Traffic Manager のルーティング方法](/azure/traffic-manager/traffic-manager-routing-methods)を使用します。  Azure Traffic Manager は、App Service トラフィックを分散させることができる、DNS ベースのトラフィック ロード バランサーです。 このトラフィックは世界中の Azure リージョンにまたがるサービスに対して最適化され、同時に高可用性と応答性が提供されます。 

[Azure Front Door ベースのルーティング](/azure/frontdoor/front-door-routing-methods)を使用することもできます。 これら 2 つの方法の比較については、「[Azure のアプリケーション配信スイートでの負荷分散](/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)」を参照してください。

### <a name="optimize-cost-in-an-active-active-configuration"></a>アクティブ/アクティブ構成でコストを最適化する

ディザスター リカバリーにアクティブ/アクティブ構成を使用すると、コストが直線的に増加します。 コストには、ノード、ストレージ、マークアップ、および[帯域幅](https://azure.microsoft.com/pricing/details/bandwidth/)の追加のネットワーク コストが含まれます。

#### <a name="use-optimized-autoscale-to-optimize-costs"></a>最適化された自動スケーリングを使用してコストを最適化する

[最適化された自動スケーリング](manage-cluster-horizontal-scaling.md#optimized-autoscale)機能を使用して、セカンダリ クラスターの水平スケーリングを構成します。 これらは、インジェストの負荷を処理できるように、ディメンション化する必要があります。 プライマリ クラスターに到達できなくなると、セカンダリ クラスターによって、より多くのトラフィックが取得され、構成に応じてスケーリングされます。 

この例で最適化された自動スケーリングを使用することで、すべてのレプリカで同じ水平および垂直スケーリングを行う場合と比べ、約 50% のコストが節約されました。

## <a name="next-steps"></a>次のステップ

まず、[事業継続とディザスター リカバリーの概要](business-continuity-overview.md)を確認します。
