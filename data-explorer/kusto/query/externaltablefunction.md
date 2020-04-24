---
title: external_table() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで external_table() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 9fd03fb3c8452702c3db27a5e0466e8608c04eb9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515455"
---
# <a name="external_table"></a>external_table()

外部テーブルを名前で参照します。

```kusto
external_table('StormEvent')
```

**構文**

`external_table``(`*テーブル名*`,` [*マッピング名*]`)`

**引数**

* *テーブル名*: クエリを実行する外部テーブルの名前。
  の種類`blob`または`adl`の外部テーブルを参照する文字列リテラルである必要があります。 <!-- TODO: Document data formats supported -->

* *MappingName*: 実際の (外部) データシャードのフィールドを、この関数によって出力される列にマップするマッピング オブジェクトの名前です。

**メモ**

[外部テーブル](schema-entities/externaltables.md)の詳細については、外部テーブルを参照してください。

[外部テーブルを管理するためのコマンド](../management/externaltables.md)も参照してください。

この`external_table`関数には、[表](tablefunction.md)関数と同様の制約があります。