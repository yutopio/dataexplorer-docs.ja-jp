---
title: カウント演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの count 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 9a34734ebfee94646b2b2f15730f14f9d2709c6d
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227512"
---
# <a name="count-operator"></a>count 演算子

入力レコードセット内のレコードの数を返します。

**構文**

`T | count`

**引数**

*T*: カウントするレコードが含まれる表形式のデータ。

**戻り値**

この関数は、1 つのレコードと `long`型の列を含むテーブルを返します。 唯一のセルの値は、 *T*内のレコードの数です。 

**例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
