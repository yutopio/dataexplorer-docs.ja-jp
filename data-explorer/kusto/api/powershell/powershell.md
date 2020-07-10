---
title: PowerShell からの kusto .NET クライアントライブラリ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーで PowerShell から .NET クライアントライブラリを使用する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 6804b71ff3985de17460dddfa60f081f3bb910c0
ms.sourcegitcommit: b286703209f1b657ac3d81b01686940f58e5e145
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86188423"
---
# <a name="using-the-net-client-libraries-from-powershell"></a>PowerShell からの .NET クライアントライブラリの使用

Powershell スクリプトは、任意の (PowerShell 以外の) .NET ライブラリとの PowerShell の組み込み統合によって、Azure データエクスプローラー .NET クライアントライブラリを使用できます。

## <a name="getting-the-net-client-libraries-for-scripting-with-powershell"></a>PowerShell を使用してスクリプトを作成するための .NET クライアントライブラリの取得

PowerShell を使用して Azure データエクスプローラー .NET クライアントライブラリの操作を開始するには

1. [ `Microsoft.Azure.Kusto.Tools` NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)をダウンロードします。
    * Powershell 7 (またはそれ以上) を使用している場合は、 [ `Microsoft.Azure.Kusto.Tools.NETCore` NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools.NETCore/)をダウンロードします。
1. パッケージ内の ' tools ' ディレクトリの内容を抽出します (などのアーカイブツールを使用し `7-zip` ます)。
1. `[System.Reflection.Assembly]::LoadFrom("path")`PowerShell からを呼び出して、必要なライブラリを読み込みます。 
    * コマンドのパラメーターは、抽出された `path` ファイルの場所を示します。
1. すべての依存 .NET アセンブリが読み込まれると、次のようになります。
   1. Kusto 接続文字列を作成します。
   1. *クエリプロバイダー*または*管理プロバイダー*をインスタンス化します。
   1. 次の[例](powershell.md#examples)に示すように、クエリまたはコマンドを実行します。

詳細については、「 [Azure データエクスプローラークライアントライブラリ](../netfx/about-kusto-data.md)」を参照してください。

## <a name="examples"></a>例

### <a name="initialization"></a>初期化

```powershell
#  Part 1 of 3
#  ------------
#  Packages location - This is an example of the location from where you extract the Microsoft.Azure.Kusto.Tools package.
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

#   Option A: using Azure AD User Authentication
$kcsb = New-Object Kusto.Data.KustoConnectionStringBuilder ($clusterUrl, $databaseName)
 
#   Option B: using Azure AD application Authentication
#     $applicationId = "application ID goes here"
#     $applicationKey = "application key goes here"
#     $authority = "authority goes here"
#     $kcsb = $kcsb.WithAadApplicationKeyAuthentication($applicationId, $applicationKey, $authority)
#
#   NOTE: if you're running with Powershell 7 (or above) and the .NET Core library,
#         AAD user authentication with prompt will not work, and you should choose
#         a different authentication method.
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

出力は次のようになります。
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

出力は次のようになります。

|StartTime           |EndTime             |EpisodeID |EventID |状態          |EventType         |InjuriesDirect |InjuriesIndirect |DeathsDirect |DeathsIndirect
|---------           |-------             |--------- |------- |-----          |---------         |-------------- |---------------- |------------ |--------------
|2007-12-30 16:00:00 |2007-12-30 16:05:00 |    11749 |  64588 |グルジア        |雷雨風 |             0 |               0 |           0 |             0
|2007-12-20 07:50:00 |2007-12-20 07:53:00 |    12554 |  68796 |MISSISSIPPI    |雷雨風 |             0 |               0 |           0 |             0
|2007-09-29 08:11:00 |2007-09-29 08:11:00 |    11091 |  61032 |大西洋南部 |水スパウト       |             0 |               0 |           0 |             0
|2007-09-20 21:57:00 |2007-09-20 22:05:00 |    11078 |  60913 |フロリダ        |Tornado           |             0 |               0 |           0 |             0
|2007-09-18 20:00:00 |2007-09-19 18:00:00 |    11074 |  60904 |フロリダ        |重い雨        |             0 |               0 |           0 |             0
