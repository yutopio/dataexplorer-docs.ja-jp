---
title: Azure Data Explorer .NET SDK を使用してデータを取り込む
description: この記事では、.NET SDK を使用して Azure Data Explorer にデータを取り込む (読み込む) 方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: vladikb
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: c6a544228d5527f1703567256bd3e824ddc0504a
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872711"
---
# <a name="ingest-data-using-the-azure-data-explorer-net-sdk"></a>Azure Data Explorer NET SDK 使用してデータを取り込む 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Node](node-ingest-data.md)
> * [Go](go-ingest-data.md)

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 これには、.NET 用のクライアント ライブラリとして、[取り込みライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest/)と[データ ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data/)の 2 つが用意されています。 .NET SDK の詳細については、[.NET SDKについて](/azure/data-explorer/kusto/api/netfx/about-the-sdk)の記事を参照してください。
これらのライブラリを使用すると、クラスターにデータを取り込み (読み込み)、コードからデータのクエリを行うことができます。 この記事ではまず、テスト クラスター内にテーブルとデータ マッピングを作成します。 その後、クラスターに対するインジェストをキューに入れて、結果を検証します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

* [テスト用のクラスターとデータベース](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>取り込みライブラリをインストールする

```azurecli
Install-Package Microsoft.Azure.Kusto.Ingest
```

## <a name="add-authentication-and-construct-connection-string"></a>認証の追加と接続文字列の作成

### <a name="authentication"></a>認証

Azure Data Explorer SDK では、アプリケーションを認証するために AAD テナント ID が使用されます。 テナント ID を検索するには、次の URL を使用し、ドメインを *YourDomain* に置き換えます。

```http
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

たとえば、ドメインが *contoso.com* の場合、URL は [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/) になります。 結果を表示するには、この URL をクリックします。最初の行は次のとおりです。 

```console
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

この場合のテナント ID は `6babcaad-604b-40ac-a9d7-9fd97c0b779f` です。

この例では、クラスターにアクセスするための対話式の AAD ユーザー認証を使用します。 証明書またはアプリケーション シークレットによる AAD アプリケーション認証を使用することもできます。 このコードを実行する前に、`tenantId` と `clusterUri`に正しい値を設定してください。 

Azure Data Explorer SDK には、接続文字列の一部として認証方法を設定する便利な方法が用意されています。 Azure Data Explorer 接続文字列の完全なドキュメントについては、[接続文字列](kusto/api/connection-strings/kusto.md)を参照してください。

> [!NOTE]
> 現在のバージョンの SDK では、.NET Core での対話型のユーザー認証はサポートされていません。 必要に応じて、AAD のユーザー名/パスワードまたはアプリケーション認証を代わりに使用してください。

### <a name="construct-the-connection-string"></a>接続文字列を作成する

これで、Azure Data Explorer 接続文字列を作成できるようになりました。 ターゲット テーブルとマッピングは後のステップで作成します。

```csharp
var tenantId = "<TenantId>";
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net/";

var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri).WithAadUserPromptAuthentication(tenantId);
```

## <a name="set-source-file-information"></a>ソース ファイルの情報を設定する

ソース ファイルのパスを設定します。 この例では、Azure Blob Storage でホストされているサンプル ファイルを使います。 **StormEvents**サンプル データ セットには､ [National Centers for Environmental Information から入手した気象関連データが含まれています](https://www.ncdc.noaa.gov/stormevents/)｡

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>テスト クラスターにテーブルを作成する

`StormEvents.csv` ファイル内のデータのスキーマと一致する、`StormEvents` という名前のテーブルを作成します。

> [!TIP]
> 次のコード スニペットは、ほぼすべての呼び出しに対してクライアントのインスタンスを作成します。 これは、各スニペットを個別に実行できるようにするためです。 実稼働環境では、クライアント インスタンスは再入可能であり、必要な限り保持する必要があります。 複数のデータベースを使用する場合でも、URI ごとに 1 つのクライアントインスタンスで十分です (データベースはコマンド レベルで指定できます)。

```csharp
var databaseName = "<DatabaseName>";
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("InjuriesIndirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(databaseName, command);
}
```

## <a name="define-ingestion-mapping"></a>インジェストのマッピングを定義する

受信した CSV データを、テーブル作成時に使用される列名にマップします。
[CSV 列マッピング オブジェクト](kusto/management/create-ingestion-mapping-command.md)をそのテーブルにプロビジョニングします。

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Csv,
            table,
            tableMapping,
            new[] {
                new ColumnMapping() { ColumnName = "StartTime", Properties = new Dictionary<string, string>() { { MappingConsts.Ordinal, "0" } } },
                new ColumnMapping() { ColumnName = "EndTime", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "1" } } },
                new ColumnMapping() { ColumnName = "EpisodeId", Properties = new Dictionary<string, string>() { { MappingConsts.Ordinal, "2" } } },
                new ColumnMapping() { ColumnName = "EventId", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "3" } } },
                new ColumnMapping() { ColumnName = "State", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "4" } } },
                new ColumnMapping() { ColumnName = "EventType", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "5" } } },
                new ColumnMapping() { ColumnName = "InjuriesDirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "6" } } },
                new ColumnMapping() { ColumnName = "InjuriesIndirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "7" } } },
                new ColumnMapping() { ColumnName = "DeathsDirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "8" } } },
                new ColumnMapping() { ColumnName = "DeathsIndirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "9" } } },
                new ColumnMapping() { ColumnName = "DamageProperty", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "10" } } },
                new ColumnMapping() { ColumnName = "DamageCrops", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "11" } } },
                new ColumnMapping() { ColumnName = "Source", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "12" } } },
                new ColumnMapping() { ColumnName = "BeginLocation", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "13" } } },
                new ColumnMapping() { ColumnName = "EndLocation", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "14" } } },
                new ColumnMapping() { ColumnName = "BeginLat", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "15" } } },
                new ColumnMapping() { ColumnName = "BeginLon", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "16" } } },
                new ColumnMapping() { ColumnName = "EndLat", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "17" } } },
                new ColumnMapping() { ColumnName = "EndLon", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "18" } } },
                new ColumnMapping() { ColumnName = "EpisodeNarrative", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "19" } } },
                new ColumnMapping() { ColumnName = "EventNarrative", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "20" } } },
                new ColumnMapping() { ColumnName = "StormSummary", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "21" } } }
        });

    kustoClient.ExecuteControlCommand(databaseName, command);
}
```

## <a name="define-batching-policy-for-your-table"></a>テーブルのバッチ処理ポリシーを定義する

Azure Data Explorer のインジェストでは、受信データのバッチ処理を実行して、データのシャード サイズが最適化されます。 このプロセスは [インジェスト バッチ処理ポリシー](kusto/management/batchingpolicy.md)によって制御され、[インジェスト バッチ処理ポリシー制御コマンド](kusto/management/batching-policy.md)によって変更できます。 このポリシーを使用すると、低速なデータの待機時間を短縮できます。

```kusto
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableAlterIngestionBatchingPolicyCommand(
        databaseName,
        table,
        new IngestionBatchingPolicy(maximumBatchingTimeSpan: TimeSpan.FromSeconds(10.0), maximumNumberOfItems: 100, maximumRawDataSizeMB: 1024));

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>取り込みのためにメッセージをキューに入れる

BLOB ストレージからデータをプルし、そのデータを Azure Data Explorer に取り込むために、メッセージをキューに入れます。 Azure Data Explorer クラスターのデータ インジェスト エンドポイントへの接続が確立され、そのエンドポイントを操作するために別のクライアントが作成されます。 

> [!TIP]
> 次のコード スニペットは、ほぼすべての呼び出しに対してクライアントのインスタンスを作成します。 これは、各スニペットを個別に実行できるようにするためです。 実稼働環境では、クライアント インスタンスは再入可能であり、必要な限り保持する必要があります。 複数のデータベースを使用する場合でも、URI ごとに 1 つのクライアントインスタンスで十分です (データベースはコマンド レベルで指定できます)。

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net";
var ingestConnectionStringBuilder = new KustoConnectionStringBuilder(ingestUri).WithAadUserPromptAuthentication(tenantId);

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            IngestionMapping = new IngestionMapping()
            { 
                IngestionMappingReference = tableMapping,
                IngestionMappingKind = IngestionMappingKind.Csv
            },
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromStorageAsync(blobPath, ingestionProperties: properties).GetAwaiter().GetResult();
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>データがテーブルに取り込まれたことを確認する

キューに入れられたインジェストが取り込みをスケジュールされて、Azure Data Explorer にデータが読み込まれるまで、5 から 10 分待ちます。 その後、次のコードを実行して、`StormEvents` テーブル内のレコードの数を取得します。

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(databaseName, query);
    Console.WriteLine(results.Single());
}
```

## <a name="run-troubleshooting-queries"></a>トラブルシューティングのクエリを実行する

[https://dataexplorer.azure.com](https://dataexplorer.azure.com) にサインインして、クラスターに接続します。 データベースで次のコマンドを実行し、過去 4 時間以内にインジェスト エラーがあったかどうかを調べます。 実行する前にデータベース名を置き換えてください。

```kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

次のコマンドを実行し、過去 4 時間以内のすべてのインジェスト操作の状態を表示します。 実行する前にデータベース名を置き換えてください。

```kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他の記事に進む場合は、作成したリソースをそのままにします。 行わない場合は、データベースで次のコマンドを実行して、`StormEvents` テーブルをクリーンアップします。

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>次のステップ

* [クエリを作成する](write-queries.md)
