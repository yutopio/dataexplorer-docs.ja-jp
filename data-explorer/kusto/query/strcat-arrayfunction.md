---
title: strcat_array() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの strcat_array() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d2412762cf68243e3952a8ad12a5b919d947bd3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506955"
---
# <a name="strcat_array"></a>strcat_array()

指定した区切り文字を使用して、配列値の連結文字列を作成します。
    
**構文**

`strcat_array(`*配列*,*区切り記号*`)`

**引数**

* *配列*:`dynamic`連結する値の配列を表す値。
* *区切り :*`string`*配列*内の値を連結するために使用される値

**戻り値**

単一の文字列に連結された配列値。

**使用例**
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|str|
|---|
|1->2->3|