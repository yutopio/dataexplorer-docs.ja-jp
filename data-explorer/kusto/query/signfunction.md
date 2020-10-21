---
title: sign ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの sign () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: db6757e8c3ca871036cba1c21c1a20c3486b9249
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250010"
---
# <a name="sign"></a>sign()

数値式の符号

## <a name="syntax"></a>構文

`sign(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 実数。

## <a name="returns"></a>戻り値

* 指定された式の正 (+ 1)、ゼロ (0)、または負 (-1) の符号。 

## <a name="examples"></a>例

```kusto
print s1 = sign(-42), s2 = sign(0), s3 = sign(11.2)

```

|s1|s2|s3|
|---|---|---|
|-1|0|1|