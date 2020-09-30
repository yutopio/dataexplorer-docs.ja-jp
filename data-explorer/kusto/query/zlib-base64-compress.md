---
title: zlib_compress_to_base64_string-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの zlib_compress_to_base64_string () コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 09/29/2020
ms.openlocfilehash: c43ffcbb449c6fc8a4c01b7a032df50c40829702
ms.sourcegitcommit: 463ee13337ed6d6b4f21eaf93cf58885d04bccaa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573540"
---
# <a name="zlib_compress_to_base64_string"></a>zlib_compress_to_base64_string ()

Zlib 圧縮を実行し、結果を base64 にエンコードします。

> [!NOTE]
> サポートされている windows のサイズは15だけです。

## <a name="syntax"></a>構文

`zlib_compress_to_base64_string('input_string')`

## <a name="arguments"></a>引数

*input_string*: 入力 `string` 、圧縮する文字列、および base64 でエンコードされた文字列。 関数は、1つの文字列引数を受け入れます。

## <a name="returns"></a>戻り値

* `string`Zlib 圧縮および base64 でエンコードされた元の文字列を表すを返します。 
* 圧縮またはエンコードに失敗した場合は、空の結果を返します。

## <a name="example"></a>例

### <a name="using-kusto"></a>Kusto の使用

```kusto
print zcomp = zlib_compress_to_base64_string("1234567890qwertyuiop")
```

**出力:** | "eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGdw = = "|

### <a name="using-python"></a>Python の使用

圧縮は、Python などの他のツールを使用して行うことができます。 

```python
print(base64.b64encode(zlib.compress(b'<original_string>')))
```

## <a name="next-steps"></a>次のステップ

[Zlib_decompress_from_base64_string ()](zlib-base64-decompress.md)を使用して、圧縮されていない元の文字列を取得します。