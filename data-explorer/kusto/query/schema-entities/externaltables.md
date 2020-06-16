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
ms.openlocfilehash: a76ef031a3fbcaa26f90fd2c2664920805b823b5
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780271"
---
# <a name="external-tables"></a>外部テーブル

**外部テーブル**は、Azure データエクスプローラーデータベースの外部に格納されているデータを参照する Kusto スキーマエンティティです。

[テーブル](tables.md)と同様に、外部テーブルには、適切に定義されたスキーマ (列名とデータ型のペアの順序付きリスト) があります。 テーブルとは異なり、データはクラスターの外部で格納および管理されます。 ほとんどの場合、データは CSV、Parquet、Avro などの標準形式で格納され、Azure データエクスプローラーによって取り込まれたされることはありません。

**外部テーブル**は1回作成されます。 外部テーブルを作成するには、次のコマンドを参照してください。
* [外部テーブル全般制御コマンド](../../management/externaltables.md)
* [外部 SQL テーブルを作成および変更する](../../management/external-sql-tables.md)
* [Azure Storage または Azure Data Lake でのテーブルの作成と変更](../../management/external-tables-azurestorage-azuredatalake.md)

**外部テーブル**を名前で参照するには、 [external_table ()](../../query/externaltablefunction.md)関数を使用します。 

**ノート**

* 外部テーブル名:
   * 大文字と小文字を区別します。
   * Kusto テーブル名と重複することはできません。
   * [エンティティ名](./entity-names.md)の規則に従います。
* データベースあたりの外部テーブルの上限は1000です。
* Kusto では、外部テーブルへの[エクスポート](../../management/data-export/export-data-to-an-external-table.md)と[連続エクスポート](../../management/data-export/continuous-data-export.md)、および[外部テーブル](../../../data-lake-query-data.md)に対するクエリがサポートされています。
* 外部テーブルに[データ消去](../../concepts/data-purge.md)が適用されていません。 外部テーブルからレコードが削除されることはありません。
