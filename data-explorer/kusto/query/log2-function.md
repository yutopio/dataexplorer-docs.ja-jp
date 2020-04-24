---
title: log2() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで log2() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 41e9a1457f97fa04a4daa54e1929f27d8a448ae3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513126"
---
# <a name="log2"></a>log2()

`log2()`は、底 2 の対数関数を返します。  

**構文**

`log2(`*X*`)`

**引数**

* *x*: 実数は 0 >。

**戻り値**

* 対数は底2の対数で、底2の指数関数(exp)の逆数です。
* `null`引数が負または null の場合、または`real`値に変換できない場合。 

**参照**

* 自然 (底 e) 対数については、 [log()](log-function.md)を参照してください。
* 一般的な (10 をベース) 対数については[、log10()](log10-function.md)を参照してください。