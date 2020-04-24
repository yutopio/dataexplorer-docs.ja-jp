---
title: 外部テーブル - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの外部テーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: f4a059ba4533ed91353e75b499e564e6dd06d591
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509386"
---
# <a name="external-tables"></a>外部テーブル

**外部テーブル**は、Kusto データベースの外部に格納されているデータを参照する Kusto スキーマ エンティティです。

[テーブル](tables.md)と同様に、外部テーブルには、明確に定義されたスキーマ (列名とデータ型のペアの順序付けされたリスト) があります。 テーブルとは異なり、データは Kusto Cluster の外部に格納および管理されます。 最も一般的に、データはCSV、寄木細工、アブロなどの標準的な形式で保存され、Kustoによって摂取されません。

**外部表**は一度作成され ([外部表制御コマンド](../../management/externaltables.md)を参照)、external_table() 関数を[external_table()](../../query/externaltablefunction.md)使用してその名前で参照できます。 

**メモ**

* 外部テーブル名では、大文字と小文字が区別されます。
* 外部テーブル名を Kusto テーブル名と重複することはできません。
* 外部テーブル名は、[エンティティ名](./entity-names.md)の規則に従います。
* データベースあたりの外部テーブルの最大数は 1,000 です。
* Kusto は[、外部テーブルへのデータのエクスポート](../../management/data-export/export-data-to-an-external-table.md)と[外部テーブルへのクエリをサポートしています](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)。
