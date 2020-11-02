---
title: gzip_compress_to_base64_string-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの gzip_compress_to_base64_string () コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 11/01/2020
ms.openlocfilehash: 2d5d19a1a0d90a6bcff42247b85d01497afd0fac
ms.sourcegitcommit: 0e2fbc26738371489491a96924f25553a8050d51
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148562"
---
# <a name="gzip_compress_to_base64_string"></a>gzip_compress_to_base64_string ()

Gzip 圧縮を実行し、結果を base64 にエンコードします。


## <a name="syntax"></a>Syntax

`gzip_compress_to_base64_string("`*input_string* "`)`

## <a name="arguments"></a>引数

*input_string* : 入力 `string` 、圧縮する文字列、および base64 でエンコードされた文字列。 関数は、1つの文字列引数を受け入れます。

## <a name="returns"></a>戻り値

* `string`Gzip で圧縮され、base64 でエンコードされた元の文字列を表すを返します。 
* 圧縮またはエンコードに失敗した場合は、空の結果を返します。

## <a name="example"></a>例
```kusto
print res = gzip_compress_to_base64_string("1234567890qwertyuiop")
```

**出力:** 
> |"eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGd0xvZwAzAG5JZA = = "|

## <a name="next-steps"></a>次の手順

[Gzip_decompress_from_base64_string ()](gzip-base64-decompress.md)を使用して、圧縮されていない元の文字列を取得します。
