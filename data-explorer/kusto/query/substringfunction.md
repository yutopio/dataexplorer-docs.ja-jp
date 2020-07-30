---
title: substring ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの substring () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b0e83e8d0baf33e5c11cb8b7ecafa607a08fe32b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350861"
---
# <a name="substring"></a>substring()

あるインデックスから文字列の末尾までの位置から、ソース文字列から部分文字列を抽出します。

必要に応じて、要求する部分文字列の長さを指定できます。

```kusto
substring("abcdefg", 1, 2) == "bc"
```

## <a name="syntax"></a>構文

`substring(`*ソース* `,`*startingIndex* [ `,` *長さ*]`)`

## <a name="arguments"></a>引数

* *source*: 部分文字列が取得されるソース文字列。
* *startingIndex*: 要求された部分文字列の0から始まる開始文字位置。
* *length*: 省略可能なパラメーター。部分文字列で要求された文字数を指定するために使用できます。 

**メモ**

*startingIndex*には負の数を指定できます。この場合、部分文字列はソース文字列の末尾から取得されます。

## <a name="returns"></a>戻り値

指定した文字列の部分文字列。 部分文字列は、startingIndex の (0 から始まる) 文字位置から始まり、文字列の末尾か、指定した文字数になるまで続きます。

## <a name="examples"></a>使用例

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```