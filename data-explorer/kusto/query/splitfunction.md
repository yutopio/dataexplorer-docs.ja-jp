---
title: split() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで split() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6e3935c524056afd27eb0d5e2d80925e072c8fa7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507465"
---
# <a name="split"></a>split()

指定した区切り文字に従って指定した文字列を分割し、含まれている部分文字列を含む文字列配列を返します。

必要に応じて、特定の部分文字列が存在すれば、それを返すこともできます。

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

**構文**

`split(`*ソース*`,`*区切り文字*[`,` *要求されたインデックス*]`)`

**引数**

* *source*: 指定された区切り文字に従って分割されるソース文字列。
* *delimiter*: ソース文字列を分割するために使用される区切り記号。
* *requestedIndex*: 0 から始まるインデックス `int` (省略可能)。 指定した場合、返される文字列配列には、要求した部分文字列が含まれます (存在する場合)。 

**戻り値**

指定された区切り記号によって分割された、指定されたソース文字列の部分文字列が含まれている文字列配列。

**使用例**

```kusto
print
    split("aa_bb", "_"),           // ["aa","bb"]
    split("aaa_bbb_ccc", "_", 1),  // ["bbb"]
    split("", "_"),                // [""]
    split("a__b", "_"),            // ["a","","b"]
    split("aabbcc", "bb")          // ["aa","cc"]
```