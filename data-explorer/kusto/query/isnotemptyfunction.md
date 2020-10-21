---
title: isnotempty ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの isnotempty () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 42699b1d4a6f225213b2a2d55520cb019a983121
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253221"
---
# <a name="isnotempty"></a>isnotempty()

引数が空の文字列ではなく、null でない場合は、を返し `true` ます。

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>構文

`isnotempty(`[*値*]`)`

`notempty(`[*値*] `)` --のエイリアス `isnotempty`

## <a name="example"></a>例

```kusto
T
| where isnotempty(fieldName)
| count
```
