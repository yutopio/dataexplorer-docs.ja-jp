---
title: SQL server からリンクサーバーへの kusto as-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの SQL server からのリンクサーバーとしての Kusto について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: a6f053ac6a838d2dacd047c284b9608bdea9e5dc
ms.sourcegitcommit: bd30e24d026d8d98f9f7d8b79f18a03e295846b8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87758110"
---
# <a name="kusto-as-a-linked-server-from-the-sql-server"></a>SQL server のリンクサーバーとしての kusto

オンプレミスの SQL server を使用すると、リンクサーバーをアタッチして、SQL server およびリンクサーバーからデータを結合するクエリを作成できます。

Kusto は、ODBC 接続を介してリンクサーバーとして使用できます。
オンプレミスの SQL Server サービスでは、(既定のサービスアカウントではなく) active directory アカウントを使用して、Azure Active Directory (Azure AD) を使用して Azure データエクスプローラーに接続できるようにする必要があります。

1. SQL Server 2017 用の最新の ODBC ドライバーをインストールします (SSMS 18 も付属しています)。https://aka.ms/downloadmsodbcsql
1. ODBC ドライバー用の DSN レス接続文字列を、特定の Azure データエクスプローラークラスターおよびデータベースに対して準備します `Driver={ODBC Driver 17 for SQL Server};Server=<cluster>.kusto.windows.net;Database=<database>;Authentication=ActiveDirectoryIntegrated;Language=any@MaxStringSize:4000` 。 Language オプションを追加して、文字列を NVARCHAR (4000) としてエンコードするように Azure データエクスプローラーを調整します。 この回避策の詳細については、「 [ODBC](./clients.md#odbc)」を参照してください。
1. 赤い矢印で示されている設定を使用して、リンクサーバーを作成します。

:::image type="content" source="../images/linkedserverconnection.png" alt-text="リンクサーバー接続":::

1. 赤い矢印で示される設定でセキュリティを定義します。 

:::image type="content" source="../images/linkedserverlogin.png" alt-text="リンクサーバーログイン":::

Kusto にデータを照会するには:

```sql
SELECT * FROM OpenQuery(LINKEDSERVER, 'SELECT * FROM <KustoStoredFunction>[(<Parameters>)]')
```

> [!NOTE]
> 1. Azure データエクスプローラーからデータを抽出するために、Kusto に[格納され](../../query/schema-entities/stored-functions.md)ている関数を使用します。 格納されている関数には、Kusto からの効率的なクエリに必要なすべてのロジックを含めることができ、ネイティブ[Kql](../../query/index.md)言語で作成され、指定されたパラメーター値によって制御されます。 Kusto に格納されている関数を呼び出すための t-sql 構文は、SQL 表形式関数の呼び出しと同じです。
> 1. SQL server では、独自のクエリ内のリンクサーバーからのリモート表形式関数の呼び出しはサポートされていません。 この制限の回避策は、 `OpenQuery` リンクサーバーでリモートクエリを実行するためにを使用することです。 これにより、表形式関数は SQL server ディレクトリではなく、リンクサーバーで実行されるクエリで呼び出されます。 外部 T-sql クエリを使用して、SQL server 上のデータと Kusto によって返されたデータを経由で結合でき `OpenQuery` ます。
