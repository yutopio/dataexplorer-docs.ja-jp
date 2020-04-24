---
title: base64_decode_tostring() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでbase64_decode_tostring() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: a821fb07d62d5bca3982cc4c48b8e0457641f3f2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518175"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

base64 文字列を UTF-8 文字列にデコードします。

**構文**

`base64_decode_tostring(`*文字列*`)`

**引数**

* *文字列*: base64 から UTF8-8 文字列にデコードされる入力文字列。

**戻り値**

base64 文字列からデコードされた UTF-8 文字列を返します。

* base64 文字列を長い値の配列にデコードする場合は[、base64_decode_toarray() を](base64_decode_toarrayfunction.md)参照してください。
* base64 文字列へのエンコード文字列については[、base64_encode_tostring() を](base64_encode_tostringfunction.md)参照してください。

**例**

```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|クワイン|
|-----|
|Kusto|

無効な UTF-8 エンコーディングから生成された base64 文字列をデコードしようとすると、null が返されます。

```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||