---
title: Azure Data Explorer クラスターに適したコンピューティング SKU を選択する
description: この記事では、Azure Data Explorer クラスターに最適なコンピューティング SKU サイズを選択する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/13/2020
ms.openlocfilehash: 5381b558d54002ddcd50fbbec2e4fbef6d44fdbc
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003064"
---
# <a name="select-the-correct-compute-sku-for-your-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターに適したコンピューティング SKU を選択する 

変化するワークロードのために新しいクラスターを作成するか、またはクラスターを最適化する場合、Azure Data Explorer では、選択できる複数の仮想マシン (VM) SKU が提供されます。 これらのコンピューティング SKU は、すべてのワークロードに最適なコストを提供するために慎重に選択されています。 

データ管理クラスターのサイズと VM SKU は、Azure Data Explorer サービスによって完全に管理されます。 これらは、エンジンの VM サイズやインジェスト ワークロードなどの要因によって決定されます。 

エンジン クラスターのコンピューティング SKU は、[クラスターをスケールアップ](manage-cluster-vertical-scaling.md)することによっていつでも変更できます。 最初のシナリオに適した最小の SKU サイズから始めることが最善です。 クラスターをスケールアップすると、新しい SKU でクラスターが再作成される間、最大 30 分のダウンタイムが発生することに注意してください。 [Azure Advisor の推奨事項](azure-advisor.md)を使用してコンピューティング SKU を最適化することもできます。

> [!TIP]
> [コンピューティングの予約インスタンス (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) は、Azure Data Explorer クラスターに適用されます。  

この記事では、さまざまなコンピューティング SKU オプションについて説明し、最適な選択を行うために役立つ技術的な詳細情報を提供します。

## <a name="select-a-cluster-type"></a>クラスターの種類を選択する

Azure Data Explorer には、次の 2 種類のクラスターが用意されています。

* **運用**: 運用クラスターには、エンジン クラスターとデータ管理クラスター用の 2 つのノードが含まれており、Azure Data Explorer の [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/) のもとで運用されます。

* **Dev/Test (SLA なし)** : Dev/Test クラスターには、エンジンとデータ管理クラスター用の 1 つのノードがあります。 このクラスターの種類は、インスタンスの数が少なく、エンジン マークアップ料金が必要ないため、最も低いコスト構成です。 冗長性がないため、このクラスター構成には SLA がありません。

## <a name="compute-sku-types"></a>コンピューティング SKU の種類

Azure Data Explorer クラスターでは、さまざまな種類のワークロードに対応したさまざまな SKU がサポートされています。 各 SKU では、お客様が適切にデプロイのサイズを設定し、エンタープライズ分析ワークロードのソリューションを最適なコストで構築できるように、個別の SSD と CPU の比率が提供されます。

### <a name="compute-optimized"></a>コンピューティング最適化

* 高いコア/キャッシュ比率が提供されます。
* 小規模から中規模のデータ サイズに対してクエリを行う率が高い場合に適しています。
* 低待機時間 I/O のためのローカル SSD。

### <a name="heavy-compute"></a>高負荷のコンピューティング

* より高いコア/キャッシュ比率が提供される AMD SKU。
* 低待機時間 I/O のためのローカル SSD。

### <a name="storage-optimized"></a>ストレージ最適化

* 1 つのエンジン ノードあたり 1 ～ 4 TB の大きなストレージに対するオプション。
* あまりコンピューティング集中型でないクエリ要件で、大量のデータの格納を必要とするワークロードに適しています。
* 特定の SKU では、ホット データ ストレージ用のローカル SSD ではなく、エンジン ノードに接続された Premium Storage (マネージド ディスク) が使用されます。

### <a name="isolated-compute"></a>分離コンピューティング

サーバー インスタンスレベルの分離を必要とするワークロードを実行するのに最適な SKU。

## <a name="select-and-optimize-your-compute-sku"></a>コンピューティング SKU を選択して最適化する 

### <a name="select-your-compute-sku-during-cluster-creation"></a>クラスターの作成時にコンピューティング SKU を選択する

Azure Data Explorer クラスターを作成する場合は、計画されたワークロードのための*最適な* VM SKU を選択してください。

次の属性も、SKU の選択に役立ちます。
 
| 属性 | 詳細 |
|---|---
|**可用性**| すべてのリージョンですべての SKU サイズを使用できるわけではありません |
|**コアあたりで GB キャッシュあたりのコスト**| コンピューティングと高負荷のコンピューティング最適化では高コスト。 ストレージ最適化 SKU では低コスト |
|**予約インスタンス (RI) の価格**| RI の割引は、リージョンごとおよび SKU ごとに異なります |  

> [!NOTE]
> Azure Data Explorer クラスターの場合、ストレージやネットワークと比較して、コンピューティング コストはクラスター コストの最も重要な部分です。

### <a name="optimize-your-cluster-compute-sku"></a>クラスターのコンピューティング SKU を最適化する

クラスターのコンピューティング SKU を最適化するには、[垂直スケーリングを構成](manage-cluster-vertical-scaling.md#configure-vertical-scaling)し、[Azure Advisor の推奨事項](azure-advisor.md)を確認してください。 

選択できるさまざまなコンピューティング SKU オプションを使用して、シナリオのパフォーマンスとホット キャッシュの要件に合わせてコストを最適化できます。 
* クエリの量が多いときに最適なパフォーマンスが必要な場合、理想的な SKU はコンピューティング最適化です。 
* クエリの負荷が比較的低いときに大量のデータにクエリを実行する必要がある場合は、ストレージ最適化 SKU が、コストを削減しながら引き続き優れたパフォーマンスを提供するために役立ちます。

小さな SKU ではクラスターあたりのインスタンスの数が制限されるため、RAM の多いより大きな VM を使用することをお勧めします。 RAM リソースに対する需要の多い一部のクエリの種類 (`joins` を使用するクエリなど) には、より多くの RAM が必要です。 そのため、スケーリング オプションを検討している場合は、インスタンスを追加してスケールアウトするのではなく、より大きな SKU にスケールアップすることをお勧めします。

## <a name="compute-sku-options"></a>コンピューティング SKU のオプション

Azure Data Explorer クラスター VM の技術仕様を次の表で説明します。

|**名前**| **カテゴリ** | **SSD サイズ** | **コア** | **RAM** | **Premium Storage ディスク (1&nbsp;TB)**| **クラスターあたりの最小インスタンス数** | **クラスターあたりの最大インスタンス数**
|---|---|---|---|---|---|---|---
|Dev(No SLA) Standard_D11_v2| コンピューティング最適化 | 75&nbsp;GB    | 1 | 14&nbsp;GB | 0 | 1 | 1
|Dev(No SLA) Standard_E2a_v4| コンピューティング最適化 | 18&nbsp;GB    | 1 | 14&nbsp;GB | 0 | 1 | 1
|Standard_D11_v2| コンピューティング最適化 | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 2 | 8 
|Standard_D12_v2| コンピューティング最適化 | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|Standard_D13_v2| コンピューティング最適化 | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1,000
|Standard_D14_v2| コンピューティング最適化 | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1,000
|Standard_E2a_v4| 高負荷のコンピューティング | 18&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 2 | 8 
|Standard_E4a_v4| 高負荷のコンピューティング | 54&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|Standard_E8a_v4| 高負荷のコンピューティング | 127&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1,000
|Standard_E16a_v4| 高負荷のコンピューティング | 273&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1,000
|Standard_DS13_v2 + 1&nbsp;TB&nbsp;PS| ストレージ最適化 | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1,000
|Standard_DS13_v2 + 2&nbsp;TB&nbsp;PS| ストレージ最適化 | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1,000
|Standard_DS14_v2 + 3&nbsp;TB&nbsp;PS| ストレージ最適化 | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1,000
|Standard_DS14_v2 + 4&nbsp;TB&nbsp;PS| ストレージ最適化 | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1,000
|Standard_E8as_v4 + 1&nbsp;TB&nbsp;PS| ストレージ最適化 | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1,000
|Standard_E8as_v4 + 2&nbsp;TB&nbsp;PS| ストレージ最適化 | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1,000
|Standard_E16as_v4 + 3&nbsp;TB&nbsp;PS| ストレージ最適化 | 3&nbsp;TB | 16 | 112&nbsp;GB | 3 | 2 | 1,000
|Standard_E16as_v4 + 4&nbsp;TB&nbsp;PS| ストレージ最適化 | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1,000
|Standard_L4s| ストレージ最適化 | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|Standard_L8s| ストレージ最適化 | 1.3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1,000
|Standard_L16s| ストレージ最適化 | 2.6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000
|Standard_L8s_v2| ストレージ最適化 | 1.7&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1,000
|Standard_L16s_v2| ストレージ最適化 | 3.5&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000
|Standard_E64i1_v3| 分離コンピューティング | 1.1&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000

* Azure Data Explorer [ListSkus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet) を使用して、リージョンごとの更新されたコンピューティング SKU の一覧を表示できます。 
* [さまざまな SKU](/azure/virtual-machines/windows/sizes) の詳細について参照してください。 

## <a name="next-steps"></a>次のステップ

* さまざまなニーズに応じて VM SKU を変更することによって、エンジン クラスターをいつでも[スケールアップまたはスケールダウン](manage-cluster-vertical-scaling.md)できます。 

* 変化する需要に応じて容量を変更するために、エンジン クラスターのサイズを[スケールインまたはスケールアウト](manage-cluster-horizontal-scaling.md)できます。

* [Azure Advisor の推奨事項](azure-advisor.md)を使用してコンピューティング SKU を最適化します。

