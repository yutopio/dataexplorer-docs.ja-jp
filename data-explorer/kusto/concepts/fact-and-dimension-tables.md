---
title: ファクト テーブルとディメンション テーブル - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのファクト テーブルとディメンション テーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 1a88f8549bf5accce197294d433ece8ccb683281
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523241"
---
# <a name="fact-and-dimension-tables"></a>ファクト テーブルとディメンション テーブル

Kusto データベースのスキーマを設計する場合、テーブルは次の 2 つのカテゴリのいずれかに大きく属していると考えると便利です。
* [ファクト テーブル](https://en.wikipedia.org/wiki/Fact_table)および
* [ディメンション テーブル](https://en.wikipedia.org/wiki/Dimension_(data_warehouse)#Dimension_table)

**ファクト テーブル**は、サービス ログや測定情報など、不変の "ファクト" のレコードを持つテーブルです。 レコードは、ストリーミング方式または大きなチャンクで徐々にテーブルに追加され、コスト上の理由から、または値が失われるため、削除が必要になるまで、テーブルに保持されます。 それ以外の場合は、レコードは更新されません。

**ディメンション テーブル**には、参照データ (エンティティ識別子からプロパティへの参照テーブルなど) とスナップショットのようなデータ (1 つのトランザクションで内容全体が変更されるテーブル) が保持されます。 通常、このようなテーブルは定期的に新しいデータで取り込まれません。 代わりに、データコンテンツ全体が[、.set-or-replace](../management/data-ingestion/ingest-from-query.md) [、.move エクステント](../management/extents-commands.md#move-extents)[、.rename テーブル](../management/rename-table-command.md)などの操作を使用して一度に更新されます。
場合によっては、ディメンション テーブルは、ファクト テーブルの[更新ポリシー](../management/updatepolicy.md)を介してファクト テーブルから派生し、各エンティティの最後のレコードを取得するテーブルに対するクエリを使用することもあります。

Kusto のテーブルをファクト テーブルまたはディメンション テーブルとして "マーク" する方法がないことを認識することが重要です。 データをテーブルに取り込む方法と、テーブルの使用方法が重要です。

> [!NOTE]
> Kusto は、エンティティ データがゆっくりと変化するようにファクト テーブルに保持するために使用される場合があります。 たとえば、場所を頻繁に変更する物理的なエンティティ (たとえば、オフィス機器の一部) に関するデータ。
> Kusto のデータは不変であるため、一般的には、エンティティを識別する ID (`string`) 列と、最後に変更された (`datetime`) タイムスタンプ列の 2 つの列を各テーブルに保持します。 その後、各エンティティ ID の最後のレコードのみが取得されます。



## <a name="commands-that-differentiate-fact-and-dimension-tables"></a>ファクト テーブルとディメンション テーブルを区別するコマンド

Kusto には、ファクト テーブルとディメンション テーブルを区別するプロセスがあります。

* [継続的なエクスポート](../management/data-export/continuous-data-export.md)




これらのプロセスは、[データベース カーソル](../management/databasecursor.md)機構に依存して、ファクト テーブル内のデータを正確に 1 回だけ処理することが保証されます。

たとえば、連続エクスポート ジョブを実行するたびに、データベース カーソルの最後の更新以降に取り込まれたすべてのレコードがエクスポートされます。 したがって、継続的なエクスポート ジョブでは、ファクト テーブル (新しく取り込まれたデータのみを処理する) とディメンション テーブル (ルックアップとして使用されるため、テーブル全体を考慮する必要があります) を区別する必要があります。