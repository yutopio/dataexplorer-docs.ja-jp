---
title: strcat ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの strcat () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: af25bb0407c9bc0c004c2f22e326ac034c6682cd
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717106"
---
# <a name="strcat"></a>strcat()

1 ~ 64 の引数を連結します。

* 引数が文字列型でない場合は、文字列に強制的に変換されます。

**構文**

`strcat(`*引数 1*、*引数 2*[、 *argumentn*]`)`

**引数**

* *引数 1* ...*argumentn*: 連結する式。

**戻り値**

1つの文字列に連結された引数。

**使用例**
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|
