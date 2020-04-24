---
title: log() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで log() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 7bd3c4f5c6b262587cab2327486945f5d78aaf02
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513279"
---
# <a name="log"></a>log()

`log()`自然対数関数を返します。  

**構文**

`log(`*X*`)`

**引数**

* *x*: 実数は 0 >。

**戻り値**

* 自然対数は、自然指数関数(exp)の逆数である底電子対数です。
* `null`引数が負または null の場合、または`real`値に変換できない場合。 

**参照**

* 一般的な (10 をベース) 対数については[、log10()](log10-function.md)を参照してください。
* 底 2 の対数については[、log2() を](log2-function.md)参照してください。