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
ms.openlocfilehash: 5a21031b07df3a4fa654fd13eb3761618308337b
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717344"
---
# <a name="isnotempty"></a>isnotempty()

引数が空の文字列ではなく、null でない場合は、を返し `true` ます。

```kusto
isnotempty("") == false
```

**構文**

`isnotempty(`[*値*]`)`

`notempty(`[*値*] `)`--のエイリアス`isnotempty`
