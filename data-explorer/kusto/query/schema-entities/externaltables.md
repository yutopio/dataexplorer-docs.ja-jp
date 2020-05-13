---
title: 外部テーブル-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの外部テーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: cfcf8a18bac1f6369b75538f2172fe8f25cb9660
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372941"
---
# <a name="external-tables"></a>外部テーブル

**外部テーブル**は、kusto データベースの外部に格納されているデータを参照する kusto スキーマエンティティです。

[テーブル](tables.md)と同様に、外部テーブルには、適切に定義されたスキーマ (列名とデータ型のペアの順序付きリスト) があります。 テーブルとは異なり、データは Kusto クラスターの外部で格納および管理されます。 通常、データは CSV、Parquet、Avro などの標準形式で格納され、Kusto では取り込まれたません。

**外部テーブル**は、1回作成されます ([外部テーブル制御コマンド](../../management/externaltables.md)を参照してください)。また、 [external_table ()](../../query/externaltablefunction.md)関数を使用して、その名前で参照することもできます。 

**メモ**

* 外部テーブル名では大文字と小文字が区別されます。
* 外部テーブル名を Kusto テーブル名と重複させることはできません。
* 外部テーブル名は、[エンティティ名](./entity-names.md)の規則に従います。
* データベースあたりの外部テーブルの上限は1000です。
* Kusto は[、外部テーブルへのデータのエクスポート](../../management/data-export/export-data-to-an-external-table.md)と、[外部テーブルのクエリ](../../../data-lake-query-data.md)をサポートしています。
