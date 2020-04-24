---
title: サブストリング() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの部分文字列() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b0273b3e93c8778af9c380f164faec74349aa8cd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506700"
---
# <a name="substring"></a>substring()

文字列のインデックスから末尾までのインデックスから始まる部分文字列を抽出します。

必要に応じて、要求する部分文字列の長さを指定できます。

```kusto
substring("abcdefg", 1, 2) == "bc"
```

**構文**

`substring(`*ソース*`,`*の開始インデックス*[`,` *長さ*]`)`

**引数**

* *source*: 部分文字列の取得元となるソース文字列。
* *startingIndex*: 要求された部分文字列の開始位置を 0 から始めます。
* *length*: サブストリング内の要求された文字数を指定するために使用できるオプションのパラメーター。 

**メモ**

*startingIndex*には負の数を指定できます。

**戻り値**

指定した文字列の部分文字列。 部分文字列は、startingIndex の (0 から始まる) 文字位置から始まり、文字列の末尾か、指定した文字数になるまで続きます。

**使用例**

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```