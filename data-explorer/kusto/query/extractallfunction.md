---
title: extract_all() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで extract_all() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f7623a064e272f8b96ca25cc6af47318e26dfb93
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515421"
---
# <a name="extract_all"></a>extract_all()

テキスト文字列から[正規表現](./re2.md)のすべての一致を取得します。

必要に応じて、一致するグループのサブセットを取得できます。

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") == dynamic(["123", "567", "789"])
```

**構文**

`extract_all(`*正規表現*`,`[*キャプチャグループ*`,`]*テキスト*`)`

**引数**

* *正規表現*:[正規表現](./re2.md). 正規表現には、少なくとも 1 つのキャプチャ グループがあり、16 個以下のキャプチャ グループが必要です。
* *キャプチャグループ*: (オプション)。 抽出するキャプチャ グループを示す動的配列定数。 有効な値は、正規表現のキャプチャ グループの数の 1 からです。 名前付きキャプチャ グループも同様に許可されます (例のセクションを参照)。
* *text*:`string`検索する A。

**戻り値**

*regex*が*text*で一致を見つけた場合: 指定されたキャプチャ グループ*captureGroups* (または*regex*内のすべてのキャプチャ グループ) に対するすべての一致を含む動的配列を返します。
*captureGroups*の数が 1 の場合: 返される配列は、一致した値の 1 つの次元を持ちます。
*captureGroups*の数が 1 より多い場合: 返される配列は *、captureGroups*の選択ごとに複数値の一致の 2 次元コレクションです (または*captureGroups*が省略されている場合は *、regex*に存在するすべてのキャプチャ グループ) 

一致するものがない場合: `null`. 

**使用例**

### <a name="extracting-single-capture-group"></a>単一のキャプチャ グループの抽出
次の例では、GUID の 16 進数のバイト表現 (2 桁の 16 進数) を返します。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[82,"b8"、""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""14",49"]|

### <a name="extracting-several-capture-groups"></a>複数のキャプチャ グループの抽出 
次の例では、3 つのキャプチャ グループを含む正規表現を使用して、各 GUID 部分を最初の文字、最後の文字、および中間の任意の部分に分割します。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["8","2b8be2",d"],[d"""fa",7"],[4","bd",1"],[8","f6","3"],[2","4ad26d3144",9"]|

### <a name="extracting-subset-of-capture-groups"></a>キャプチャ グループのサブセットの抽出

次の例では、キャプチャ グループのサブセットを選択する方法を示します: この場合、正規表現は最初の文字、最後の文字、およびすべての残りの部分に一致します - *captureGroups*パラメーターは最初と最後の部分のみを選択するために使用されます。 

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["8","d"],[d",7"],[4",1"],[8",3"],[2",9"]|


### <a name="using-named-capture-groups"></a>名前付きキャプチャ グループの使用

extract_all() で RE2 の名前付きキャプチャ グループを利用できます。 次の例では *、captureGroups*はキャプチャ グループ インデックスと名前付きキャプチャ グループ参照の両方を使用して、一致する値をフェッチします。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["8","2b8be2",d"],[d"""fa",7"],[4","bd",1"],[8","f6","3"],[2","4ad26d3144",9"]|