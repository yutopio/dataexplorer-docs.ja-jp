---
title: replace() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで replace() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 84a741f10172ef418da7d92b8c1ad6ba26593d72
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510338"
---
# <a name="replace"></a>replace()

正規表現のすべての一致を別の文字列に置き換えます。

**構文**

`replace(`*正規表現*`,`*書き換え*`,`*テキスト*`)`

**引数**

* *正規表現*:*テキスト*を検索するための[正規表現](https://github.com/google/re2/wiki/Syntax)。 複数のキャプチャ グループをかっこ内に含めることができます。 
* *書き換え*: *matchRegex*によって行われた任意の一致の代わりの正規表現。 完全一致を参照する場合は `\0`、最初のキャプチャ グループの場合は `\1`、後続のキャプチャ グループの場合は `\2` などを使用します。
* *text*: 文字列。

**戻り値**

*regex* のすべての一致を *rewrite* の評価で置き換えた後の *text*。 一致が重なり合うことはありません。

**例**

次のステートメントを実行します。

```kusto
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

結果は次のとおりです。

| x    | str | replaced|
|---|---|---|
| 1    | Number is 1.000000  | Number was: 1.000000|
| 2    | Number is 2.000000  | Number was: 2.000000|
| 3    | Number is 3.000000  | Number was: 3.000000|
| 4    | Number is 4.000000  | Number was: 4.000000|
| 5    | Number is 5.000000  | Number was: 5.000000|
 