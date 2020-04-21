---
title: Kusto.INGEST ライブラリを使用したデータの取り込み方法 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで Kusto.INGEST ライブラリを使用したデータの取り込み方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/05/2020
ms.openlocfilehash: 80b2b61c70269c5bd166a064fe9d0e2c59dd8197
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523632"
---
# <a name="howto-data-ingestion-with-kustoingest-library"></a>Kusto.Ingest ライブラリを使用したデータの取り込み方法
この資料では、Kusto.Ingest クライアント ライブラリを使用するサンプル コードを紹介します。

## <a name="overview"></a>概要
次のコード サンプルは、Kusto.Ingest ライブラリを使用して Kusto へのキューイング (Kusto データ管理サービス経由で行う) データの取り込み方法を示しています。

> この記事では、プロダクション グレードのパイプラインの推奨されるインジェスト モードについて説明します(**キューインジェスト**とも呼ばれます ( Kusto.Ingest ライブラリの観点から言うと、対応するエンティティは[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)インターフェイスです ) 。 このモードでは、クライアント コードは、Azure キューにインジェスト通知メッセージを送信することで Kusto サービスとやり取りします。 インジェスティオン)サービス。 データ管理サービスとの対話は **、 AAD**で認証される必要があります。

#### <a name="authentication"></a>認証
このコード サンプルでは、AAD ユーザー認証を使用し、対話ユーザーの ID で実行されます。

## <a name="dependencies"></a>依存関係
このサンプル コードには、次の NuGet パッケージが必要です。
* マイクロソフト.クスト.インジェスト
* Microsoft.IdentityModel.Clients.ActiveDirectory
* WindowsAzure.Storage
* Newtonsoft.Json

## <a name="namespaces-used"></a>使用される名前空間
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Kusto.Data;
using Kusto.Data.Common;
using Kusto.Data.Net.Client;
using Kusto.Ingest;
```

## <a name="code"></a>コード
以下に示すコードは、次の処理を実行します。
1. データベースの下に`KustoLab`共有 Kusto`KustoIngestClientDemo`クラスタ上にテーブルを作成します。
2. そのテーブルに[JSON 列マッピングオブジェクト](../../management/create-ingestion-mapping-command.md)をプロビジョニングします。
3. データ管理サービスの[IKustoQueueDIngest](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) `Ingest-KustoLab`インスタンスを作成します。
4. 適切な[取り込みオプションを使用して KustoQueuedIngestion プロパティ](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)を設定します
5. 取り込まれる生成されたデータを格納するメモリストリームを作成します。
6. メソッドを使用してデータを`KustoQueuedIngestClient.IngestFromStream`取り込む
7. イン[ジェクション エラー](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient)のポーリング

```csharp
static void Main(string[] args)
{
    var clusterName = "KustoLab";
    var db = "KustoIngestClientDemo";
    var table = "Table1";
    var mappingName = "Table1_mapping_1";
    var serviceNameAndRegion = "clusterNameAndRegion"; // For example, "mycluster.westus"
    var authority = "AAD Tenant or name"; // For example, "microsoft.com"

    // Set up table
    var kcsbEngine =
        new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net").WithAadUserPromptAuthentication(authority: $"{authority}");

    using (var kustoAdminClient = KustoClientFactory.CreateCslAdminProvider(kcsbEngine))
    {
        var columns = new List<Tuple<string, string>>()
        {
            new Tuple<string, string>("Column1", "System.Int64"),
            new Tuple<string, string>("Column2", "System.DateTime"),
            new Tuple<string, string>("Column3", "System.String"),
        };

        var command = CslCommandGenerator.GenerateTableCreateCommand(table, columns);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: command);

        // Set up mapping
        var columnMappings = new List<JsonColumnMapping>();
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column1", JsonPath = "$.Id" });
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column2", JsonPath = "$.Timestamp" });
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column3", JsonPath = "$.Message" });

        command = CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
                                            table, mappingName, columnMappings);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: command);
    }

    // Create Ingest Client
    var kcsbDM =
        new KustoConnectionStringBuilder($"https://ingest-{serviceNameAndRegion}.kusto.windows.net").WithAadUserPromptAuthentication(authority: $"{authority}");

    using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(kcsbDM))
    {
        var ingestProps = new KustoQueuedIngestionProperties(db, table);
        // For the sake of getting both failure and success notifications we set this to IngestionReportLevel.FailuresAndSuccesses
        // Usually the recommended level is IngestionReportLevel.FailuresOnly
        ingestProps.ReportLevel = IngestionReportLevel.FailuresAndSuccesses;
        ingestProps.ReportMethod = IngestionReportMethod.Queue;
        ingestProps.JSONMappingReference = mappingName;
        ingestProps.Format = DataSourceFormat.json;

        // Prepare data for ingestion
        using (var memStream = new MemoryStream())
        using (var writer = new StreamWriter(memStream))
        {
            for (int counter = 1; counter <= 10; ++counter)
            {
                writer.WriteLine(
                    "{{ \"Id\":\"{0}\", \"Timestamp\":\"{1}\", \"Message\":\"{2}\" }}",
                    counter, DateTime.UtcNow.AddSeconds(100 * counter),
                    $"This is a dummy message number {counter}");
            }

            writer.Flush();
            memStream.Seek(0, SeekOrigin.Begin);

            // Post ingestion message
            ingestClient.IngestFromStream(memStream, ingestProps, leaveOpen: true);
        }

        // Wait and retrieve all notifications
        //  - Actual duration should be decided based on the effective Ingestion Batching Policy set on the table/database
        Thread.Sleep(<timespan>);
        var errors = ingestClient.GetAndDiscardTopIngestionFailures().GetAwaiter().GetResult();
        var successes = ingestClient.GetAndDiscardTopIngestionSuccesses().GetAwaiter().GetResult();

        errors.ForEach((f) => { Console.WriteLine($"Ingestion error: {f.Info.Details}"); });
        successes.ForEach((s) => { Console.WriteLine($"Ingested: {s.Info.IngestionSourcePath}"); });
    }
}
```