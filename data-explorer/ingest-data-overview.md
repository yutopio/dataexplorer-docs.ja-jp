---
title: Azure Data Explorer のデータ インジェスト概要
description: Azure データ エクスプローラーでデータを取り込む (読み込む) さまざまな方法について説明します
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/18/2020
ms.localizationpriority: high
ms.openlocfilehash: 5304d2fcce23d6143faebb9326a6ab960a964f22
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512402"
---
# <a name="azure-data-explorer-data-ingestion-overview"></a>Azure Data Explorer のデータ インジェスト概要 

データ インジェストとは、Azure Data Explorer で 1 つまたは複数のソースからデータ レコードを読み込んで、テーブルにデータをインポートするプロセスのことです。 取り込まれたデータは、クエリに使用できるようになります。

次の図は、Azure Data Explorer での作業のエンド ツー エンドのフローと、さまざまな取り込み方法を示したものです。

:::image type="content" source="media/data-ingestion-overview/data-management-and-ingestion-overview.png" alt-text="データ インジェストと管理の概要":::

データ インジェストを担当する Azure Data Explorer のデータ管理サービスでは、次のプロセスが実装されます。

Azure Data Explorer は、外部ソースからデータをプルし、保留中の Azure キューから要求を読み取ります。 データは、バッチ処理されるか Data Manager にストリーミングされます。 同じデータベースおよびテーブルに送られるバッチ データは、インジェストのスループット向けに最適化されます。 Azure Data Explorer は、初期データを検証し、必要に応じてデータ形式を変換します。 その他のデータ操作には、スキーマの照合と、データの整理、インデックス付け、エンコード、圧縮などがあります。 データは、設定されたアイテム保持ポリシーに従ってストレージに保存されます。 Data Manager は、データ インジェストをエンジンにコミットします。その結果、クエリで使用できるようになります。 

## <a name="supported-data-formats-properties-and-permissions"></a>サポートされるデータ形式、プロパティ、およびアクセス許可

* **[サポートされるデータ形式](ingestion-supported-formats.md)** 

* **[インジェストのプロパティ](ingestion-properties.md)** :データの取り込まれる方法に影響を与えるプロパティ (タグ付け、マッピング、作成時間など)。

* **アクセス許可**:データを取り込むプロセスでは、[データベース インジェスター レベルのアクセス許可](kusto/management/access-control/role-based-authorization.md)が必要です。 クエリなどの他の操作では、データベース管理者、データベース ユーザー、またはテーブル管理者のアクセス許可が必要になる場合があります。

## <a name="batching-vs-streaming-ingestion"></a>バッチ処理とストリーミング インジェスト

* バッチ処理インジェストでは、データのバッチ処理が行われ、高インジェスト スループットのために最適化されます。 この方法は、推奨される、最もパフォーマンスの高い種類のインジェストです。 データはインジェスト プロパティに従ってバッチ処理されます。 データの小さなバッチはその後マージされ、高速なクエリ結果用に最適化されます。 [インジェスト バッチ](kusto/management/batchingpolicy.md) ポリシーは、データベースまたはテーブルに対して設定できます。 既定では、バッチ処理の最大値は、5 分、1000 項目、または合計サイズ1 GB です。

* [ストリーミング インジェスト](ingest-data-streaming.md) は、ストリーミング ソースからの継続的なデータ インジェストです。 ストリーミング インジェストを使用すると、テーブルごとに少量のデータ セットに対してほぼリアルタイムの待機時間を実現できます。 データは最初に行ストアに取り込まれ、次に列ストアのエクステントに移動されます。 ストリーミング インジェストは、Azure Data Explorer クライアント ライブラリまたはサポートされているいずれかのデータ パイプラインを使用して行うことができます。 

## <a name="ingestion-methods-and-tools"></a>インジェストの方法とツール

Azure Data Explorer では複数のインジェスト方法がサポートされており、それぞれに固有のターゲット シナリオがあります。 これらの方法には、インジェスト ツール、さまざまなサービスへのコネクタとプラグイン、マネージド パイプライン、SDK を使用したプログラムによる取り込み、インジェストへの直接アクセスなどがあります。

### <a name="ingestion-using-managed-pipelines"></a>マネージド パイプラインを使用したインジェスト

外部サービスによって実行される管理 (調整、再試行、監視、アラートなど) を必要とする組織では、コネクタを使用するのが最も適切なソリューションである可能性があります。 キューによるインジェストは、大量のデータに適しています。 Azure Data Explorer では、次の Azure Pipelines がサポートされています。

* **[Event Grid](https://azure.microsoft.com/services/event-grid/)** :Azure ストレージをリッスンし、サブスクライブしたイベントが発生したときに情報をプルするように Azure Data Explorer を更新するパイプライン。 詳細については、[Azure Data Explorer への Azure BLOB の取り込み](ingest-data-event-grid.md)に関するページを参照してください。

* **[Event Hubs](https://azure.microsoft.com/services/event-hubs/)** :サービスから Azure Data Explorer にイベントを転送するパイプライン。 詳細については、[イベント ハブから Azure Data Explorer へのデータの取り込み](ingest-data-event-hub.md)に関するページを参照してください。

* **[IoT Hub](https://azure.microsoft.com/services/iot-hub/)** :サポートされている IoT デバイスから Azure Data Explorer にデータを転送するために使用されるパイプライン。 詳細については、[IoT Hub からの取り込み](ingest-data-iot-hub.md)に関する記事を参照してください。

* **Azure Data Factory (ADF)** Azure の分析ワークロード用のフル マネージド データ統合サービス。 Azure Data Factory は 90 を超えるサポートされるソースに接続して、効率的で回復性があるデータ転送を提供します。 ADF では、さまざまな方法で監視できる分析情報を提供するために、データが準備、変換、強化されます。 このサービスは、1 回限りのソリューションとして、または定期的なタイムラインで使用したり、特定のイベントによってトリガーしたりすることができます。 
  * [Azure Data Explorer と Azure Data Factory の統合](data-factory-integration.md)。
  * [Azure Data Factory を使用してサポートされソースから Azure Data Explorer にデータをコピーする](./data-factory-load-data.md)。
  * [Azure Data Factory テンプレートを使用してデータベースから Azure Data Explorer に一括コピーする](data-factory-template.md)。
  * [Azure Data Factory コマンド アクティビティを使用して Azure Data Explorer 制御コマンドを実行する](data-factory-command-activity.md)。

### <a name="ingestion-using-connectors-and-plugins"></a>コネクタとプラグインを使用したインジェスト

* **Logstash プラグイン**。[Logstash から Azure Data Explorer へのデータの取り込み](ingest-data-logstash.md)に関するページを参照してください。

* **Kafka コネクタ**。[Kafka から Azure Data Explorer へのデータの取り込み](ingest-data-kafka.md)に関するページを参照してください。

* **[Power Automate](https://flow.microsoft.com/)** :Azure Data Explorer への自動化されたワークフロー パイプライン。 Power Automate を使用してクエリを実行し、クエリ結果をトリガーとして使用して事前設定されたアクションを実行することができます。 「[Power Automate に接続する Azure Data Explorer コネクタ (プレビュー)](flow.md)」を参照してください。

* **Apache Spark コネクタ**:任意の Spark クラスターで実行できるオープンソース プロジェクト。 Azure Data Explorer と Spark クラスター間でデータを移動するためのデータ ソースとデータ シンクを実装します。 データ ドリブン シナリオをターゲットとする、高速でスケーラブルなアプリケーションを作成することができます。 「[Apache Spark 用の Azure Data Explorer コネクタ](spark-connector.md)」を参照してください。

### <a name="programmatic-ingestion-using-sdks"></a>SDK を使用したプログラムによるインジェスト

Azure データ エクスプローラーで提供されている SDK を使用して、クエリとデータ インジェストを行うことができます。 プログラムによるインジェストは、インジェスト プロセスの最中および後のストレージ トランザクションを最小限に抑えることによって、インジェスト コスト (COG) を削減するように最適化されています。

**使用可能な SDK とオープン ソース プロジェクト**

* [Python SDK](kusto/api/python/kusto-python-client-library.md)

* [.NET SDK](kusto/api/netfx/about-the-sdk.md)

* [Java SDK](kusto/api/java/kusto-java-client-library.md)

* [Node SDK](kusto/api/node/kusto-node-client-library.md)

* [REST API](kusto/api/netfx/kusto-ingest-client-rest.md)

* [GO API](kusto/api/golang/kusto-golang-client-library.md)

### <a name="tools"></a>ツール

* **[ワンクリック インジェスト](ingest-data-one-click.md)** :さまざまな種類のソースからテーブルを作成および調整することにより、データを迅速に取り込むことができます。 ワンクリック インジェストでは、Azure Data Explorer 内のデータ ソースに基づいて、テーブルとマッピングの構造が自動的に提案されます。 ワンクリック インジェストは、1 回限りのインジェストに使用したり、データが取り込まれたされたコンテナー上の Event Grid を使用した継続的インジェストを定義するために使用したりすることができます。

* **[LightIngest](lightingest.md)** :Azure Data Explorer へのアドホック データ インジェストのためのコマンドライン ユーティリティ。 このユーティリティは、ローカル フォルダーまたは Azure Blob Storage コンテナーからソース データをプルできます。

### <a name="kusto-query-language-ingest-control-commands"></a>Kusto クエリ言語の取り込み制御コマンド

Kusto クエリ言語 (KQL) のコマンドを使用してデータをエンジンに直接取り込む方法は多数あります。 この方法ではデータ管理サービスがバイパスされるため、これは探索およびプロトタイプにのみ適しています。 運用環境または大規模なシナリオでは、この方法を使用しないでください。

  * **インライン インジェスト**:取り込まれるデータをコマンド テキスト自体の一部として、制御コマンド [.ingest inline](kusto/management/data-ingestion/ingest-inline.md) がエンジンに送信されます。 この方法は、即席のテストを目的としたものです。

  * **クエリからの取り込み**:クエリまたはコマンドの結果としてデータを間接的に指定し、制御コマンド [.set、.append、.set-or-append、または .set-or-replace](kusto/management/data-ingestion/ingest-from-query.md) がエンジンに送信されます。

  * **ストレージからの取り込み (プル)** :エンジンによってアクセス可能であり、コマンドで指定された外部ストレージ (Azure Blob Storage など) にデータを格納して、制御コマンド [.ingest into](kusto/management/data-ingestion/ingest-from-storage.md) がエンジンに送信されます。

## <a name="comparing-ingestion-methods-and-tools"></a>インジェストの方法とツールの比較

| インジェスト名 | データ型 | ファイルの最大サイズ | ストリーミング、バッチ処理、直接 | 最も一般的なシナリオ | 考慮事項 |
| --- | --- | --- | --- | --- | --- |
| [**ワンクリック インジェスト**](ingest-data-one-click.md) | *sv、JSON | 非圧縮で 1 GB (注を参照)| 直接インジェストでコンテナー、ローカル ファイル、BLOB にバッチ処理 | 1 回限り、テーブル スキーマの作成、イベント グリッドによる継続的インジェストの定義、コンテナーを使用した一括インジェスト (最大 1 万の BLOB) | 1 万の BLOB がコンテナーからランダムに選択される|
| [**LightIngest**](lightingest.md) | すべての形式がサポートされる | 非圧縮で 1 GB (注を参照) | DM を使用したバッチ処理またはエンジンへの直接インジェスト |  データの移行、インジェスト タイムスタンプを調整した履歴データ、一括インジェスト (サイズ制限なし)| 大文字と小文字を区別する、スペースを区別する |
| [**ADX Kafka**](ingest-data-kafka.md) | | | | |
| [**ADX から Apache Spark**](spark-connector.md) | | | | |
| [**LogStash**](ingest-data-logstash.md) | | | | |
| [**Azure Data Factory**](./data-factory-integration.md) | [サポートされるデータ形式](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | 無制限 *(ADF あたりの制限) | バッチ処理または ADF トリガーごと | 通常はサポートされていない形式、大規模なファイルをサポートしています。また、オンプレミスからクラウドに 90 を超えるソースをコピーできます。 | インジェストの時間 |
|[ **Azure Data Flow**](./flow.md) | | | | フローの一部としてのインジェスト コマンド| 高パフォーマンスの応答時間が必要 |
| [**IoT Hub**](ingest-data-iot-hub-overview.md) | [サポートされるデータ形式](ingest-data-iot-hub-overview.md#data-format)  | 該当なし | バッチ処理、ストリーミング | IoT メッセージ、IoT イベント、IoT プロパティ | |
| [**イベント ハブ**](ingest-data-event-hub-overview.md) | [サポートされるデータ形式](ingest-data-event-hub-overview.md#data-format) | 該当なし | バッチ処理、ストリーミング | メッセージ、イベント | |
| [**Event Grid**](ingest-data-event-grid-overview.md) | [サポートされるデータ形式](ingest-data-event-grid-overview.md#data-format) | 非圧縮で 1 GB | バッチ処理 | Azure ストレージからの継続的なインジェスト、Azure ストレージ内の外部データ | 100 KB が最適なファイル サイズ。BLOB の名前変更と BLOB の作成に使用される |
| [ **.NET SDK**](./net-sdk-ingest-data.md) | すべての形式がサポートされる | 非圧縮で 1 GB (注を参照) | バッチ処理、ストリーミング、直接 | 組織のニーズに合わせて独自のコードを作成する |
| [**Python**](python-ingest-data.md) | すべての形式がサポートされる | 非圧縮で 1 GB (注を参照) | バッチ処理、ストリーミング、直接 | 組織のニーズに合わせて独自のコードを作成する |
| [**Node.js**](node-ingest-data.md) | すべての形式がサポートされる | 非圧縮で 1 GB (注を参照) | バッチ処理、ストリーミング、直接 | 組織のニーズに合わせて独自のコードを作成する |
| [**Java**](kusto/api/java/kusto-java-client-library.md) | すべての形式がサポートされる | 非圧縮で 1 GB (注を参照) | バッチ処理、ストリーミング、直接 | 組織のニーズに合わせて独自のコードを作成する |
| [**REST**](kusto/api/netfx/kusto-ingest-client-rest.md) | すべての形式がサポートされる | 非圧縮で 1 GB (注を参照) | バッチ処理、ストリーミング、直接| 組織のニーズに合わせて独自のコードを作成する |
| [**Go**](kusto/api/golang/kusto-golang-client-library.md) | すべての形式がサポートされる | 非圧縮で 1 GB (注を参照) | バッチ処理、ストリーミング、直接 | 組織のニーズに合わせて独自のコードを作成する |

> [!Note] 
> 上の表で参照されている場合、インジェストでは最大ファイル サイズとして 4 GB がサポートされます。 100 MB から 1 GB の間のファイルを取り込むことをお勧めします。

## <a name="ingestion-process"></a>インジェスト プロセス

ニーズに最も適したインジェスト方法を選択したら、次の手順を実行します。

1. **保持ポリシーを設定する**

    Azure Data Explorer のテーブルに取り込まれたデータは、テーブルの有効な保持ポリシーの対象となります。 テーブルに明示的に設定しない限り、有効な保持ポリシーはデータベースの保持ポリシーから取得されます。 ホット リテンションは、クラスター サイズと保持ポリシーの機能です。 使用可能な領域よりも多くのデータを取り込むと、最初のデータにコールド リテンションが適用されます。
    
    データベースの保持ポリシーがご自分のニーズに適していることを確認してください。 適していない場合は、テーブル レベルで明示的にオーバーライドします。 詳細については、「[保持ポリシー](kusto/management/retentionpolicy.md)」を参照してください。 
    
1. **テーブルの作成**

    データを取り込むためには、事前にテーブルを作成する必要があります。 次のいずれかのオプションを使用します。
   * [コマンドを使用して](kusto/management/create-table-command.md)テーブルを作成します。 
   * [ワンクリック インジェスト](one-click-ingestion-new-table.md)を使用してテーブルを作成します。

    > [!Note]
    > レコードが不完全な場合、またはフィールドを必要なデータ型として解析できない場合は、対応するテーブル列に null 値が設定されます。

1. **スキーマ マッピングの作成**

    [スキーマ マッピング](kusto/management/mappings.md)は、ソースのデータ フィールドをターゲットのテーブル列にバインドするのに役立ちます。 マッピングを使用すると、定義された属性に基づいて、異なるソースから同じテーブルにデータを取り込むことができます。 行指向 (CSV、JSON、AVRO) と列指向 (Parquet) の両方で、さまざまな種類のマッピングがサポートされています。 ほとんどの方法では、マッピングを[テーブルで事前に作成](kusto/management/create-ingestion-mapping-command.md)して、取り込みコマンドのパラメーターから参照することもできます。

1. **更新ポリシーの設定** (省略可能)

   一部のデータ形式マッピング (Parquet、JSON、Avro) では、簡単で便利な取り込み時の変換がサポートされています。 取り込み時により複雑な処理を必要とするシナリオでは、更新ポリシーを使用します。これにより、Kusto クエリ言語コマンドを使用した簡易処理が可能になります。 更新ポリシーでは、元のテーブルの取り込まれたデータに対して抽出と変換が自動的に実行され、結果のデータが 1 つ以上の宛先テーブルに取り込まれます。 [更新ポリシー](kusto/management/update-policy.md)を設定します。



## <a name="next-steps"></a>次のステップ

* [サポートされるデータ形式](ingestion-supported-formats.md)
* [サポートされるインジェストのプロパティ](ingestion-properties.md)