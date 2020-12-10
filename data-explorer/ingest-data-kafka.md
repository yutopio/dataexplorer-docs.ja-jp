---
title: Kafka から Azure Data Explorer にデータを取り込む
description: この記事では、Kafka から Azure Data Explorer にデータを取り込む (読み込む) 方法について学習します
author: orspod
ms.author: orspodek
ms.reviewer: ankhanol
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: cc2f10570081fec3a5762ab3f2e23b9e22839063
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2020
ms.locfileid: "96774658"
---
# <a name="ingest-data-from-apache-kafka-into-azure-data-explorer"></a>Apache Kafka から Azure Data Explorer にデータを取り込む
 
Azure Data Explorer では、[Apache Kafka](http://kafka.apache.org/documentation/) からの[データ インジェスト](ingest-data-overview.md)がサポートされています。 Apache Kafka は、システム間やアプリケーション間で確実にデータを移動するリアルタイム ストリーミング データ パイプラインを構築するための分散ストリーミング プラットフォームです。 [Kafka Connect](https://docs.confluent.io/3.0.1/connect/intro.html#kafka-connect) は、Apache Kafka と他のデータ システムとの間でスケーラブルかつ高い信頼性でデータをストリーム配信するためのツールです。 Azure Data Explorer [Kafka Sink](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md) は、Kafka からのコネクタとして機能し、コードを使用する必要はありません。 こちらの [Git リポジトリ](https://github.com/Azure/kafka-sink-azure-kusto/releases) または [Confluent Connector Hub](https://www.confluent.io/hub/microsoftcorporation/kafka-sink-azure-kusto) からシンク コネクタ jar をダウンロードしてください。

この記事では、Kafka クラスターと Kafka コネクタ クラスターのセットアップを簡略化するために自己完結型の Docker セットアップを使用して、Kafka によって Azure Data Explorer にデータを取り込む方法について説明します。 

詳細については、コネクタの [Git リポジトリ](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md)と[バージョンの詳細](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md#13-major-version-specifics)を参照してください。

## <a name="prerequisites"></a>必須コンポーネント

* [Microsoft Azure アカウント](/azure/)を作成します。
* [Azure CLI](/cli/azure/install-azure-cli) をインストールします。
* [Docker](https://docs.docker.com/get-docker/) と [Docker Compose](https://docs.docker.com/compose/install) をインストールします。
* 既定のキャッシュおよびアイテム保持ポリシーを使用して、[Azure portal でAzure Data Explorer のクラスターとデータベースを作成します](create-cluster-database-portal.md)。

## <a name="create-an-azure-active-directory-service-principal"></a>Azure Active Directory のサービス プリンシパルを作成する

Azure Active Directory サービス プリンシパルは、次の例のように [Azure portal](/azure/active-directory/develop/howto-create-service-principal-portal) またはプログラムを使用して作成できます。

このサービス プリンシパルは、Azure Data Explorer テーブルに書き込むためにコネクタによって利用される ID になります。 後で、Azure Data Explorer にアクセスする権限をこのサービス プリンシパルに付与します。

1. Azure CLI 経由で Azure サブスクリプションにログインします。 次に、ブラウザーで認証します。

   ```azurecli-interactive
   az login
   ```


1. ラボの実行に使用するサブスクリプションを選択します。 この手順は、複数のサブスクリプションがある場合に必要です。

   ```azurecli-interactive
   az account set --subscription YOUR_SUBSCRIPTION_GUID
   ```

1. サービス プリンシパルを作成します。 この例では、サービス プリンシパルを `kusto-kafka-spn` と呼びます。

   ```azurecli-interactive
   az ad sp create-for-rbac -n "kusto-kafka-spn"
   ```

1. 次に示すような JSON 応答が返されます。 `appId`、`password`、`tenant` をコピーします。これらは後の手順で必要になります。

    ```json
    {
      "appId": "fe7280c7-5705-4789-b17f-71a472340429",
      "displayName": "kusto-kafka-spn",
      "name": "http://kusto-kafka-spn",
      "password": "29c719dd-f2b3-46de-b71c-4004fb6116ee",
      "tenant": "42f988bf-86f1-42af-91ab-2d7cd011db42"
    }
    ```

## <a name="create-a-target-table-in-azure-data-explorer"></a>Azure データ エクスプローラーでターゲット テーブルを作成する

1. [Azure ポータル](https://portal.azure.com)

1. 対象の Azure Data Explorer クラスターにアクセスします。

1. 次のコマンドを使用して、`Storms` というテーブルを作成します。

    ```kusto
    .create table Storms (StartTime: datetime, EndTime: datetime, EventId: int, State: string, EventType: string, Source: string)
    ```

    :::image type="content" source="media/ingest-data-kafka/create-table.png" alt-text="Azure Data Explorer ポータルでテーブルを作成する":::
    
1. 次のコマンドを使用して、取り込まれたデータに対応するテーブル マッピング `Storms_CSV_Mapping` を作成します。
    
    ```kusto
    .create table Storms ingestion csv mapping 'Storms_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EventId","datatype":"int","Ordinal":2},{"Name":"State","datatype":"string","Ordinal":3},{"Name":"EventType","datatype":"string","Ordinal":4},{"Name":"Source","datatype":"string","Ordinal":5}]'
    ```    

1. 構成可能なインジェスト待機時間用のバッチ インジェスト ポリシーをテーブルに作成します。

    > [!TIP]
    > [インジェスト バッチ処理ポリシー](kusto/management/batchingpolicy.md)はパフォーマンス オプティマイザーであり、3 つのパラメーターを含みます。 最初の条件が満たされると、Azure Data Explorer テーブルへのインジェストがトリガーされます。

    ```kusto
    .alter table Storms policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:15", "MaximumNumberOfItems": 100, "MaximumRawDataSizeMB": 300}'
    ```

1. 「[Azure Active Directory のサービス プリンシパルを作成する](#create-an-azure-active-directory-service-principal)」のサービス プリンシパルを使用して、データベースを操作する権限を付与します。

    ```kusto
    .add database YOUR_DATABASE_NAME admins  ('aadapp=YOUR_APP_ID;YOUR_TENANT_ID') 'AAD App'
    ```

## <a name="run-the-lab"></a>ラボを実行する

次のラボは、データの作成の開始、Kafka コネクタの設定、およびコネクタを使用した Azure Data Explorer へのこのデータのストリーミングのエクスペリエンスをユーザーに提供するように設計されています。 その後、取り込まれたデータを確認できます。

### <a name="clone-the-git-repo"></a>Git リポジトリをクローンする

ラボの Git [リポジトリ](https://github.com/Azure/azure-kusto-labs)をクローンします。

1. お使いのマシン上にローカル ディレクトリを作成します。

    ```
    mkdir ~/kafka-kusto-hol
    cd ~/kafka-kusto-hol
    ```

1. リポジトリをクローンします。

    ```shell
    cd ~/kafka-kusto-hol
    git clone https://github.com/Azure/azure-kusto-labs
    cd azure-kusto-labs/kafka-integration/dockerized-quickstart
    ```

#### <a name="contents-of-the-cloned-repo"></a>クローンされたリポジトリの内容

次のコマンドを実行して、クローンされたリポジトリの内容を一覧表示します。

```
cd ~/kafka-kusto-hol/azure-kusto-labs/kafka-integration/dockerized-quickstart
tree
```

この検索の結果は次のとおりです。

```
├── README.md
├── adx-query.png
├── adx-sink-config.json
├── connector
│   └── Dockerfile
├── docker-compose.yaml
└── storm-events-producer
    ├── Dockerfile
    ├── StormEvents.csv
    ├── go.mod
    ├── go.sum
    ├── kafka
    │   └── kafka.go
    └── main.go
 ```

### <a name="review-the-files-in-the-cloned-repo"></a>クローンされたリポジトリ内のファイルを確認する

以下のセクションでは、上記のファイル ツリーのファイルの重要な部分について説明します。

#### <a name="adx-sink-configjson"></a>adx-sink-config.json

このファイルには、特定の構成の詳細を更新する Kusto シンク プロパティ ファイルが含まれています。
 
```json
{
    "name": "storm",
    "config": {
        "connector.class": "com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector",
        "flush.size.bytes": 10000,
        "flush.interval.ms": 10000,
        "tasks.max": 1,
        "topics": "storm-events",
        "kusto.tables.topics.mapping": "[{'topic': 'storm-events','db': '<enter database name>', 'table': 'Storms','format': 'csv', 'mapping':'Storms_CSV_Mapping'}]",
        "aad.auth.authority": "<enter tenant ID>",
        "aad.auth.appid": "<enter application ID>",
        "aad.auth.appkey": "<enter client secret>",
        "kusto.url": "https://ingest-<name of cluster>.<region>.kusto.windows.net",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.storage.StringConverter"
    }
}
```

Azure Data Explorer のセットアップに従って、`aad.auth.authority`、`aad.auth.appid`、`aad.auth.appkey`、`kusto.tables.topics.mapping` (データベース名)、`kusto.url` の各属性の値を置き換えてください。

#### <a name="connector---dockerfile"></a>connector - Dockerfile

このファイルには、コネクタ インスタンス用の Docker イメージを生成するコマンドが含まれています。  これには、Git リポジトリのリリース ディレクトリからのコネクタのダウンロードが含まれています。

#### <a name="storm-events-producer-directory"></a>storm-events-producer ディレクトリ

このディレクトリには、ローカルの "StormEvents.csv" ファイルを読み取り、そのデータを Kafka トピックに発行する Go プログラムがあります。

#### <a name="docker-composeyaml"></a>docker-compose.yaml

```yaml
version: "2"
services:
  zookeeper:
    image: debezium/zookeeper:1.2
    ports:
      - 2181:2181
  kafka:
    image: debezium/kafka:1.2
    ports:
      - 9092:9092
    links:
      - zookeeper
    depends_on:
      - zookeeper
    environment:
      - ZOOKEEPER_CONNECT=zookeeper:2181
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092
  kusto-connect:
    build:
      context: ./connector
      args:
        KUSTO_KAFKA_SINK_VERSION: 1.0.1
    ports:
      - 8083:8083
    links:
      - kafka
    depends_on:
      - kafka
    environment:
      - BOOTSTRAP_SERVERS=kafka:9092
      - GROUP_ID=adx
      - CONFIG_STORAGE_TOPIC=my_connect_configs
      - OFFSET_STORAGE_TOPIC=my_connect_offsets
      - STATUS_STORAGE_TOPIC=my_connect_statuses
  events-producer:
    build:
      context: ./storm-events-producer
    links:
      - kafka
    depends_on:
      - kafka
    environment:
      - KAFKA_BOOTSTRAP_SERVER=kafka:9092
      - KAFKA_TOPIC=storm-events
      - SOURCE_FILE=StormEvents.csv
```

### <a name="start-the-containers"></a>コンテナーを開始する

1. ターミナルで、コンテナーを開始します。
    
    ```shell
    docker-compose up
    ```

    プロデューサー アプリケーションによって、`storm-events` トピックへのイベントの送信が開始されます。 
    次のようなログが表示されます。

    ```shell
    ....
    events-producer_1  | sent message to partition 0 offset 0
    events-producer_1  | event  2007-01-01 00:00:00.0000000,2007-01-01 00:00:00.0000000,13208,NORTH CAROLINA,Thunderstorm Wind,Public
    events-producer_1  | 
    events-producer_1  | sent message to partition 0 offset 1
    events-producer_1  | event  2007-01-01 00:00:00.0000000,2007-01-01 05:00:00.0000000,23358,WISCONSIN,Winter Storm,COOP Observer
    ....
    ```
    
1. ログを確認するには、別のターミナルで次のコマンドを実行します。

    ```shell
    docker-compose logs -f | grep kusto-connect
    ```
    
### <a name="start-the-connector"></a>コネクタを開始する

Kafka Connect REST 呼び出しを使用して、コネクタを開始します。

1. 別のターミナルで、次のコマンドを使用してシンク タスクを起動します。

    ```shell
    curl -X POST -H "Content-Type: application/json" --data @adx-sink-config.json http://localhost:8083/connectors
    ```
    
1. 状態を確認するには、別のターミナルで次のコマンドを実行します。
    
    ```shell
    curl http://localhost:8083/connectors/storm/status
    ```

コネクタにより、Azure Data Explorer へのインジェスト プロセスがキューに格納され始めます。

> [!NOTE]
> ログ コネクタの問題がある場合は、[issue を作成します](https://github.com/Azure/kafka-sink-azure-kusto/issues)。

## <a name="query-and-review-data"></a>データのクエリを実行して確認する

### <a name="confirm-data-ingestion"></a>データ インジェストを確認する

1. `Storms` テーブルにデータが到着するのを待ちます。 データの転送を確認するには、その行数を確認します。
    
    ```kusto
    Storms | count
    ```

1. インジェスト プロセスでエラーが発生していないことを確認します。

    ```kusto
    .show ingestion failures
    ```
    
    データを確認したら、クエリをいくつか試してみてください。 

### <a name="query-the-data"></a>データにクエリを実行する

1. すべてのレコードを表示するには、次の[クエリ](write-queries.md)を実行します。
    
    ```kusto
    Storms
    ```

1. `where` と `project` を使用して、特定のデータをフィルター処理します。
    
    ```kusto
    Storms
    | where EventType == 'Drought' and State == 'TEXAS'
    | project StartTime, EndTime, Source, EventId
    ```
    
1. [`summarize`](./write-queries.md#summarize) 演算子を使用します。

    ```kusto
    Storms
    | summarize event_count=count() by State
    | where event_count > 10
    | project State, event_count
    | render columnchart
    ```
    
    :::image type="content" source="media/ingest-data-kafka/kusto-query.png" alt-text="Azure Data Explorer での Kafka クエリの縦棒グラフの結果":::

その他のクエリの例とガイダンスについては、「[Azure Data Explorer のクエリを記述する](write-queries.md)」と [Kusto クエリ言語のドキュメント](./kusto/query/index.md)を参照してください。

## <a name="reset"></a>リモート アクセスの

リセットするには、次の手順を実行します。

1. コンテナーを停止します (`docker-compose down -v`)
1. [削除] (`drop table Storms`)
1. `Storms` テーブルを再作成します
1. テーブル マッピングを再作成します
1. コンテナーを再起動します (`docker-compose up`)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Data Explorer リソースを削除するには、[az cluster delete](/cli/azure/kusto/cluster#az-kusto-cluster-delete) または [az Kusto database delete](/cli/azure/kusto/database#az-kusto-database-delete) を使用します。

```azurecli-interactive
az kusto cluster delete -n <cluster name> -g <resource group name>
az kusto database delete -n <database name> --cluster-name <cluster name> -g <resource group name>
```

## <a name="next-steps"></a>次の手順

* [ビッグ データ アーキテクチャ](/azure/architecture/solution-ideas/articles/big-data-azure-data-explorer)の詳細を確認します。
* [Azure Data Explorer に JSON 書式付きサンプル データを取り込む方法](./ingest-json-formats.md?tabs=kusto-query-language)を確認します。
* その他の Kafka ラボについて:
   * [分散モードでの Confluent Cloud Kafka からのインジェストのハンズオン ラボ](https://github.com/Azure/azure-kusto-labs/blob/master/kafka-integration/confluent-cloud/README.md)
   * [分散モードでの HDInsight Kafka からのインジェストのハンズオン ラボ](https://github.com/Azure/azure-kusto-labs/tree/master/kafka-integration/distributed-mode/hdinsight-kafka)
   * [分散モードでの AKS 上の Confluent IaaS Kafka からのインジェストのハンズオン ラボ](https://github.com/Azure/azure-kusto-labs/blob/master/kafka-integration/distributed-mode/confluent-kafka/README.md)
