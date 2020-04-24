---
title: log10() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで log10() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 9ccc83ff466d0414f793b7cfbbcf10d2ca169348
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513194"
---
# <a name="log10"></a>log10()

`log10()`は、一般的な (10 をベース) 対数関数を返します。  

**構文**

`log10(`*X*`)`

**引数**

* *x*: 実数は 0 >。

**戻り値**

* 一般的な対数は、底 10 の対数です。
* `null`引数が負または null の場合、または`real`値に変換できない場合。 

**参照**

* 自然 (底 e) 対数については、 [log()](log-function.md)を参照してください。
* 底 2 の対数については[、log2() を](log2-function.md)参照してください。