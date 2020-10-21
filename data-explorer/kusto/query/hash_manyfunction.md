---
title: hash_many ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの hash_many () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: a323491a2d3c4e78684c8bcaff6de8c55573d61a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243301"
---
# <a name="hash_many"></a>hash_many()

複数の値の結合されたハッシュ値を返します。

## <a name="syntax"></a>構文

`hash_many(`*s1* `,`*s2* [ `,` *s3* ...]`)`

## <a name="arguments"></a>引数

* *s1*、 *s2*、...、 *sN*: 入力値がまとめてハッシュされます。

## <a name="returns"></a>戻り値

指定されたスカラーの結合ハッシュ値。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|さ|
|---|---|---|
|こんにちは|World|-1440138333540407281|
