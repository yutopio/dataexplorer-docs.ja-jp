---
title: pack_all() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでpack_all() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3c6a22b656e28b8b7113864e0b3f9636a4fb364d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511936"
---
# <a name="pack_all"></a>pack_all()

表形式`dynamic`の式のすべての列からオブジェクト (プロパティ バッグ) を作成します。

**構文**

`pack_all()`

**使用例**

テーブルを与えられた Sms メッセージ 

|ソース番号 |ターゲット番号| 文字数
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

次のクエリ:
```kusto
SmsMessages | extend Packed=pack_all()
``` 

戻り値:

|TableName |ソース番号 |ターゲット番号 | パック
|---|---|---|---
|メッセージ|555-555-1234 |555-555-1212 | {"ソース番号":"555-555-1234", "ターゲット番号":"555-555-1212", "CharsCount": 46}
|メッセージ|555-555-1234 |555-555-1213 | {"ソース番号":"555-555-1234", "ターゲット番号":"555-555-1213", "CharsCount": 50}
|メッセージ|555-555-1212 |555-555-1234 | {"ソース番号":"555-555-1212", "ターゲット番号":"555-555-1234", "CharsCount": 32}