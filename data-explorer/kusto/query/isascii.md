---
title: isascii ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの isascii () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a17836597277b2cf5401f2caeaa60b44da731dd5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251774"
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