---
title: gzip_decompress_from_base64_string ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの gzip_decompress_from_base64_string () コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 11/01/2020
ms.openlocfilehash: 19fc8c7ce74cfe632034722fda2b23c5105d013a
ms.sourcegitcommit: 0e2fbc26738371489491a96924f25553a8050d51
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148549"
---
# <a name="gzip_decompress_from_base64_string"></a>gzip_decompress_from_base64_string ()

Base64 から入力文字列をデコードし、gzip 圧縮解除を実行します。

## <a name="syntax"></a>Syntax

`gzip_decompress_from_base64_string("`*input_string*`")`

## <a name="arguments"></a>引数

*input_string* : `string` gzip で圧縮され、base64 でエンコードされた入力。 関数は、1つの文字列引数を受け入れます。

## <a name="returns"></a>戻り値

* 元の `string` 文字列を表すを返します。 
* 圧縮解除またはデコードに失敗した場合は、空の結果が返されます。 
    * たとえば、無効な gzip 圧縮文字列と base 64 エンコード文字列は、空の出力を返します。

## <a name="examples"></a>例

```kusto
print res=gzip_decompress_from_base64_string("eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGd0xvZwAzAG5JZA==")
```

**出力:**

|"1234567890qwertyuiop "|

無効な入力の例:

```kusto
print res=gzip_decompress_from_base64_string("x0x0x0")
```

**出力:**
>||

## <a name="next-steps"></a>次のステップ

[Gzip_compress_to_base64_string ()](gzip-base64-compress.md)を使用して、圧縮された入力文字列を作成します。
