---
title: カウント演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのカウント演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: c0d0286919a68b6e58065e0a6fe7e0d24b1cdd5f
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610223"
---
# <a name="count-operator"></a>count 演算子

入力レコード セット内のレコード数を返します。

**構文**

`T | count`

**引数**

*T*: カウントするレコードが含まれる表形式のデータ。

**戻り値**

この関数は、1 つのレコードと `long`型の列を含むテーブルを返します。 唯一のセルの値は、 *T*内のレコードの数です。 

**例**

```kusto
StormEvents | count
```
