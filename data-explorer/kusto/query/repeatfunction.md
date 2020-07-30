---
title: repeat ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの repeat () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0d488ac96cd3db2161761ea837d5490d25cfc920
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345829"
---
# <a name="repeat"></a>repeat()

一連の等しい値を保持する動的配列を生成します。

## <a name="syntax"></a>構文

`repeat(`*値* `,`*カウント*`)` 

## <a name="arguments"></a>引数

* *value*: 結果として得られる配列内の要素の値。 *値*の型は、boolean、integer、long、real、datetime、または timespan です。   
* *count*: 結果として得られる配列内の要素の数。 *カウント*は整数でなければなりません。
*Count*が0に等しい場合は、空の配列が返されます。
*Count*が0未満の場合は、null 値が返されます。 

## <a name="examples"></a>例

次の例は、 `[1, 1, 1]`を返します。

```kusto
T | extend r = repeat(1, 3)
```