---
title: 接続文字列 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の接続文字列について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 02a459af689cf9f18fe129ee73bff7034a92c80a
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342486"
---
# <a name="connection-strings"></a>接続文字列

接続文字列は、Kusto 管理コマンド、Kusto API、およびクエリで広く使用されています。
接続文字列は、Kusto の外部にあるリソースを検索して操作する方法を記述する一般的な手段を提供します。たとえば、Azure Blob Storage サービスの BLOB や Azure SQL Database データベースなどです。

接続文字列にはさまざまな形式があります。

* [Kusto 接続文字列](./kusto.md)には、Kusto サービス エンドポイントと通信する方法が記述されています。
  Kusto 接続文字列は、[ADO.NET 接続文字列モデル](/dotnet/framework/data/adonet/connection-string-syntax)の後にモデル化されます。
* [ストレージ接続文字列](./storage.md)には、Azure Blob Storage や Azure Data Lake Storage などの外部ストレージ サービスで Kusto を指す方法が記述されます。
* SQL 接続文字列 - Kusto [sql_request プラグイン](../../query/sqlrequestplugin.md)で Azure DB サービスに要求を発行する場合と、[SQL コマンドにエクスポートする](../../management/data-export/export-data-to-sql.md)場合に使用されます。  
  これらの接続文字列は、[SqlClient 接続文字列](/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)の仕様に準拠しています。

> [!NOTE]
> 一部の接続文字列では、セキュリティ プリンシパルを参照できます。 接続文字列でセキュリティ プリンシパルを指定する方法については、[プリンシパルと ID プロバイダー](../../management/access-control/principals-and-identity-providers.md)に関する記事を参照してください。