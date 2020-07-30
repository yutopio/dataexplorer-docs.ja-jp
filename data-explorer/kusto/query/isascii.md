---
title: isascii ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの isascii () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d8a060e4a332988fd966e0dec9ed07b3c76d0e3f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347291"
---
# <a name="isascii"></a>isascii()

`true`引数が有効な ascii 文字列である場合は、を返します。
    
```kusto
isascii("some string") == true
```

## <a name="syntax"></a>構文

`isascii(`[*値*]`)`

## <a name="returns"></a>戻り値

引数が有効な ascii 文字列であるかどうかを示します。

## <a name="example"></a>例

```kusto
T
| where isascii(fieldName)
| count
```