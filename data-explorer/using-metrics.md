---
title: メトリックを使用した Azure Data Explorer のパフォーマンス、正常性、および使用状況の監視
description: Azure Data Explorer メトリックを使用して、クラスターのパフォーマンス、正常性、および使用状況を監視する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/19/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: fb428e443559b579bab4764283ce124f9d9ec192
ms.sourcegitcommit: 62eff65b320ce4ca53eabed6156eb9fe5b77f548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2021
ms.locfileid: "99224215"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>メトリックを使用した Azure Data Explorer のパフォーマンス、正常性、および使用状況の監視

Azure Data Explorer のメトリックは、Azure Data Explorer クラスター リソースの正常性とパフォーマンスに関する主要な指標を提供します。 この記事で詳細に説明するメトリックを使用して、特定のシナリオでの Azure Data Explorer クラスターの使用状況、正常性、パフォーマンスをスタンドアロン メトリックとして監視します。 また、[Azure ダッシュボード](/azure/azure-portal/azure-portal-dashboards) と [Azure アラート](/azure/azure-monitor/platform/alerts-metric-overview) の操作の基本としてメトリックを使用できます。

Azure メトリックス エクスプローラーの詳細については、[メトリックス エクスプローラー](/azure/azure-monitor/platform/metrics-getting-started)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 お持ちでない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成することができます。
* [クラスターとデータベース](create-cluster-database-portal.md)。

## <a name="use-metrics-to-monitor-your-azure-data-explorer-resources"></a>メトリックを使用して Azure Data Explorer のリソースを監視する

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. Azure Data Explorer クラスターの左側のペインで、「*メトリック*」を検索します。
1. **[メトリック]** を選択して [メトリック] ペインを開き、クラスターの分析を開始します。
    :::image type="content" source="media/using-metrics/select-metrics.gif" alt-text="Azure portal でメトリックを検索して選択する":::

## <a name="work-in-the-metrics-pane"></a>[メトリック] ペインで作業する

[メトリック] ペインで、追跡する特定のメトリックを選択し、データを集計する方法を選択して、ダッシュボードに表示するメトリック グラフを作成します。

Azure Data Explorer クラスターの **[リソース]** と **[メトリック名前空間]** は事前に選択されています。 次の図の中の番号は、その下の番号付きリストに対応しています。 これらは、メトリックの設定と表示に関するさまざまなオプションについて説明します。

![[メトリック] ウィンドウ](media/using-metrics/metrics-pane.png)

1. メトリック グラフを作成するには、**メトリック** 名と、メトリックあたりの適切な **集計** を選択します。 さまざまなメトリックの詳細については、「[サポートされている Azure Data Explorer メトリック](#supported-azure-data-explorer-metrics)」を参照してください。
1. 同じグラフにプロットされた複数のメトリックを表示する場合は、 **[メトリックの追加]** を選択します。
1. 1 つのビューに複数のグラフを表示する場合は、 **[+ New chart]\(+ 新規グラフ\)** を選択します。
1. 時間範囲を変更する場合は、時刻の選択ツールを使用します (既定: 過去 24 時間)。
1. ディメンションを持つメトリックには、[ **[フィルターの追加]** および **[Apply Splitting]\(分割の適用\)**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) を使用します。
1. グラフ構成をダッシュボードに追加して、再度表示できるようにする場合は、 **[ダッシュボードにピン留めする]** を選択します。
1. 設定した条件を使用してメトリックを視覚化するには、 **[新しいアラート ルール]** を設定します。 新しいアラート ルールにはターゲット リソース、メトリック、分割、およびグラフからのフィルター ディメンションが含まれます。 これらの設定は、[アラート ルールの作成ウィンドウ](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)で変更します。

## <a name="supported-azure-data-explorer-metrics"></a>サポートされている Azure Data Explorer メトリック

Azure Data Explorer のメトリックを使用すると、リソースの全体的なパフォーマンスと使用の両方に関する分析情報、およびインジェストやクエリなどの特定のアクションに関する情報を得ることができます。 この記事のメトリックは、使用法の種類別にグループ化されています。 

メトリックの種類には、以下のものがあります。 
* [クラスターのメトリック](#cluster-metrics) 
* [メトリックのエクスポート](#export-metrics) 
* [インジェストのメトリック](#ingestion-metrics) 
* [ストリーミング インジェスト メトリック](#streaming-ingest-metrics)
* [クエリのメトリック](#query-metrics) 
* [具体化されたビューのメトリック](#materialized-view-metrics)

Azure Data Explorer 用の Azure Monitor のメトリックのアルファベット順一覧については、[サポートされる Azure Data Explorer クラスターのメトリック](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)に関する記事を参照してください。

## <a name="cluster-metrics"></a>クラスターのメトリック

クラスターのメトリックは、クラスターの全般的な正常性を追跡します。 たとえば、リソースとインジェストの使用と応答性があります。

|**メトリック** | **単位** | **集計** | **メトリックの説明** | **Dimensions** |
|---|---|---|---|---|
| キャッシュ使用率 | Percent | Avg、Max、Min | クラスターで現在使用中の割り当てられたキャッシュ リソースの割合。 キャッシュとは、定義されているキャッシュ ポリシーに従ってユーザー アクティビティに割り当てられた SSD のサイズのことです。 <br> <br> 80% 以下の平均キャッシュ使用率が、クラスターにとって持続可能な状態です。 平均キャッシュ使用率が 80% を超える場合、ストレージ最適化価格レベルにクラスターを <br> [スケールアップ](manage-cluster-vertical-scaling.md)するか、より多くのインスタンスに <br> [スケールアウト](manage-cluster-horizontal-scaling.md)します。 あるいは、キャッシュ ポリシーを調整します (キャッシュに格納する日数を削減)。 キャッシュ使用率が 100% を超える場合、キャッシュ ポリシーに基づいてキャッシュされるデータのサイズが、クラスター上の合計キャッシュ サイズを超えています。 | なし |
| CPU | Percent | Avg、Max、Min | クラスター内のマシンで現在使用中の割り当てられたコンピューティング リソースの割合。 <br> <br> 80% 以下の平均 CPU 使用率が、クラスターにとって持続可能です。 CPU の最大値は 100% であり、これは、データを処理する計算リソースがそれ以上ないことを示します。 <br> クラスターのパフォーマンスが良くない場合は、特定の CPU がブロックされていないか判別するために、CPU の最大値を確認してください。 | なし |
| インジェストの使用率 | Percent | Avg、Max、Min | 割り当てられた合計リソースからのデータのインジェストに使用された実際のリソースと、インジェストを実行するために容量ポリシーに割り当てられた合計リソースの間の比率。 既定の容量ポリシーでは、同時実行のインジェスト操作は 512 以下、またはインジェストに使用されるクラスター リソースは 75% 以下です。 <br> <br> 80% 以下の平均インジェスト使用率が、クラスターにとって持続可能な状態です。 インジェスト使用率の最大値は 100% であり、これは、すべてのクラスター インジェスト機能が使用され、インジェストがキューに入れられる結果となる可能性を示します。 | なし |
| キープ アライブ | Count | Avg | クラスターの応答性を追跡します。 <br> <br> 応答性の高いクラスターは 1 の値を返し、ブロックまたは切断されたクラスターは 0 を返します。 |
| スロットルされたコマンドの合計数 | Count | Avg、Max、Min、Sum | 同時 (並列) コマンドの最大許容数に達したために、クラスター内でスロットルされた (拒否された) コマンドの数。 | なし |
| エクステントの合計数 | Count | Avg、Max、Min、Sum | クラスター内のデータ エクステントの合計数。 <br> <br> データ エクステントのマージは CPU 負荷が高いアクティビティであるため、このメトリックの変化は、データ構造の大幅な変更とクラスターでの高負荷を示している可能性があります。 | なし |

## <a name="export-metrics"></a>メトリックのエクスポート

エクスポートのメトリックは、エクスポート操作の全般的な正常性とパフォーマンス (遅延、結果、レコード数、使用状況など) を追跡します。

|**メトリック** | **単位** | **集計** | **メトリックの説明** | **Dimensions** |
|---|---|---|---|---|
連続エクスポートによるエクスポートされたレコード数    | Count | SUM | すべての連続エクスポート ジョブでエクスポートされたレコードの数。 | ContinuousExportName |
連続エクスポートの最大遅延 |    Count   | Max   | クラスター内の連続エクスポート ジョブによって報告された遅延 (分単位)。 | なし |
保留中の連続エクスポートの数 | Count | Max   | 保留中の連続エクスポート ジョブの数。 これらのジョブは実行する準備ができていますが、キューで待機中になっています。これは、容量不足が原因である可能性があります。 
連続エクスポートの結果    | Count |   Count   | 各連続エクスポート実行の失敗および成功の結果。 | ContinuousExportName |
エクスポート使用率 |    Percent | Max   | クラスター内のエクスポート容量の合計のうち、使用されているエクスポート容量 (0 から 100 の間)。 | なし |

## <a name="ingestion-metrics"></a>インジェストのメトリック

インジェストのメトリックは、インジェスト操作の全般的な正常性とパフォーマンス (待機時間、結果、量など) を追跡します。

> [!NOTE]
> * [グラフにフィルターを適用して](/azure/azure-monitor/platform/metrics-charts#apply-filters-to-charts)、ディメンション別に部分的なデータをプロットします。 たとえば、特定の `Database` へのインジェストを調べます。
> * [グラフに分割を適用して](/azure/azure-monitor/platform/metrics-charts#apply-splitting-to-a-chart)、各種コンポーネント別にデータを視覚化します。 このプロセスは、`Blobs received` など、インジェスト パイプラインの各ステップによって報告されるメトリックを分析するのに役立ちます。

|**メトリック** | **単位** | **集計** | **メトリックの説明** | **Dimensions** |
|---|---|---|---|---|
| Batch blob count (バッチ BLOB 数)  | Count | Avg、Max、Min | 完了したインジェスト バッチ内のデータ ソースの数。 | データベース |
| Batch duration (バッチ期間)    | Seconds | Avg、Max、Min | インジェスト フロー内のバッチ処理フェーズの期間。  | データベース |
| バッチ サイズ        | バイト | Avg、Max、Min | インジェスト用に集計されたバッチで、予想される非圧縮のデータ サイズ | データベース |
| Batches processed (処理されたバッチ) | Count | Sum、Max、Min | インジェスト用に完了したバッチの数。 <br> `Batching Type`: バッチの完了が、[バッチ処理ポリシー](./kusto/management/batchingpolicy.md)によって設定されたバッチ処理時間、データ サイズ、またはファイル数の上限に基づいていたかどうか。 | データベース、バッチ処理の種類 |
| Blobs received (受信した BLOB)    | Count | Sum、Max、Min | コンポーネントによって入力ストリームから受信した BLOB の数。 <br> <br> **[apply splitting]\(分割の適用\)** を使用して各コンポーネントを分析します。 | データベース、コンポーネントの種類、コンポーネント名 |
| Blobs processed (処理された BLOB)   | Count | Sum、Max、Min | コンポーネントによって処理された BLOB の数。 <br> <br> **[apply splitting]\(分割の適用\)** を使用して各コンポーネントを分析します。 | データベース、コンポーネントの種類、コンポーネント名 |
| Blobs dropped (破棄された BLOB)     | Count | Sum、Max、Min | コンポーネントによって永久に破棄された BLOB の数。 このような BLOB ごとに、エラーの理由を含む `Ingestion result` メトリックが送信されます。 <br> <br> **[apply splitting]\(分割の適用\)** を使用して各コンポーネントを分析します。 | データベース、コンポーネントの種類、コンポーネント名 |
| 検出の待機時間 | Seconds | Avg | データのエンキューから、データ接続によって検出されるまでの時間。 この時間は、**Stage latency (ステージの待機時間)** または **Ingestion latency (インジェストの待ち時間)** メトリックには含まれません。 | コンポーネントの種類、コンポーネント名 |
| Events received (受信したイベント)   | Count | Sum、Max、Min | 入力ストリームからのデータ接続によって受信されたイベントの数。 | コンポーネントの種類、コンポーネント名 |
| Events processed (処理されたイベント)  | Count | Sum、Max、Min | データ接続によって処理されたイベントの数。 | コンポーネントの種類、コンポーネント名 | 
| Events dropped (破棄されたイベント)    | Count | Sum、Max、Min | データ接続によって永久に破棄されたイベントの数。 | コンポーネントの種類、コンポーネント名 | 
| (Event/IoT Hubs の) 処理されたイベント | Count | Max、Min、Sum | Event Hubs から読み取られ、クラスターによって処理されたイベントの合計数。 これらのイベントは、2 つのグループ (クラスター エンジンによって拒否されたイベントと受け入れられたイベント) に分類されます。 | Status |
| インジェストの待ち時間 | Seconds | Avg、Max、Min | クラスターでデータが受信された時点からクエリー用に準備できるまでの、取り込まれたデータの待ち時間。 インジェストの待ち時間の長さは、インジェストのシナリオに応じて異なります。 | なし |
| インジェストの結果  | Count | SUM | 失敗または成功したインジェスト操作の合計数。 <br> <br> **[apply splitting]\(分割の適用\)** を使用して、成功および失敗した結果のバケットを作成し、ディメンションを分析します ( **[値]**  >  **[状態]** )。 <br>起こりえる失敗の結果の詳細については、「[Azure Data Explorer のインジェスト エラー コード](error-codes.md)」を参照してください。| Status |
| インジェストの量 (MB 単位) | Count | Max、Sum | 圧縮前にクラスターにインジェストされたデータの合計サイズ (MB 単位)。 | データベース |
| Queue length | Count | Avg | コンポーネントの入力キュー内の保留中のメッセージの数。 | コンポーネントの種類 |
| Queue oldest message (キューの最も古いメッセージ) | Seconds | Avg | コンポーネントの入力キュー内に最も古いメッセージが挿入されてからの経過時間 (秒)。 | コンポーネントの種類 | 
| Received data size bytes (受信したデータ サイズ (バイト単位)) | バイト | Avg、Sum | 入力ストリームからのデータ接続によって受信されたデータのサイズ。 | コンポーネントの種類、コンポーネント名 |
| ステージの待機時間 | Seconds | Avg | メッセージが Azure Data Explorer によって検出されてから、そのコンテンツが処理のためにインジェスト コンポーネントによって受信されるまでの時間。 <br> <br> **[フィルターを適用する]** を使用し、 **[コンポーネントの種類] > [EngineStorage]** を選択して、インジェストの合計待機時間を表示します。| データベース、コンポーネントの種類 | 

## <a name="streaming-ingest-metrics"></a>ストリーミング インジェスト メトリック

ストリーミング インジェスト メトリックは、ストリーミング インジェスト データおよび要求率、期間、結果を追跡します。

|**メトリック** | **単位** | **集計** | **メトリックの説明** | **Dimensions** |
|---|---|---|---|---|
ストリーミング インジェストのデータ速度 |    Count   | RateRequestsPerSecond | クラスターに取り込まれるデータの総量。 | なし |
ストリーミング インジェストの期間   | ミリ秒  | Avg、Max、Min | すべてのストリーミング インジェスト要求の合計期間。 | なし |
ストリーミング インジェストの要求率   | Count | Count、Avg、Max、Min、Sum | ストリーミングインジェスト要求の合計数。 | なし |
ストリーミング インジェストの結果 | Count | Avg   | 結果の種類別のストリーミング インジェスト要求の合計数。 | 結果 |

## <a name="query-metrics"></a>クエリのメトリック

クエリ パフォーマンス メトリックは、クエリ期間および同時クエリまたはスロットルされたクエリの合計数を追跡します。

|**メトリック** | **単位** | **集計** | **メトリックの説明** | **Dimensions** |
|---|---|---|---|---|
| クエリ実行時間 | ミリ秒 | Avg、Min、Max、Sum | クエリ結果を受け取るまでの合計時間 (ネットワーク待ち時間は含まれません)。 | QueryStatus |
| 同時クエリの合計数 | Count | Avg、Max、Min、Sum | クラスターで並列実行されるクエリの数。 このメトリックは、クラスターの負荷を見積もるのに適した方法です。 | なし |
| スロットルされたクエリの合計数 | Count | Avg、Max、Min、Sum | クラスター内のスロットルされた (拒否された) クエリの数。 許可される同時 (並列) クエリの最大数は、要求レート制限ポリシーで定義されます。 | なし |

## <a name="materialized-view-metrics"></a>具体化されたビューのメトリック

|**メトリック** | **単位** | **集計** | **メトリックの説明** | **Dimensions** |
|---|---|---|---|---|
|MaterializedViewHealth                    | 1、0    | Avg     |  ビューが正常と見なされる場合、値は 1 です。それ以外の場合は 0 です。 | Database、MaterializedViewName |
|MaterializedViewAgeMinutes                | 分 | Avg     | ビューの `age` は、現在の時刻から、ビューによって処理された最後のインジェスト時間を引いた値で定義されます。 メトリック値は分単位の時間です (値が小さいほど、ビューの "正常性は高い" です)。 | Database、MaterializedViewName |
|MaterializedViewResult                    | 1       | Avg     | メトリックには、最後の具体化サイクルの結果を示す `Result` ディメンションが含まれます (以下の設定可能な値を参照)。 メトリック値は常に 1 です。 | Database、MaterializedViewName、Result |
|MaterializedViewRecordsInDelta            | レコード数 | Avg | ソース テーブルの処理されていない部分に現在あるレコードの数。 詳細については、「[具体化されたビューのしくみ](./kusto/management/materialized-views/materialized-view-overview.md#how-materialized-views-work)」を参照してください| Database、MaterializedViewName |
|MaterializedViewExtentsRebuild            | エクステント数 | Avg | 具体化サイクルで再構築されたエクステントの数。 | Database、MaterializedViewName|
|MaterializedViewDataLoss                  | 1       | Max    | 未処理のソース データがリテンション期間に近づいたときに、メトリックが発生します。 | Database、MaterializedViewName、Kind |

## <a name="next-steps"></a>次のステップ

* [チュートリアル:Azure Data Explorer で監視データを取り込んでクエリを実行する](ingest-data-no-code.md)
* [診断ログを使用して Azure Data Explorer インジェスト操作を監視する](using-diagnostic-logs.md)
* [クイック スタート: Azure データ エクスプローラーでデータのクエリを実行する](web-query-data.md)
