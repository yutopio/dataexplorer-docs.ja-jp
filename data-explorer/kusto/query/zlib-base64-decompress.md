---
title: zlib_decompress_from_base64_string ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの zlib_decompress_from_base64_string () コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 09/29/2020
ms.openlocfilehash: e2163b3fe5460a660579889a589acd1e5fd965cd
ms.sourcegitcommit: 463ee13337ed6d6b4f21eaf93cf58885d04bccaa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573527"
---
# <a name="zlib_decompress_from_base64_string"></a>zlib_decompress_from_base64_string ()

Base64 から入力文字列をデコードし、zlib 圧縮解除を実行します。

> [!NOTE]
> サポートされている windows のサイズは15だけです。

## <a name="syntax"></a>構文

`zlib_decompress_from_base64_string('input_string')`

## <a name="arguments"></a>引数

*input_string*: `string` zlib で圧縮された後、base64 でエンコードされた入力。 関数は、1つの文字列引数を受け入れます。

## <a name="returns"></a>戻り値

* 元の `string` 文字列を表すを返します。 
* 圧縮解除またはデコードに失敗した場合は、空の結果が返されます。 
    * たとえば、無効な zlib 圧縮文字列と base 64 エンコードされた文字列は、空の出力を返します。

## <a name="examples"></a>例

```kusto
print zcomp = zlib_decompress_from_base64_string("eJwLSS0uUSguKcrMS1cwNDIGACxqBQ4=")
```

**出力:**

|テスト文字列 123 |

無効な入力の例:

```kusto
print zcomp = zlib_decompress_from_base64_string("x0x0x0")
```

**Output**
||

## <a name="next-steps"></a>次のステップ

[Zlib_compress_to_base64_string ()](zlib-base64-compress.md)を使用して、圧縮された入力文字列を作成します。