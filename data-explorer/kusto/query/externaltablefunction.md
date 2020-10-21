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
ms.openlocfilehash: b966dbd43d1f40842240eaebf7d4008450e1f746
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343200"
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

## <a name="next-steps"></a>次の手順

* [外部テーブル全般制御コマンド](../management/external-table-commands.md)
* [Azure Storage または Azure Data Lake の外部テーブルを作成および変更する](../management/external-tables-azurestorage-azuredatalake.md)
* [外部 SQL テーブルを作成および変更する](../management/external-sql-tables.md)