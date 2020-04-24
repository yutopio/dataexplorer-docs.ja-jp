---
title: repeat() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで repeat() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0fd944112a64e7400e38c627b7b0651bb6fccd54
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510423"
---
# <a name="repeat"></a>repeat()

一連の等しい値を保持する動的配列を生成します。

**構文**

`repeat(`*値*`,`*カウント*`)` 

**引数**

* *value*: 結果の配列内の要素の値。 *値*の型には、ブール値、整数、長整数、実数、日時、または時間範囲を指定できます。   
* *count*: 結果の配列内の要素の数。 *カウント*は整数でなければなりません。
*count*がゼロの場合は、空の配列が返されます。
*count*が 0 より小さい場合は、NULL 値が返されます。 

**使用例**

次の例は、 `[1, 1, 1]`を返します。

```kusto
T | extend r = repeat(1, 3)
```