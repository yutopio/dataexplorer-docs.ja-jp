---
title: strcmp() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで strcmp() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 62ebcbfa71ebf8a29f3a8a1559feb91f96e0a2bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506904"
---
# <a name="strcmp"></a>strcmp()

2 つの文字列を比較します。

この関数は、各文字列の最初の文字の比較を開始します。 互いに等しい場合は、文字が異なるまで、または短い文字列の終わりに達するまで、次のペアで続行されます。

**構文**

`strcmp(`*文字列1*`,`*文字列2*`)` 

**引数**

* *string1*: 比較用の最初の入力文字列。 
* *string2*: 比較用の 2 番目の入力文字列。

**戻り値**

文字列間の関係を示す整数値を返します。
* *<0* - 一致しない最初の文字は、string1 の値が string2 よりも小さい値です。
* *0* - 両方の文字列の内容が等しい
* *>0* - 一致しない最初の文字は、string1 の値が string2 よりも大きい値を持ちます。

**使用例**

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
|Abcde|abc|1|