---
title: 列管理 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの列管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: db0f51d3f34a16771231bf6d0e917f029ba9c04d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521949"
---
# <a name="columns-management"></a>列管理

このセクションでは、テーブル列の管理に使用される次の制御コマンドについて説明します。

|command |説明 |
|------- | -------|
|[列の変更](alter-column.md) |既存のテーブル列のデータ型を変更します。 |
|[テーブル列の変更](alter-merge-table-column.md)と[変更の列-doc文字列](alter-merge-table-column.md#alter-table-column-docstrings) | 指定した`docstring`テーブルの 1 つ以上の列のプロパティを設定します。
|[列を削除し、テーブル列を削除する](drop-column.md) |テーブルから 1 つまたは複数の列を削除します。 |
|[列または列の名前を変更する](rename-column.md) |既存または複数のテーブル列の名前を変更します。 |