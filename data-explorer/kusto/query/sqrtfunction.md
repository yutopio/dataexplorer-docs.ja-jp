---
title: sqrt() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの sqrt() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 660235a60893732288a551e1febd9b7b044b4f00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507261"
---
# <a name="sqrt"></a>sqrt()

平方根関数を返します。  

**構文**

`sqrt(`*X*`)`

**引数**

* *x*: 実数>= 0。

**戻り値**

* `sqrt(x) * sqrt(x) == x`
* 引数が負であるか、`real` 値に変換できない場合は `null`。 