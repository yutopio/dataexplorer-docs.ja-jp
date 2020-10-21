---
title: external_table ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの external_table () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 5ec069979d41a7c750c140ad84ef0db4ba5638a4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245064"
---
# <a name="external_table"></a>external_table()

[外部テーブル](schema-entities/externaltables.md)を名前で参照します。

```kusto
external_table('StormEvent')
```

> [!NOTE]
> * 関数には、 `external_table` [テーブル](tablefunction.md) 関数と同様の制限があります。
> * 標準 [クエリの制限](../concepts/querylimits.md) は、外部テーブルのクエリにも適用されます。

## <a name="syntax"></a>構文

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>引数

* *TableName*: クエリ対象の外部テーブルの名前。
  種類がまたはの外部テーブルを参照する文字列リテラルである必要があり `blob` `adl` `sql` ます。

* *MappingName*: 実際の (外部) データシャードのフィールドをこの関数によって出力される列にマップするマッピングオブジェクトの省略可能な名前です。

## <a name="next-steps"></a>次のステップ

* [外部テーブル全般制御コマンド](../management/externaltables.md)
* [Azure Storage または Azure Data Lake の外部テーブルを作成および変更する](../management/external-tables-azurestorage-azuredatalake.md)
* [外部 SQL テーブルを作成および変更する](../management/external-sql-tables.md)
