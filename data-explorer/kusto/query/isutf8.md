---
title: isutf8 ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの isutf8 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c9ad35964eb6d5a8c4a38b5ecdeb1eae43221d52
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247347"
---
# <a name="isutf8"></a>isutf8()

`true`引数が有効な utf8 文字列の場合はを返します。
    
```kusto
isutf8("some string") == true
```

## <a name="syntax"></a>構文

`isutf8(`[*値*]`)`

## <a name="returns"></a>戻り値

引数が有効な utf8 文字列であるかどうかを示します。

## <a name="example"></a>例

```kusto
T
| where isutf8(fieldName)
| count
```