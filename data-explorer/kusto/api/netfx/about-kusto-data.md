---
title: Kusto クライアント ライブラリ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの Kusto クライアント ライブラリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/15/2020
ms.openlocfilehash: 8a3ab9bb3727efdc80e1887ab802f2ad4e3c7cce
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502603"
---
# <a name="kusto-client-library"></a>Kusto クライアント ライブラリ
    
Kusto クライアント SDK (Kusto.Data) は、ADO.NETに似たプログラム API を公開するため、.NET を経験したユーザーにとっては、この API を使用するのが自然な感じになります。 Kusto エンジン サービス`ICslQueryProvider`、データベース、認証メソッドなどを指`ICslAdminProvider`す接続文字列オブジェクトから、クエリ クライアント ( ) または制御コマンド プロバイダ ( ) を作成します。次に、適切な Kusto クエリ言語文字列を指定してデータ クエリまたはコントロール コマンドを発行し、返された`IDataReader`オブジェクトを介して 1 つ以上のデータ テーブルを取得できます。

より具体的には、Kusto に対するクエリを許可するADO.NETのようなクライアントを作成するには、クラスで`Kusto.Data.Net.Client.KustoClientFactory`静的メソッドを使用します。 これらは接続文字列を受け取り、スレッド セーフで使い捨てのクライアント オブジェクトを作成します。 (クライアント コードでは、このオブジェクトの "多すぎる" インスタンスを作成しないことを強くお勧めします。 代わりに、クライアント コードは接続文字列ごとにオブジェクトを作成し、必要な限り保持する必要があります。これにより、クライアント オブジェクトは効率的にリソースをキャッシュできます。

一般に、クライアント上のすべてのメソッドは、2 つの例外を除いて`Dispose`スレッド セーフです。 一貫した結果を得るには、どちらのメソッドも同時に呼び出しません。

以下に例を示します。 その他のサンプルは[、こちらからご覧いただけます](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client)。

**例: 行のカウント**
 
次のコードは、 という名前`StormEvents``Samples`のデータベース内のテーブルの行数を示しています。

```csharp
var client = Kusto.Data.Net.Client.KustoClientFactory.CreateCslQueryProvider("https://help.kusto.windows.net/Samples;Fed=true");
var reader = client.ExecuteQuery("StormEvents | count");
// Read the first row from reader -- it's 0'th column is the count of records in MyTable
// Don't forget to dispose of reader when done.
```

**例: Kusto クラスターからの診断情報の取得**

```csharp
var kcsb = new KustoConnectionStringBuilder(cluster URI here). WithAadUserPromptAuthentication();
using (var client = KustoClientFactory.CreateCslAdminProvider(kcsb))
{
    var diagnosticsCommand = CslCommandGenerator.GenerateShowDiagnosticsCommand();
    var objectReader = new ObjectReader<DiagnosticsShowCommandResult>(client.ExecuteControlCommand(diagnosticsCommand));
    DiagnosticsShowCommandResult diagResult = objectReader.ToList().FirstOrDefault();
    // DO something with the diagResult    
}
```



## <a name="the-kustoclientfactory-client-factory"></a>クストクライアントファクトリークライアントファクトリ

静的クラス`Kusto.Data.Net.Client.KustoClientFactory`は Kusto を利用するクライアント コードの作成者にメイン エントリ ポイントを提供します。 次の重要な静的メソッドが用意されています。

|Method                                      |戻り値                                |使用目的                                                      |
|--------------------------------------------|---------------------------------------|--------------------------------------------------------------|
|`CreateCslQueryProvider`                    |`ICslQueryProvider`                    |Kusto エンジン クラスタにクエリを送信しています。                    |
|`CreateCslAdminProvider`                    |`ICslAdminProvider`                    |Kusto クラスタにコントロール コマンドを送信する (あらゆる種類の)。    |
|`CreateRedirectProvider`                    |`IRedirectProvider`                    |Kusto 要求のリダイレクト HTTP 応答メッセージを作成しています。|

