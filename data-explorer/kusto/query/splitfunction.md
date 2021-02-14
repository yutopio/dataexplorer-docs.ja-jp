---
title: split() - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の split() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: ed58a1d249a74c02445968e81930e303aeff0d56
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100360066"
---
# <a name="split"></a>split()

指定された区切り記号に従って指定された文字列を分割し、部分文字列が含まれている文字列配列を返します。

必要に応じて、特定の部分文字列が存在すれば、それを返すこともできます。

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>構文

`split(`*source*`,` *delimiter* [`,` *requestedIndex*]`)`

## <a name="arguments"></a>引数

* *source*: 指定された区切り記号に従って分割されるソース文字列。
* *delimiter*: ソース文字列を分割するために使用される区切り記号。
* *requestedIndex*: 0 から始まるインデックス `int` (省略可能)。 指定した場合、返される文字列配列には、要求した部分文字列が含まれます (存在する場合)。 

## <a name="returns"></a>戻り値

指定された区切り記号によって分割された、指定されたソース文字列の部分文字列が含まれている文字列配列。

## <a name="examples"></a>使用例

```kusto
print
    split("aa_bb", "_"),           // ["aa","bb"]
    split("aaa_bbb_ccc", "_", 1),  // ["bbb"]
    split("", "_"),                // [""]
    split("a__b", "_"),            // ["a","","b"]
    split("aabbcc", "bb")          // ["aa","cc"]
```