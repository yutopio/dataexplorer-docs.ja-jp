---
title: sign ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの sign () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8d6761cc2ffa9a8c28151c00720bbd1340a56875
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351082"
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