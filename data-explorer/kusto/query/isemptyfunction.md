---
title: isempty ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの isempty () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2944e90f14f38e2f136f5815a95584edc50d8235
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251734"
---
# <a name="isempty"></a>isempty()

`true`引数が空の文字列であるか、または null である場合は、を返します。
    
```kusto
isempty("") == true
```

## <a name="syntax"></a>構文

`isempty(`[*値*]`)`

## <a name="returns"></a>戻り値

引数が空の文字列または null であるかどうかを示します。

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|parsejson (" {} ")|false

## <a name="example"></a>例

```kusto
T
| where isempty(fieldName)
| count
```