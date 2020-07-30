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
ms.openlocfilehash: 1f26b4bf267a4387748fe4c4c26636579607de51
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350997"
---
# <a name="strcat"></a>strcat()

1 ~ 64 の引数を連結します。

* 引数が文字列型でない場合は、文字列に強制的に変換されます。

## <a name="syntax"></a>構文

`strcat(`*引数 1*、*引数 2*[、 *argumentn*]`)`

## <a name="arguments"></a>引数

* *引数 1* ...*argumentn*: 連結する式。

## <a name="returns"></a>戻り値

1つの文字列に連結された引数。

## <a name="examples"></a>例
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|
