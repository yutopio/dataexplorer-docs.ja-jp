---
title: base64_decode_tostring ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの base64_decode_tostring () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: ffef2fd609075b0d5e5af5c4064e079c27cd8c94
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818505"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

Base64 文字列を UTF-8 文字列にデコードします。

**構文**

`base64_decode_tostring(`*文字列*`)`

**引数**

* *String*: BASE64 から UTF8-8 文字列にデコードする入力文字列。

**戻り値**

Base64 文字列からデコードされた UTF-8 文字列を返します。

* Base64 文字列を long 型の値の配列にデコードするには、「 [base64_decode_toarray ()](base64_decode_toarrayfunction.md) 」を参照してください。
* 文字列を base64 文字列にデコードするには、「 [base64_encode_tostring ()](base64_encode_tostringfunction.md) 」を参照してください。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|Quine|
|-----|
|Kusto|

無効な UTF-8 エンコードから生成された base64 文字列をデコードしようとすると、null が返されます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
