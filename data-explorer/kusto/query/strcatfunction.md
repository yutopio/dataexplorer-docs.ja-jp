---
title: strcat() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで strcat() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dd01f875b45be038371cc184987aa2a8f8b8d5eb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506921"
---
# <a name="strcat"></a>strcat()

1 個から 64 個の引数を連結します。

* 引数が文字列型でない場合、引数は強制的に文字列に変換されます。

**構文**

`strcat(`*引数 1*,*引数 2* *[, 引数N]*`)`

**引数**

* *引数1..**引数N* : 連結する式。

**戻り値**

引数を単一の文字列に連結します。

**使用例**
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|