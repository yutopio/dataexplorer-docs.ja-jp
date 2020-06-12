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
ms.openlocfilehash: c414f1bdb83850bc6ec6065314bc7c8662ab0ed2
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717089"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

文字列を base64 文字列としてエンコードします。

**構文**

`base64_encode_tostring(`*文字列*`)`

**引数**

* *String*: base64 文字列としてエンコードされる入力文字列。

**戻り値**

Base64 文字列としてエンコードされた文字列を返します。

* Base64 文字列を UTF-8 文字列にデコードするには、「 [base64_decode_tostring ()](base64_decode_tostringfunction.md) 」を参照してください。
* Base64 文字列を long 型の値の配列にデコードするには、「 [base64_decode_toarray ()](base64_decode_toarrayfunction.md) 」を参照してください。


**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quine   |
|--------|
|S3VzdG8 =|

