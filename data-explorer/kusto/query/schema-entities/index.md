---
title: エンティティ - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer のエンティティについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: a69362b590acee99fbe9b57d9303099f0033d458
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128497"
---
# <a name="entity-types"></a>エンティティの種類

Kusto クエリは、Kusto クラスターに接続されている Kusto データベースのコンテキストで実行されます。 データベース内のデータは、クエリが参照できるテーブルに配置され、テーブル内では列と行からなる四角形のグリッドとして編成されます。 さらに、クエリはデータベース内のストアド関数 (再利用が可能なクエリ フラグメント) を参照する場合があります。

* クラスターは、データベースを保持するエンティティです。
  クラスターには名前がありませんが、クラスターの URI に `cluster()` 特殊関数を使用して参照できます。
  たとえば、`cluster("https://help.kusto.windows.net")` は、`Samples` データベースを保持するクラスターへの参照です。

* [データベース](./databases.md)は、テーブルおよびストアド関数を保持する名前付きエンティティです。 すべての Kusto クエリはデータベースのコンテキストで実行され、そのデータベースのエンティティは、修飾なしのクエリで参照できます。 さらに、クラスターのその他のデータベース、またはデータベースやその他のクラスターは、[database() 特殊関数](../databasefunction.md)を使用して参照できます。 たとえば、`cluster("https://help.kusto.windows.net").database("Samples")` のように指定します。
  これは、特定のデータベースのユニバーサル参照です。

* [テーブル](./tables.md)は、データを保持する名前付きエンティティです。 テーブルには、順序付けされた一連の列と、0 行以上のデータがあります。各行は、テーブルの各列に対して 1 つのデータ値を保持します。 テーブルは、クエリのコンテキストでデータベース内にある場合にのみ、名前で参照されます。それ以外の場合は、データベース参照で修飾することによって参照されます。 たとえば、`cluster("https://help.kusto.windows.net").database("Samples").StormEvents` は、`Samples` データベース内の特定のテーブルへのユニバーサル参照です。
  テーブルは、[table() 特殊関数](../tablefunction.md)を使用して参照することもできます。

* [列](./columns.md)は、[スカラー データ型](../scalar-data-types/index.md)を持つ名前付きエンティティです。
  列は、それらを参照する特定の演算子のコンテキストにある表形式のデータ ストリームに対して相対的にクエリで参照されます。

* [ストアド関数](./stored-functions.md)は、Kusto クエリまたはクエリ部分の再利用を可能にする、名前付きエンティティです。

* [外部テーブル](./externaltables.md)は、Kusto データベースの外部に格納されるデータを参照するエンティティです。
  外部テーブルは、Kusto から外部ストレージにデータをエクスポートするためや、Kusto に取り込まずに外部データにクエリを実行するために使用されます。