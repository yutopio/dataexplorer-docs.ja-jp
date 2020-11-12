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
ms.openlocfilehash: fffa39ca5fa41c065971b4feebfe60752b84ed59
ms.sourcegitcommit: 3eabd78305d32cd9b8a6bd1d76877ddc19d8ac63
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548871"
---
# <a name="gzip_compress_to_base64_string"></a>gzip_compress_to_base64_string()

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

|H4sIAAAAAAAA/Weuの MTIzNDU2Nzg5MHF3ZXJ0eXVpb3A6m7f2FAAAAA = = |

## <a name="next-steps"></a>次の手順

* [Gzip_decompress_from_base64_string ()](gzip-base64-decompress.md)を使用して、圧縮されていない元の文字列を取得します。
* 「 [Zlib_compress_to_base64_string ()](zlib-base64-compress.md) 」も参照してください。
