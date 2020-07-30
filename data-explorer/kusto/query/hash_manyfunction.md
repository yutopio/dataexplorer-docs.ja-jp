---
title: hash_many ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの hash_many () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: 3d0f389264d078d2b55ac06214bb3b820fcf7f13
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347597"
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
