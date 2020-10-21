---
title: strcat ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの strcat () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 55a52ff4aece3fa1a3197db0fb47984217292136
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251525"
---
# <a name="strcat"></a>strcat()

1 ~ 64 の引数を連結します。

* 引数が文字列型でない場合は、文字列に強制的に変換されます。

## <a name="syntax"></a>構文

`strcat(`*引数 1*、 *引数 2*[、 *argumentn*]`)`

## <a name="arguments"></a>引数

* *引数 1* ... *argumentn*: 連結する式。

## <a name="returns"></a>戻り値

1つの文字列に連結された引数。

## <a name="examples"></a>例
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|
