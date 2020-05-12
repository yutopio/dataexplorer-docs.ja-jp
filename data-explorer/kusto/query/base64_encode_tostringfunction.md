---
title: base64_encode_tostring ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの base64_encode_tostring () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 332ff6bedd268dd79be020ff1dc4d0591ed486f7
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225311"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

文字列を base64 文字列としてエンコードします。

**構文**

`base64_encode_tostring(`*文字列*`)`

**引数**

* *String*: base64 文字列としてエンコードされる入力文字列。

**戻り値**

Base64 文字列としてエンコードされた文字列を返します。

* Base64 文字列を UTF-8 文字列にデコードする方法については、「 [base64_decode_tostring ()](base64_decode_tostringfunction.md) 」を参照してください。
* Base64 文字列を long 型の値の配列にデコードする方法については、「 [base64_decode_toarray ()](base64_decode_toarrayfunction.md) 」を参照してください。


**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quine   |
|--------|
|S3VzdG8 =|
