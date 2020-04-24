---
title: トヘックス() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで tohex() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 402a0923d4fe760e97fa6098ad955b6c24a5d5ee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506122"
---
# <a name="tohex"></a>tohex()

入力を 16 進数の文字列に変換します。

```kusto
tohex(256) == '100'
tohex(-256) == 'ffffffffffffff00' // 64-bit 2's complement of -256
tohex(toint(-256), 8) == 'ffffff00' // 32-bit 2's complement of -256
tohex(256, 8) == '00000100'
tohex(256, 2) == '100' // Exceeds min length of 2, so min length is ignored.
```

**構文**

`tohex(`*エクスプル*`, [`` *MinLength*]`、)'

**引数**

* *Expr*: 16 進文字列に変換される整数または長整数型の値。  その他の型はサポートされていません。
* *MinLength*: 出力に含める先頭文字の数を表す数値。  1 から 16 までの値がサポートされ、16 より大きい値は 16 に切り捨てられます。  文字列が先頭文字を持たない minLength より長い場合は、minLength は事実上無視されます。  負の数値は、基礎となるデータサイズによって少なくとも表されるだけなので、int (32 ビット) の場合、minLength は最低 8 個になり、長さ (64 ビット) の場合は最低 16 になります。

**戻り値**

変換が成功すると、結果は文字列値になります。
変換が成功しなかった場合、結果は null になります。