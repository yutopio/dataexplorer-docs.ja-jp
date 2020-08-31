---
title: 列の管理-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの列の管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 92f432f1367fb28f3343c4f63b6ccf62679ee336
ms.sourcegitcommit: 487485c87706183a9824f695e5b56b0bc5ade048
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89056253"
---
# <a name="columns-management"></a>列の管理

ここでは、テーブル列の管理に使用する次の制御コマンドについて説明します。

|コマンド |説明 |
|------- | -------|
|[列の変更](alter-column.md) |既存のテーブル列のデータ型を変更します。 |
|[alter-merge table 列](alter-merge-table-column.md) および [alter table 列-docstrings](alter-merge-table-column.md#alter-table-column-docstrings) | `docstring`指定されたテーブルの1つ以上の列のプロパティを設定します。
|[`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md) | テーブルのスキーマの変更 (列の追加と削除) |
|[列を削除し、テーブルの列を削除する](drop-column.md) |1つまたは複数の列をテーブルから削除します。 |
|[列名または列名の変更](rename-column.md) |既存または複数のテーブル列の名前を変更します。 |
