---
title: dynamic_to_json ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの dynamic_to_json () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 08/05/2020
ms.openlocfilehash: 94901a69b4c4435e983f39f1692cf45cf27756e5
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793790"
---
# <a name="dynamic_to_json"></a>dynamic_to_json()

`dynamic`入力を文字列形式に変換します。
入力がプロパティバッグの場合、出力文字列はキーによって並べ替えられた内容を再帰的に出力します。 それ以外の場合、出力は関数の出力に似てい `tostring` ます。

## <a name="syntax"></a>構文

`dynamic_to_json(Expr)`

## <a name="arguments"></a>引数

* *Expr*: `dynamic` 入力。 関数は、1つの引数を受け取ります。

## <a name="returns"></a>戻り値

入力の文字列形式を返し `dynamic` ます。 入力がプロパティバッグの場合、出力文字列はキーによって並べ替えられた内容を再帰的に出力します。

## <a name="examples"></a>例

式:

```kusto
  let bag1 = dynamic_to_json(dynamic({ 'Y10':dynamic({ }), 'X8': dynamic({ 'c3':1, 'd8':5, 'a4':6 }),'D1':114, 'A1':12, 'B1':2, 'C1':3, 'A14':[15, 13, 18]}));
  print bag1
```
  
結果:

```
"{
  ""A1"": 12,
  ""A14"": [
    15,
    13,
    18
  ],
  ""B1"": 2,
  ""C1"": 3,
  ""D1"": 114,
  ""X8"": {
    ""c3"": 1,
    ""d8"": 5,
    ""a4"": 6
  },
  ""Y10"": {}
}"
```

式:

```kusto
 let bag2 = dynamic_to_json(dynamic({ 'X8': dynamic({ 'a4':6, 'c3':1, 'd8':5}), 'A14':[15, 13, 18], 'C1':3, 'B1':2, 'Y10': dynamic({ }), 'A1':12, 'D1':114}));
 print bag2
```
 
結果:

```
{
  "A1": 12,
  "A14": [
    15,
    13,
    18
  ],
  "B1": 2,
  "C1": 3,
  "D1": 114,
  "X8": {
    "a4": 6,
    "c3": 1,
    "d8": 5
  },
  "Y10": {}
}
```
