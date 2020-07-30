---
title: isempty ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの isempty () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ac2bf5d5ea55172cbdb07bf90704ae5ad497e925
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347274"
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