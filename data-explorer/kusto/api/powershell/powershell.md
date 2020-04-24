---
title: PowerShell から .NET クライアント ライブラリを使用する - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで PowerShell から .NET クライアント ライブラリを使用する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 635a23021a1a8c30347bfa27ecd65886b46a6fea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503215"
---
# <a name="using-the-net-client-libraries-from-powershell"></a>PowerShell から .NET クライアント ライブラリを使用する

Azure データ エクスプローラー .NET クライアント ライブラリは、PowerShell の組み込みの統合を通じて PowerShell スクリプトで使用できます。

## <a name="getting-the-net-client-libraries-for-scripting-with-powershell"></a>PowerShell を使用したスクリプト用の .NET クライアント ライブラリの取得

PowerShell を使用して Azure データ エクスプローラー .NET クライアント ライブラリの使用を開始するには、次の手順を実行します。

1. [ `Microsoft.Azure.Kusto.Tools` NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)をダウンロードします。
2. パッケージ内の 'tools' ディレクトリの内容を抽出します (例えば、7-zip を使用)。
3. 必要`[System.Reflection.Assembly]::LoadFrom("path")`なライブラリをロードするために、powershell から呼び出します。 
    - コマンド`"path"`のパラメーターは、抽出されたファイルの場所を示す必要があります。
4. すべての依存 .NET アセンブリが読み込まれたら、Kusto 接続文字列を作成し、*クエリ プロバイダ*または*管理プロバイダ*をインスタンス化し、クエリまたはコマンドを実行します (以下の[例](powershell.md#examples)に示すように)。

詳細については、 [Azure データ エクスプローラー クライアント ライブラリ](../netfx/about-kusto-data.md)を参照してください。

## <a name="examples"></a>例

### <a name="initialization"></a>初期化

```powershell
#  Part 1 of 3
#  ------------
#  Packages location - This is an example to the location where you extract the Microsoft.Azure.Kusto.Tools package.
#  Please make sure you load the types from a local directory and not from a remote share.
$packagesRoot = "C:\Microsoft.Azure.Kusto.Tools\Tools"

#  Part 2 of 3
#  ------------
#  Loading the Kusto.Client library and its dependencies
dir $packagesRoot\* | Unblock-File
[System.Reflection.Assembly]::LoadFrom("$packagesRoot\Kusto.Data.dll")

#  Part 3 of 3
#  ------------
#  Defining the connection to your cluster / database
$clusterUrl = "https://help.kusto.windows.net;Fed=True"
$databaseName = "Samples"

#   Option A: using AAD User Authentication
$kcsb = New-Object Kusto.Data.KustoConnectionStringBuilder ($clusterUrl, $databaseName)
 
#   Option B: using AAD application Authentication
#     $applicationId = "application ID goes here"
#     $applicationKey = "application key goes here"
#     $authority = "authority goes here"
#     $kcsb = $kcsb.WithAadApplicationKeyAuthentication($applicationId, $applicationKey, $authority)
```

### <a name="example-running-an-admin-command"></a>例: 管理コマンドの実行

```powershell
$adminProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslAdminProvider($kcsb)
$command = [Kusto.Data.Common.CslCommandGenerator]::GenerateDiagnosticsShowCommand()
Write-Host "Executing command: '$command' with connection string: '$($kcsb.ToString())'"
$reader = $adminProvider.ExecuteControlCommand($command)
$reader.Read() # this reads a single row/record. If you have multiple ones returned, you can read in a loop 
$isHealthy = $Reader.GetBoolean(0)
Write-Host "IsHealthy = $isHealthy"
```

そして、出力は次のとおりです。
```
IsHealthy = True
```

### <a name="example-running-a-query"></a>例: クエリの実行

```powershell
$queryProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslQueryProvider($kcsb)
$query = "StormEvents | limit 5"
Write-Host "Executing query: '$query' with connection string: '$($kcsb.ToString())'"
#   Optional: set a client request ID and set a client request property (e.g. Server Timeout)
$crp = New-Object Kusto.Data.Common.ClientRequestProperties
$crp.ClientRequestId = "MyPowershellScript.ExecuteQuery." + [Guid]::NewGuid().ToString()
$crp.SetOption([Kusto.Data.Common.ClientRequestProperties]::OptionServerTimeout, [TimeSpan]::FromSeconds(30))

#   Execute the query
$reader = $queryProvider.ExecuteQuery($query, $crp)

# Do something with the result datatable, for example: print it formatted as a table, sorted by the 
# "StartTime" column, in descending order
$dataTable = [Kusto.Cloud.Platform.Data.ExtendedDataReader]::ToDataSet($reader).Tables[0]
$dataView = New-Object System.Data.DataView($dataTable)
$dataView | Sort StartTime -Descending | Format-Table -AutoSize
```

そして、出力は次のとおりです。

|StartTime           |EndTime             |エピソードId |EventId |State          |EventType         |負傷者ダイレクト |間接的な傷害 |デスダイレクト |デス・インダイレクト
|---------           |-------             |--------- |------- |-----          |---------         |-------------- |---------------- |------------ |--------------
|2007-12-30 16:00:00 |2007-12-30 16:05:00 |    11749 |  64588 |グルジア        |雷雨風 |             0 |               0 |           0 |             0
|2007-12-20 07:50:00 |2007-12-20 07:53:00 |    12554 |  68796 |ミシシッピ    |雷雨風 |             0 |               0 |           0 |             0
|2007-09-29 08:11:00 |2007-09-29 08:11:00 |    11091 |  61032 |アトランティック サウス |竜巻        |             0 |               0 |           0 |             0
|2007-09-20 21:57:00 |2007-09-20 22:05:00 |    11078 |  60913 |フロリダ        |竜巻           |             0 |               0 |           0 |             0
|2007-09-18 20:00:00 |2007-09-19 18:00:00 |    11074 |  60904 |フロリダ        |大雨        |             0 |               0 |           0 |             0