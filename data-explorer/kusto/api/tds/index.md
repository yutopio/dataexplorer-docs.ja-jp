---
title: MS-TDS (T-SQL サポート) - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer での MS-TDS (T-SQL サポート) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: 8aaea26b6c4e8a7f76c4129faeb681791f25ae7e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490550"
---
# <a name="ms-tds-t-sql-support"></a>MS-TDS (T-SQL サポート)

Kusto では、SQL Server に対するクエリの実行方法を認識している既存のツールで Kusto を操作できるように、T-SQL クエリ言語のサブセットを使用して Microsoft SQL Server 通信プロトコル (MS-TDS) のサブセットをサポートしています。 サポートされているクライアントの中には、Microsoft Excel、Microsoft Power BI などがあります。

クライアント ツールが MS-TDS を使用して Kusto に対してクエリを実行するためには、クライアントで Azure Active Directory 統合認証が使用されている必要があることに注意してください。

Kusto で実装されている T-SQL クエリ言語の詳細については、「[T-SQL](./t-sql.md)」を参照してください。 

MS-TDS/T-SQL を使用して一部の既知のクライアントから Kusto を使用する方法の例については、「[MS-TDS クライアントと Kusto](./clients.md)」を参照してください。

Kusto クラスターを、オンプレミスの SQL Server へのリンク サーバーとして構成するには、[SQL Server へのリンク サーバーとしての Kusto](./linkedserver.md) に関する記事を参照してください。

Kusto に接続するための TDS 経由での AAD の使用の詳細については、「[Azure Active Directory での MS-TDS](./aad.md)」を参照してください。

TDS エンドポイント経由でのネイティブ KQL クエリの実行については、「[TDS 経由の KQL](./tdskql.md)」を参照してください。 

最後に、SQL Server での T-SQL と Kusto の実装の主な違いについては、[こちら](./sqlknownissues.md)を参照してください。