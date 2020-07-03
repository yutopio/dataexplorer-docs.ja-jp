---
title: Kusto を使用したデータインジェスト-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Kusto. インジェストライブラリを使用したデータインジェストについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/05/2020
ms.openlocfilehash: 6963c118742593d2402d5ae81d8ff4373a2ff600
ms.sourcegitcommit: d40fe44e7581d87f63cc0cb939f3aa9c3996fc08
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85839428"
---
# <a name="data-ingestion-with-the-kustoingest-library"></a>Kusto インジェストライブラリを使用したデータインジェスト

この記事では、データインジェストのために Kusto. インジェストクライアントライブラリを使用するサンプルコードを示します。 このコードは、キューインジェストと呼ばれる、運用レベルのパイプラインのインジェストの推奨モードを詳細に説明しています。 Kusto. インジェストライブラリの場合、対応するエンティティは[IKustoQueuedIngestClient インターフェイス](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)です。
クライアントコードは、azure キューにインジェスト通知を送信することによって、Azure データエクスプローラーサービスとやり取りします。 キューへの参照は、インジェストを担当するデータ管理エンティティから取得されます。 

> [!NOTE]
> データ管理サービスとの対話は、Azure Active Directory (Azure AD) を使用して認証される必要があります。

このサンプルコードでは Azure AD ユーザー認証を使用し、対話型ユーザーの id で実行します。

## <a name="dependencies"></a>依存関係

このサンプルコードでは、次の NuGet パッケージが必要です。
* Microsoft. Kusto. インジェスト
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

このコードでは、次のことを行います。
1. `KustoLab`データベースの下にある共有 Azure データエクスプローラークラスターにテーブルを作成します。 `KustoIngestClientDemo`
2. そのテーブルで[JSON 列マッピングオブジェクト](../../management/create-ingestion-mapping-command.md)をプロビジョニングします。
3. データ管理サービスの[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)インスタンスを作成します。 `Ingest-KustoLab`
4. 適切なインジェストオプションを使用して[KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)を設定する
5. 生成されたデータを取り込まれたに格納して MemoryStream を作成します。
6. メソッドを使用してデータを取り込みする `KustoQueuedIngestClient.IngestFromStream`
7. [インジェストエラー](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient)をポーリングします。

```csharp
static void Main(string[] args)
{
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
        var columnMappings = new List<ColumnMapping>();
            columnMappings.Add(new ColumnMapping()
            {
                ColumnName = "Column1",
                Properties = new Dictionary<string, string>() {
                    { Data.Common.MappingConsts.Path, "$.Id" },
            } });
            columnMappings.Add(new ColumnMapping()
            {
                ColumnName = "Column2",
                Properties = new Dictionary<string, string>() {
                    { Data.Common.MappingConsts.Path, "$.Timestamp" },
            }
            });
            columnMappings.Add(new ColumnMapping()
            {
                ColumnName = "Column3",
                Properties = new Dictionary<string, string>() {
                    { Data.Common.MappingConsts.Path, "$.Message" },
            }
            });
            var secondCommand = CslCommandGenerator.GenerateTableMappingCreateCommand(
                Data.Ingestion.IngestionMappingKind.Json, table, mappingName, columnMappings);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: secondCommand);
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
        ingestProps.IngestionMapping = new IngestionMapping()
        { 
            IngestionMappingReference = mappingName
        };
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
        var errors = ingestClient.GetAndDiscardTopIngestionFailuresAsync().GetAwaiter().GetResult();
        var successes = ingestClient.GetAndDiscardTopIngestionSuccessesAsync().GetAwaiter().GetResult();

        errors.ForEach((f) => { Console.WriteLine($"Ingestion error: {f.Info.Details}"); });
        successes.ForEach((s) => { Console.WriteLine($"Ingested: {s.Info.IngestionSourcePath}"); });
    }
}
```
