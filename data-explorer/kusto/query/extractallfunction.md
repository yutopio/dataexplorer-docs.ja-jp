---
title: extract_all ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの extract_all () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 94bafd3890f57a1379440c5cced3fa349f9055c5
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616424"
---
# <a name="extract_all"></a>extract_all()

テキスト文字列から[正規表現](./re2.md)のすべての一致を取得します。

必要に応じて、一致するグループのサブセットを取得できます。

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") // results with the dynamic array ["123", "567", "789"]
```

**構文**

`extract_all(`*regex* `,` [*captureGroups*`,`]*テキスト*`)`

**引数**

* *regex*:[正規表現](./re2.md)。 正規表現には、少なくとも1つのキャプチャグループと、16以下のキャプチャグループが必要です。
* *captureGroups*: (省略可能)。 抽出するキャプチャグループを示す動的配列定数。 有効な値は、正規表現内のキャプチャグループの 1 ~ 数です。 名前付きキャプチャグループも使用できます (「例」セクションを参照してください)。
* *text*: 検索`string`対象の。

**戻り値**

*Regex*が*text*内で一致するものを検索する場合: 指定されたキャプチャグループ*captureGroups* (または*regex*内のすべてのキャプチャグループ) に対するすべての一致を含む動的配列を返します。
Number of *captureGroups*が1の場合: 返される配列には、一致する値の1つの次元があります。
Number of *captureGroups*が1以上の場合: 返される配列は、 *captureGroups* Selection (または*captureGroups*が省略されている場合は、 *regex*に存在するすべてのキャプチャグループ) の2次元コレクションです。 

一致するものがない場合`null`は。 

**使用例**

### <a name="extracting-single-capture-group"></a>1つのキャプチャグループを抽出しています
次の例では、GUID の16進数表現 (2 桁の16進数) が返されます。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["82", "b8", "be", "2d", "df", "a7", "4b", "d1", "8f", "63", "24", "ad", "26", "d3", "14", "49"]|

### <a name="extracting-several-capture-groups"></a>複数のキャプチャグループの抽出 
次の例では、3つのキャプチャグループを含む正規表現を使用して、各 GUID 部分を最初の文字、最後の文字、および中間に分割します。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["8", "2b8be2"、"d"]、["d"、"fa"、"7"]、["4"、"bd"、"1"]、["8"、"f6"、"3"]、["2"、"4ad26d3144"、"9"]]|

### <a name="extracting-subset-of-capture-groups"></a>キャプチャグループのサブセットを抽出しています

次の例では、キャプチャグループのサブセットを選択する方法を示しています。この例では、正規表現は最初の文字、最後の文字、すべての残りの部分に一致しますが、 *captureGroups*パラメーターを使用して最初と最後の部分のみを選択します。 

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8"、"d"]、["d"、"7"]、["4"、"1"]、["8"、"3"]、["2"、"9"] "|


### <a name="using-named-capture-groups"></a>名前付きキャプチャグループの使用

Extract_all () では、RE2 の名前付きキャプチャグループを利用できます。 次の例では、 *captureGroups*はキャプチャグループインデックスと名前付きキャプチャグループ参照の両方を使用して、一致する値をフェッチします。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["8", "2b8be2"、"d"]、["d"、"fa"、"7"]、["4"、"bd"、"1"]、["8"、"f6"、"3"]、["2"、"4ad26d3144"、"9"]]|
