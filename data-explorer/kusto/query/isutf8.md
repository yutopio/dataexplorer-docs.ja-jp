---
title: isutf8 ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの isutf8 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 952ea030d351a9e23fe26bbd7f27a96d182a89e3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347155"
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