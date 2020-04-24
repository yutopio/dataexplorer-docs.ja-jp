---
title: countof() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで countof() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d932fbcea9b38849e7d7de09230c9a5aa9fa8e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516900"
---
# <a name="countof"></a>countof()

文字列内の部分文字列の出現回数をカウントします。 プレーン文字列一致では、重なり合う可能性があります。正規表現では、重なり合いません。

```kusto
countof("The cat sat on the mat", "at") == 3
countof("The cat sat on the mat", @"\b.at\b", "regex") == 3
```

**構文**

`countof(`*テキスト*`,`*検索*`,` [*種類*]`)`

**引数**

* *text*: 文字列。
* *search*:*テキスト*内で一致するプレーン文字列または[正規表現](./re2.md)。
* *種類* `"normal"|"regex"` :`normal`デフォルト . 

**戻り値**

コンテナー内で検索文字列が一致する回数。 プレーン文字列一致では、重なり合う可能性があります。正規表現では、重なり合いません。

**使用例**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (2 ではありません)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    