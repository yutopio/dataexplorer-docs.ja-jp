---
title: ファクトテーブルとディメンションテーブル-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのファクトテーブルとディメンションテーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 6db37366ddd3d70aaa89c0d6eebd1ec8affbb76d
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264445"
---
# <a name="fact-and-dimension-tables"></a>ファクトとディメンション テーブル

Azure データエクスプローラーデータベースのスキーマを設計するときは、テーブルを2つのカテゴリのうちの1つに広範に属しているものと考えてください。
* [ファクトテーブル](https://en.wikipedia.org/wiki/Fact_table)
* [ディメンションテーブル](https://en.wikipedia.org/wiki/Dimension_(data_warehouse)#Dimension_table)

## <a name="fact-tables"></a>ファクトテーブル
ファクトテーブルは、サービスログや測定情報など、変更できない "ファクト" レコードを持つテーブルです。 レコードは、ストリーミング方式または大きなまとまりでテーブルに徐々に追加されます。 これらのレコードは、コストが原因で削除されるまで、または値が失われてしまうため、そのままになります。 それ以外の場合、レコードは更新されません。

エンティティデータはファクトテーブルに保持されることがあり、エンティティデータの変更は遅くなります。 たとえば、場所を変更することの少ないオフィス機器など、一部の物理的なエンティティに関するデータです。
Kusto のデータは不変であるため、一般的な方法は、各テーブルに2つの列を保持することです。
* `string`エンティティを識別する id () 列
* 最後に変更された ( `datetime` ) timestamp 列

その後、各エンティティ id の最後のレコードのみが取得されます。

## <a name="dimension-tables"></a>ディメンションテーブル
ディメンションテーブル:
* エンティティ識別子の参照テーブルなどの参照データをそのプロパティに保持する
* 単一のトランザクションでコンテンツ全体が変更されるテーブルにスナップショットのようなデータを保持する

ディメンションテーブルは、新しいデータに対して定期的に取り込まれたされるわけではありません。 代わりに、データコンテンツ全体が一度に更新されます。これには、 [... または replace](../management/data-ingestion/ingest-from-query.md)、 [. move エクステント](../management/extents-commands.md#move-extents)、などの操作を使用し[ます。](../management/rename-table-command.md)

ディメンションテーブルがファクトテーブルから派生している場合があります。 このプロセスは、ファクトテーブルの[更新ポリシー](../management/updatepolicy.md)を使用して実行できます。また、テーブルに対するクエリでは、各エンティティの最後のレコードを取得します。

## <a name="differentiate-fact-and-dimension-tables"></a>ファクトテーブルとディメンションテーブルを区別する

Kusto には、ファクトテーブルとディメンションテーブルを区別するプロセスがあります。 そのうちの1つは[連続エクスポート](../management/data-export/continuous-data-export.md)です。

これらのメカニズムでは、ファクトテーブルのデータを正確に1回処理することが保証されます。 [データベースカーソル](../management/databasecursor.md)機構に依存しています。

たとえば、連続エクスポートジョブを実行するたびに、データベースカーソルの最後の更新以降に取り込まれたされたすべてのレコードがエクスポートされます。 連続エクスポートジョブでは、ファクトテーブルとディメンションテーブルを区別する必要があります。 ファクトテーブルは、新しく取り込まれたデータを処理するだけで、ディメンションテーブルは参照として使用されます。 そのため、テーブル全体を考慮する必要があります。

テーブルを "ファクトテーブル" または "ディメンションテーブル" としてマークする方法はありません。
テーブルへのデータの取り込まれた方法とテーブルの使用方法は、その型を識別するものです。
