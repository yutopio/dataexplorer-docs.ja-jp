---
title: split ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの split () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 4baae5bee8dd1e85a304be7fb4eae988acc404d8
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512946"
---
# <a name="split"></a>split()

指定された区切り記号に従って指定された文字列を分割し、含まれている部分文字列を含む文字列配列を返します。

必要に応じて、特定の部分文字列が存在すれば、それを返すこともできます。

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>Syntax

`split(`*ソース* `,`*区切り記号*[ `,` *requestedindex*]`)`

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