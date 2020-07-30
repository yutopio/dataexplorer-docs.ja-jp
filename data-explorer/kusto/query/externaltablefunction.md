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
ms.openlocfilehash: 4a3a1150996000742f5065df0eddc385074eaa48
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348090"
---
# <a name="external_table"></a>external_table()

外部テーブルを名前で参照します。

```kusto
external_table('StormEvent')
```

## <a name="syntax"></a>構文

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>引数

* *TableName*: クエリ対象の外部テーブルの名前。
  種類がまたはの外部テーブルを参照する文字列リテラルである必要があり `blob` `adl` ます。 <!-- TODO: Document data formats supported -->

* *MappingName*: 実際の (外部) データシャードのフィールドをこの関数によって出力される列にマップするマッピングオブジェクトの省略可能な名前です。

**ノート**

外部テーブルの詳細については、「[外部テーブル](schema-entities/externaltables.md)」を参照してください。

「[外部テーブルを管理するためのコマンド](../management/externaltables.md)」も参照してください。

関数には、 `external_table` [テーブル](tablefunction.md)関数と同様の制限があります。