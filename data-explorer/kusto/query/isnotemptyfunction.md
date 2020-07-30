---
title: isnotempty ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの isnotempty () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d325861c7264c77535caf6df6c363ec801dd697
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347206"
---
# <a name="isnotempty"></a>isnotempty()

引数が空の文字列ではなく、null でない場合は、を返し `true` ます。

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>構文

`isnotempty(`[*値*]`)`

`notempty(`[*値*] `)`--のエイリアス`isnotempty`
