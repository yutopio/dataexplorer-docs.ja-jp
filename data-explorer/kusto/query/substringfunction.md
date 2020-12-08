---
title: substring() - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の substring() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7b2f2dc18fe12f4bd07b638b6c3ca32d95a10618
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512045"
---
# <a name="substring"></a>substring()

あるインデックスから始まるソース文字列から文字列の末尾までの部分文字列を抽出します。

必要に応じて、要求する部分文字列の長さを指定できます。

```kusto
substring("abcdefg", 1, 2) == "bc"
```

## <a name="syntax"></a>構文

`substring(`*source*`,` *startingIndex* [`,` *length*]`)`

## <a name="arguments"></a>引数

* *source*: 部分文字列の取得元となるソース文字列。
* *startingIndex*:要求する部分文字列の、0 から始まる開始文字位置。
* *length*:要求する部分文字列の文字数を指定するために使用できる省略可能なパラメーター。 

**メモ**

*startingIndex* には負の数を指定できます。この場合、部分文字列はソース文字列の末尾から取得されます。

## <a name="returns"></a>戻り値

指定した文字列の部分文字列。 部分文字列は、startingIndex の (0 から始まる) 文字位置から始まり、文字列の末尾か、指定した文字数になるまで続きます。

## <a name="examples"></a>使用例

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```