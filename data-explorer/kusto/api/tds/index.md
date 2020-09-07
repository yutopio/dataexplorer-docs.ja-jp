---
title: MS-TDS T-SQL サポート - Azure Data Explorer
description: この記事では、Azure Data Explorer での MS-TDS T-SQL サポートについて紹介します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: cc10fcc725e038d6428d4b794a1f6d368a86a39e
ms.sourcegitcommit: 9e0289945270db517e173aa10024e0027b173b52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428415"
---
# <a name="ms-tds-t-sql-support"></a>MS-TDS T-SQL サポート

Azure Data Explorer では、T-SQL クエリ言語のサブセットを使用して Microsoft SQL Server 通信プロトコル (MS-TDS) のサブセットをサポートしています。 Microsoft Excel と Microsoft Power BI は、Azure Data Explorer で使用できる多くのツールのあくまで一部です。 これらの Microsoft アプリケーションでは、SQL Server に対してクエリを実行することもできます。

> [!NOTE]
> Azure Active Directory (Azure AD) 統合認証をクライアント ツールとして使用して、MS-TDS を使用して Kusto に対してクエリを実行します。

## <a name="next-steps"></a>次のステップ

* [T-SQL](./t-sql.md) - Kusto で実装されている T-SQL クエリ言語について説明します。 

* [TDS 経由の KQL](./tdskql.md) - TDS エンドポイント経由でのネイティブ KQL クエリの実行について説明します。

* [MS-TDS クライアントと Kusto](./clients.md) - MS-TDS/T-SQL を使用する既知のクライアントから Azure Data Explorer を使用します。

* [SQL Server へのリンク サーバーとしての Azure Data Explorer](./linkedserver.md) - クラスターを、オンプレミスの SQL Server へのリンク サーバーとして構成します。 

* [MS-TDS と Azure Active Directory](./aad.md) - Azure Data Explorer に接続するために TDS 経由で Azure AD を使用します。

* [SQL の既知の問題](./sqlknownissues.md) - SQL Server での T-SQL の実装と Azure Data Explorer の主な違いについて説明します。
