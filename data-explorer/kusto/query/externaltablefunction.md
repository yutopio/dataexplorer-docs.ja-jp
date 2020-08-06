---
title: external_table ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの external_table () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 13b244eb151d140e3626412188ac9bc9de242cc6
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802980"
---
# <a name="external_table"></a>external_table()

外部テーブルを名前で参照します。

```kusto
external_table('StormEvent')
```

> [!NOTE]
> * 関数には、 `external_table` [テーブル](tablefunction.md)関数と同様の制限があります。
> * [外部テーブル](schema-entities/externaltables.md)
> * [外部テーブルを管理するためのコマンド](../management/externaltables.md)

## <a name="syntax"></a>構文

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>引数

* *TableName*: クエリ対象の外部テーブルの名前。
  種類がまたはの外部テーブルを参照する文字列リテラルである必要があり `blob` `adl` ます。 <!-- TODO: Document data formats supported -->

* *MappingName*: 実際の (外部) データシャードのフィールドをこの関数によって出力される列にマップするマッピングオブジェクトの省略可能な名前です。
