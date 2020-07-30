---
title: strcat_array ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの strcat_array () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b8369d2e994477c9d01880632fac5f8a3ebaf6a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342599"
---
# <a name="strcat_array"></a>strcat_array()

指定した区切り記号を使用して、連結された配列値の文字列を作成します。
    
## <a name="syntax"></a>構文

`strcat_array(`*array*、 *delimiter*`)`

## <a name="arguments"></a>引数

* *array*: `dynamic` 連結する値の配列を表す値。
* *区切り記号*: `string` *配列*内の値を連結するために使用される値。

## <a name="returns"></a>戻り値

1つの文字列に連結された配列値。

## <a name="examples"></a>例
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|str|
|---|
|1->2->3|