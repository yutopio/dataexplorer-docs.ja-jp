---
title: extract_all ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの extract_all () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 55168d381ab69bf0d29e8560714e13cb635fd688
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780526"
---
# <a name="extract_all"></a>extract_all()

テキスト文字列から[正規表現](./re2.md)のすべての一致を取得します。
必要に応じて、一致するグループのサブセットを取得します。

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") // results with the dynamic array ["123", "567", "789"]
```

**構文**

`extract_all(`*regex* `,`[*captureGroups* `,` ]*テキスト*`)`

**引数**

|引数        |説明                                  |必須またはオプション  |
|----------------|---------------------------------------------|----------------------|
|regex           | [正規表現](./re2.md)。 式には、少なくとも1つのキャプチャグループと16以下のキャプチャグループが必要です。                                                         |必須              |
|captureGroups   |抽出するキャプチャグループを示す動的配列定数。 有効な値は、1から正規表現内のキャプチャグループの数までです。 名前付きキャプチャグループも使用できます ([例](#examples)を参照)。|オプション         |
|テキスト            |`string`検索する。                         |必須              |

**戻り値**

* *Regex*が*text*内で一致するものを検出すると、は、指定されたキャプチャグループの*captureGroups*、または*regex*内のすべてのキャプチャグループに対するすべての一致を含む動的配列を返します。
* Number of *captureGroups*が1の場合: 返される配列には、一致する値の1つの次元があります。
* Number of *captureGroups*が1より大きい場合: 返される配列は、 *captureGroups*選択ごとの複数値の一致の2次元コレクション、または*captureGroups*が省略されている場合は*regex*に存在するすべてのキャプチャグループです。
* 一致するものがない場合は `null` 。

## <a name="examples"></a>例

### <a name="extract-a-single-capture-group"></a>1つのキャプチャグループを抽出する

GUID の16進バイト表現 (2 桁の16進数) を返します。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["82", "b8", "be", "2d", "df", "a7", "4b", "d1", "8f", "63", "24", "ad", "26", "d3", "14", "49"]|

### <a name="extract-several-capture-groups"></a>複数のキャプチャグループを抽出する 

3つのキャプチャグループを含む正規表現を使用して、各 GUID 部分を最初の文字、最後の文字、および中間にあるものに分割します。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id)
```

|id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["8", "2b8be2"、"d"]、["d"、"fa"、"7"]、["4"、"bd"、"1"]、["8"、"f6"、"3"]、["2"、"4ad26d3144"、"9"]]|

### <a name="extract-a-subset-of-capture-groups"></a>キャプチャグループのサブセットを抽出する

キャプチャグループのサブセットを選択する方法について説明します。 正規表現は、最初の文字、最後の文字、およびすべての残りの文字と一致します。 *CaptureGroups*パラメーターは、最初と最後の部分のみを選択するために使用されます。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8"、"d"]、["d"、"7"]、["4"、"1"]、["8"、"3"]、["2"、"9"] "|

### <a name="using-named-capture-groups"></a>名前付きキャプチャグループの使用

Extract_all () では、RE2 の名前付きキャプチャグループを使用できます。
*CaptureGroups*は、キャプチャグループインデックスと名前付きキャプチャグループ参照の両方を使用して、一致する値をフェッチします。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["8", "2b8be2"、"d"]、["d"、"fa"、"7"]、["4"、"bd"、"1"]、["8"、"f6"、"3"]、["2"、"4ad26d3144"、"9"]]|
