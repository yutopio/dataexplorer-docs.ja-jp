---
title: Azure Data Explorer を使用して高いコンカレンシーを実現するために最適化する
description: この記事では、高いコンカレンシーを実現するために Azure Data Explorer の設定を最適化する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 340d8b953ba9d56da2de1198c32bde71d4edb738
ms.sourcegitcommit: abbcb27396c6d903b608e7b19edee9e7517877bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2021
ms.locfileid: "100528089"
---
# <a name="optimize-for-high-concurrency-with-azure-data-explorer"></a>Azure Data Explorer を使用して高いコンカレンシーを実現するために最適化する

大規模なユーザーベースのシナリオでは、アプリケーションは低遅延かつ高スループットで、多くの要求を同時に処理するため、コンカレンシーの高いアプリケーションが必要です。 

ユース ケースには、大規模な監視とアラートのダッシュボードが含まれます。たとえば、 [Azure Monitor](https://azure.microsoft.com/services/monitor/)、 [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)、[Playfab](https://playfab.com/) などの Microsoft 製品やサービスなどです。 これらサービスはすべて、コンカレンシーの高いワークロードにサービスを提供するために Azure Data Explorer を使用しています。 Azure Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどからの大量のデータ ストリーミングをリアルタイムに分析するための、高速かつフル マネージドのビッグ データ分析サービスです。 

> [!NOTE]
> クラスターで同時に実行できるクエリの実際の数は、クラスター リソース、データ ボリューム、クエリの複雑さ、使用パターンなどのさまざまな要因によって異なります。

コンカレンシーの高いアプリケーションを設定するには、次のようにバックエンド アーキテクチャを設計します。

* [データの最適化](#optimize-data)
* [リーダー/フォロワー アーキテクチャ パターンの設定](#set-leader-follower-architecture-pattern)
* [クエリの最適化](#optimize-queries)
* [クラスター ポリシーの設定](#set-cluster-policies)
* [Azure Data Explorer クラスターの監視](#monitor-azure-data-explorer-clusters)

この記事では、上記の各項目について、最適かつ費用対効果の高い方法で高いコンカレンシーを実現するために実装できるさまざまな推奨事項を紹介します。 これらの機能は単独で使用することも、組み合わせて使用することもできます。


## <a name="optimize-data"></a>データの最適化

コンカレンシーを高めるには、クエリによって消費される CPU リソースの量を最小限に抑える必要があります。 次のいずれかまたはすべての方法を使用できます。[最適化されたテーブル スキーマ設計](#use-table-schema-design-best-practices)、[データのパーティション分割](#partition-data)、[事前集計](#pre-aggregate-your-data-with-materialized-views)、[キャッシュ](#configure-caching-policy)。

### <a name="use-table-schema-design-best-practices"></a>テーブル スキーマ設計のベスト プラクティスを使用する

テーブル スキーマ設計に関する次の推奨事項を使用して、使用される CPU リソースを最小限に抑えます。

* 列のデータ型を、これらの列に格納されている実際のデータに最適に一致させます。 たとえば、datetime 値は文字列の列に格納しないでください。
* 多くの列を含む大規模なスパース テーブルを避け、動的な列を使用してスパース プロパティを格納します。
* 頻繁に使用するプロパティは、非動的データ型の独自の列に格納します。
* 比較的大きな CPU リソースを必要とする結合を避けるために、データを非正規化します。

### <a name="partition-data"></a>データのパーティション分割

データは、エクステント (データ シャード) の形式で格納され、既定では取り込み時間によってパーティション分割されます。 [パーティション分割ポリシー](kusto/management/partitioningpolicy.md)を使用すると、バックグラウンド プロセスで単一の文字列の列または単一の datetime 列に基づいてエクステントを再パーティション分割できます。 フィルター処理、集計、またはその両方を行うために大部分のクエリがパーティション キーを使用する場合、パーティション分割によってパフォーマンスが大幅に向上します。

> [!NOTE]
> パーティション分割プロセス自体は CPU リソースを使用します。 ただし、クエリ実行時の CPU の削減量は、パーティション分割に使用される CPU の量を上回るはずです。

### <a name="pre-aggregate-your-data-with-materialized-views"></a>具体化されたビューを使用してデータを事前集計する

データを事前に集計して、クエリ実行時の CPU リソースを大幅に削減します。 シナリオの例としては、時間ビンの数を減らしてデータ ポイントを要約する、特定のレコードの最新のレコードを保持する、データセットを重複除去するなどがあります。 [具体化されたビュー](kusto/management/materialized-views/materialized-view-overview.md)を使用すると、ソース テーブルに対して構成が容易な集計ビューが得られます。 この機能を使用すると、これらの集計ビューを作成および管理する作業が簡略化されます。

> [!NOTE]
> バックグラウンドの集計プロセスでは、CPU リソースが使用されます。 ただし、クエリ実行時の CPU の削減量は、集計に使用される CPU の消費量を上回るはずです。

### <a name="configure-caching-policy"></a>キャッシュ ポリシーの構成

ホット ストレージ (ディスク キャッシュとも呼ばれます) に格納されているデータでクエリが実行されるように、キャッシュ ポリシーを構成します。 コールド ストレージ (外部テーブル) では、限定され、慎重に設計されたシナリオだけを実行します。

## <a name="set-leader-follower-architecture-pattern"></a>リーダー/フォロワー アーキテクチャ パターンの設定

フォロワー データベースは、同じリージョンに存在する別のクラスターのデータベースまたはデータベース内のテーブルのセットをフォローする機能です。 この機能は、[Azure Data Share](/azure/data-explorer/data-share)、[Azure Resource Manager API](follower.md)、一連の[クラスター コマンド](kusto/management/cluster-follower.md)によって公開されています。 

リーダー/フォロワー パターンを使用して、さまざまなワークロードのコンピューティング リソースを設定します。 たとえば、インジェスト用のクラスター、ダッシュボードやアプリケーションに対するクエリやサービスを提供するクラスター、データ サイエンス ワークロードに対するサービスを提供するクラスターを設定します。 この場合の各ワークロードは、個別にスケーリングできる専用のコンピューティング リソースと、さまざまなキャッシュとセキュリティ構成を持つことになります。 すべてのクラスターは同じデータを使用します。リーダーはデータを書き込み、フォロワーは読み取り専用モードでそれを使用します。

> [!NOTE]
> フォロワー データベースには、リーダーから、通常は数秒の遅延があります。 ご自身のソリューションで待機時間なしの最新のデータを必要とする場合は、次のソリューションが役立つ場合があります。リーダーおよびフォロワーからのデータを結合するフォロワー クラスターのビューを使用して、リーダーからの最新のデータとフォロワーからの残りのデータに対してクエリを実行します。

フォロワー クラスターに対するクエリのパフォーマンスを向上させるために、[prefetch extents の構成](kusto/management/cluster-follower.md#alter-follower-database-prefetch-extents)を有効にすることができます。 ただし、この構成はフォロワー データベースのデータの鮮度に影響を与える可能性があるため、慎重に使用してください。

## <a name="optimize-queries"></a>クエリの最適化

次の方法を使用して、高いコンカレンシーを実現するためにクエリを最適化します。

### <a name="use-query-results-cache"></a>クエリ結果のキャッシュを使用する

複数のユーザーが同じダッシュボードを同じような時間に読み込む場合、2 番目以降のユーザーのダッシュボードはキャッシュから提供できます。 この設定により、CPU をほとんど使用せずにハイ パフォーマンスを実現できます。 [クエリ結果のキャッシュ](kusto/query/query-results-cache.md)機能を使用し、`set` ステートメントを使用してクエリ結果のキャッシュの構成をクエリと共に送信します。

[Grafana](/azure/data-explorer/grafana) には、データ ソース レベルでのクエリ結果のキャッシュの構成設定が含まれています。そのため、すべてのダッシュボードで、既定でこの設定が使用され、クエリを変更する必要はありません。

### <a name="configure-query-consistency"></a>クエリの整合性の構成

クエリの整合性モデルには、"*強い*" (既定) と "*弱い*" の 2 つがあります。 強い整合性の場合、どの計算ノードがクエリを受信しても、最新の整合性のある状態データのみが表示されます。 弱い整合性の場合、ノードはメタデータのコピーを定期的に更新するため、メタデータの変更の同期において 1 分から 2 分の待機時間が発生します。 弱いモデルでは、メタデータの変更を管理するノードの負荷を軽減できるため、既定の厳密な整合性よりも高いコンカレンシーが得られます。 この構成は [クライアント要求プロパティ](kusto/api/netfx/request-properties.md)と Grafana データ ソース構成で設定します。

### <a name="optimize-queries"></a>クエリの最適化

クエリをできるだけ効率的に実行できるように、[クエリのベスト プラクティス](kusto/query/best-practices.md)に従ってください。

## <a name="set-cluster-policies"></a>クラスター ポリシーの設定

同時要求数は既定で制限されており、クラスターが過負荷にならないように[要求レート制限ポリシー](kusto/management/request-rate-limit-policy.md)によって制御されます。 このポリシーは、コンカレンシーの高い状況に合わせて調整できますが、このポリシーは、厳密なテストの後にのみ、できれば運用環境に近い使用パターンやデータセットに対して調整する必要があります。 テストを行うことで、変更された値がクラスターによって確実に保持されます。 この制限は、アプリケーションのニーズに基づいて構成できます。

## <a name="monitor-azure-data-explorer-clusters"></a>Azure Data Explorer クラスターの監視

クラスター リソースの正常性を監視すると、上記のセクションで提案されている機能を使用して最適化計画を作成するのに役立ちます。 Azure Monitor for Azure Data Explorer は、クラスターのパフォーマンス、操作、使用状況、エラーの包括的なビューを提供します。 クエリのパフォーマンス、同時クエリ、スロットルされたクエリ、その他のさまざまなメトリックについての分析情報を得るには、Azure portal 上の Azure Data Explorer クラスターの [監視] セクションにある **[Insights (preview)]\(分析情報 (プレビュー)\)** タブをクリックします。 クラスターの監視の詳細については、[Azure Monitor for Azure Data Explorer](/azure/azure-monitor/insights/data-explorer?toc=/azure/data-explorer/toc.json&amp;bc=/azure/data-explorer/breadcrumb/toc.json) のドキュメントを参照してください。 個々のメトリックの詳細については、[Azure Data Explorer メトリック](using-metrics.md#supported-azure-data-explorer-metrics)に関する記事を参照してください。
