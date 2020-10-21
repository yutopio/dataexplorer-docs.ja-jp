---
title: replace ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの replace () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d9fdcfaad41201cd99796c3f4966593aa7480e49
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243064"
---
# <a name="replace"></a>replace()

正規表現のすべての一致を別の文字列に置き換えます。

## <a name="syntax"></a>構文

`replace(`*regex* `,`*書き換え* `,`*テキスト*`)`

## <a name="arguments"></a>引数

* *regex*:*テキスト*を検索する[正規表現](https://github.com/google/re2/wiki/Syntax)。 複数のキャプチャ グループをかっこ内に含めることができます。 
* *リライト*: *matchingRegex*によって行われたすべての一致の置換 regex。 完全一致を参照する場合は `\0`、最初のキャプチャ グループの場合は `\1`、後続のキャプチャ グループの場合は `\2` などを使用します。
* *text*: 文字列。

## <a name="returns"></a>戻り値

*regex* のすべての一致を *rewrite* の評価で置き換えた後の *text*。 一致が重なり合うことはありません。

## <a name="example"></a>例

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
 