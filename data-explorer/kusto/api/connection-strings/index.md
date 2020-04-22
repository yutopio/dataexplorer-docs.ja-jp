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
ms.openlocfilehash: 53c3caecc373a646162016fc1717ce1b0b0b85d1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490074"
---
# <a name="connection-strings"></a>接続文字列

接続文字列は、Kusto 管理コマンド、Kusto API、およびクエリで広く使用されています。
接続文字列は、Kusto の外部にあるリソースを検索して操作する方法を記述する一般的な手段を提供します。たとえば、Azure Blob Storage サービスの BLOB や Azure SQL Database データベースなどです。

接続文字列にはさまざまな形式があります。

* [Kusto 接続文字列](./kusto.md)には、Kusto サービス エンドポイントと通信する方法が記述されています。
  Kusto 接続文字列は、[ADO.NET 接続文字列モデル](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax)の後にモデル化されます。
* [ストレージ接続文字列](./storage.md)には、Azure Blob Storage や Azure Data Lake Storage などの外部ストレージ サービスで Kusto を指す方法が記述されます。
* SQL 接続文字列 - Kusto [sql_request プラグイン](../../query/sqlrequestplugin.md)で Azure DB サービスに要求を発行する場合と、[SQL コマンドにエクスポートする](../../management/data-export/export-data-to-sql.md)場合に使用されます。  
  これらの接続文字列は、[SqlClient 接続文字列](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)の仕様に準拠しています。