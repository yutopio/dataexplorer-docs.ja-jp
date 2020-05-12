---
title: base64_decode_toarray ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの base64_decode_toarray () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: eda367dfeaab15dc5249fd860596964c597a1bcd
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225413"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

Base64 文字列を長整数値の配列にデコードします。

**構文**

`base64_decode_toarray(`*文字列*`)`

**引数**

* *String*: BASE64 から UTF8-8 文字列にデコードする入力文字列。

**戻り値**

Base64 文字列から ecoded long 値の配列を返します。

* Base64 文字列を UTF-8 文字列にデコードする方法については、「 [base64_decode_tostring ()](base64_decode_tostringfunction.md) 」を参照してください。
* Base64 文字列への文字列のエンコードについては、「 [base64_encode_tostring ()](base64_encode_tostringfunction.md) 」を参照してください。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|Quine|
|-----|
|[75117115116111]|

無効な UTF-8 エンコードから生成された base64 文字列をデコードしようとすると、null が返されます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
