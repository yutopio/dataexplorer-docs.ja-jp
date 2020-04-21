---
title: SQL サーバーからリンク サーバーとしての Kusto - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの SQL サーバーからリンク サーバーとして Kusto を説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 77db975f2bd7c5c1d747902248070664cd020e39
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523462"
---
# <a name="kusto-as-linked-server-from-sql-server"></a>SQL サーバーからリンク サーバーとして Kusto

オンプレミスの SQL サーバーでは、リンク サーバーを接続できます。 この機能を使用すると、SQL サーバーとリンク サーバーからのデータを結合するクエリを作成できます。

ODBC 接続を介してリンク サーバーとして Kusto を使用することができます。

1. SQL Server (オンプレミス) サービスは、AAD を使用して Kusto に接続できるアクティブなディレクトリ アカウント (既定のサービス アカウントではない) を使用する必要があります。
2. SQL Server 2017 用の最新の ODBC ドライバーをインストールします (SSMS 18 も付属)。https://www.microsoft.com/download/details.aspx?id=56567
3. 特定の Kusto クラスターおよびデータベースの ODBC ドライバーの DSN より`Driver={ODBC Driver 17 for SQL Server};Server=<cluster>.kusto.windows.net;Database=<database>;Authentication=ActiveDirectoryIntegrated;Language=any@MaxStringSize:4000`少ない接続文字列を準備します。 言語オプションは、文字列を NVARCHAR(4000) としてエンコードするように Kusto をチューニングするために追加されます。 この回避策の詳細については[、ODBC](./clients.md#odbc)を参照してください。
4. 次の 3 つの設定を定義して、リンク サーバーを![作成します。](../images/linkedserverconnection.png "リンク サーバー接続")
5. [セキュリティ] タブは、次の設定で定義する必要があります: ![alt テキスト](../images/linkedserverlogin.png "リンク サーバーログイン")

これで、次のように Kusto からデータをクエリできます。
```sql
SELECT * FROM OpenQuery(LINKEDSERVER, 'SELECT * FROM <KustoStoredFunction>[(<Parameters>)]')
```

メモ:
1. Kusto からデータを抽出するには、Kusto[のストアド関数](../../query/schema-entities/stored-functions.md)を使用することをお勧めします。 ストアド関数には[、Kusto](../../query/index.md)からの効率的なクエリに必要なすべてのロジックを含めることができます。 Kusto ストアド関数を呼び出す T-SQL 構文は、SQL 表関数を呼び出す場合と同じです。
2. SQL Server は、独自のクエリ内のリンク サーバーからリモート テーブル関数を呼び出すことはサポートしていません。 この制限の回避策は、リンク`OpenQuery`サーバーでリモート クエリを実行するために使用することです。 この方法では、テーブル関数は SQL Server の直接ではなく、リンク サーバーで実行されるクエリで呼び出されます。 外部 T-SQL クエリを使用して、 SQL サーバー上のデータと、 を介して`OpenQuery`Kusto ストアドファンクションから返されるデータを結合できます。