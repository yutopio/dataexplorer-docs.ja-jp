---
title: strcmp ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの strcmp () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 11951195d95d956f70d4bfce32d22a9f9c73dc3d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350929"
---
# <a name="strcmp"></a>strcmp()

2 つの文字列を比較します。

関数は、各文字列の最初の文字の比較を開始します。 これらが互いに等しい場合は、文字が異なるか、短い文字列の末尾に到達するまで、次のペアで続行されます。

## <a name="syntax"></a>構文

`strcmp(`*string1* `,`*string2*`)` 

## <a name="arguments"></a>引数

* *string1*: 比較用の最初の入力文字列。 
* *string2*: 比較する2番目の入力文字列。

## <a name="returns"></a>戻り値

文字列間の関係を示す整数値を返します。
* *<0* -一致しない最初の文字の値が、string1 の文字列よりも小さい値になっています
* *0* -両方の文字列の内容が等しい
* *>0* -一致しない最初の文字の値が、string1 の値よりも大きい値になっています

## <a name="examples"></a>例

```
datatable(string1:string, string2:string)
["ABC","ABC",
"abc","ABC",
"ABC","abc",
"abcde","abc"]
| extend result = strcmp(string1,string2)
```

|string1|string2|結果|
|---|---|---|
|ABC|ABC|0|
|abc|ABC|1|
|ABC|abc|-1|
|abcde...z|abc|1|