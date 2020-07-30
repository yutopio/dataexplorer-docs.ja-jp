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
ms.openlocfilehash: 218f87a3c11695c4aa8135f98b0c445580de9ad0
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349263"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

文字列を base64 文字列としてエンコードします。

## <a name="syntax"></a>構文

`base64_encode_tostring(`*文字列*`)`

## <a name="arguments"></a>引数

* *String*: base64 文字列としてエンコードされる入力文字列。

## <a name="returns"></a>戻り値

Base64 文字列としてエンコードされた文字列を返します。

* Base64 文字列を UTF-8 文字列にデコードするには、「 [base64_decode_tostring ()](base64_decode_tostringfunction.md) 」を参照してください。
* Base64 文字列を long 型の値の配列にデコードするには、「 [base64_decode_toarray ()](base64_decode_toarrayfunction.md) 」を参照してください。


## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quine   |
|--------|
|S3VzdG8 =|

