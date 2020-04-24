---
title: getschema 演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでスキーマを取得する演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2faeee575f1af72cfad808253853ae96aba7a28f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514367"
---
# <a name="getschema-operator"></a>getschema 演算子 

入力の表形式スキーマを表すテーブルを作成します。

```kusto
T | summarize MyCount=count() by Country | getschema 
```

**構文**

*T* `| ``getschema`

**例**

```kusto
StormEvents
| top 10 by Timestamp
| getschema
```

|ColumnName|ColumnOrdinal|DataType|[列の型]|
|---|---|---|---|
|Timestamp|0|System.DateTime|DATETIME|
|Language|1|System.String|string|
|ページ|2|System.String|string|
|ビュー|3|System.Int64|long
|バイト納入|4|System.Int64|long