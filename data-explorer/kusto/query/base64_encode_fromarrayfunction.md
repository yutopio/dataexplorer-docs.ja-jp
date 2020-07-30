---
title: base64_encode_fromarray ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの base64_encode_fromarray () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: ede35223b9cc57fddc5318659e6902130a80c011
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349280"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

Base64 文字列をバイト配列からエンコードします。

## <a name="syntax"></a>構文

`base64_encode_fromarray(`*BytesArray*`)`

## <a name="arguments"></a>引数

* *Bytesarray*: base64 文字列にエンコードする入力バイト配列。

## <a name="returns"></a>戻り値

バイト配列からエンコードされた base64 文字列を返します。

* Base64 文字列を UTF-8 文字列にデコードする方法については、「 [base64_decode_tostring ()](base64_decode_tostringfunction.md) 」を参照してください。
* Base64 文字列への文字列のエンコードについては、「 [base64_encode_tostring ()](base64_encode_tostringfunction.md) 」を参照してください。
* この関数は、 [base64_decode_toarray ()](base64_decode_toarrayfunction.md)の逆です。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let bytes_array = toscalar(print base64_decode_toarray("S3VzdG8="));
print decoded_base64_string = base64_encode_fromarray(bytes_array)
```

|decoded_base64_string|
|---|
|S3VzdG8 =|


無効な UTF-8 エンコード文字列から生成された、無効なバイト配列から base64 文字列をエンコードしようとすると、null が返されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let empty_bytes_array = toscalar(print base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA"));
print empty_string = base64_encode_fromarray(empty_bytes_array)
```

|empty_string|
|---|
||
