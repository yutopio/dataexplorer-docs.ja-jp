---
title: base64_encode_fromarray() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの base64_encode_fromarray() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: f601463fd6751be7064892e70e5b2235f96a20ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518073"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

バイト配列から base64 文字列をエンコードします。

**構文**

`base64_encode_fromarray(`*バイト配列*`)`

**引数**

* *BytesArray*: base64 文字列にエンコードされる入力バイト配列。

**戻り値**

バイト配列からエンコードされた base64 文字列を返します。

* base64 文字列を UTF-8 文字列にデコードする場合は[、base64_decode_tostring() を](base64_decode_tostringfunction.md)参照してください。
* base64 文字列へのエンコード文字列については[、base64_encode_tostring() を](base64_encode_tostringfunction.md)参照してください。
* この関数はbase64_decode_toarrayの逆関数[です。](base64_decode_toarrayfunction.md)

**例**

```kusto
let bytes_array = toscalar(print base64_decode_toarray("S3VzdG8="));
print decoded_base64_string = base64_encode_fromarray(bytes_array)
```

|decoded_base64_string|
|---|
|S3VzdG8=|


無効な UTF-8 エンコード文字列から生成された無効なバイト配列から base64 文字列をエンコードしようとすると、null が返されます。

```kusto
let empty_bytes_array = toscalar(print base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA"));
print empty_string = base64_encode_fromarray(empty_bytes_array)
```

|empty_string|
|---|
||