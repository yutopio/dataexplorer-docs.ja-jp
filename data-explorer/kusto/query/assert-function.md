---
title: アサート() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで assert() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 97f01df7bc85171eefabeb2ed835109f0faef81e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518413"
---
# <a name="assert"></a>assert()

条件をチェックします。 条件が false の場合、エラー メッセージが出力され、クエリは失敗します。

**構文**

`assert(`*条件*`, `*メッセージ*`)`

**引数**

* *条件*: 評価する条件式。 条件が の`false`場合、指定されたメッセージはエラーの報告に使用されます。 条件が の`true`場合、評価`true`結果として返されます。 条件は、クエリ分析フェーズ中に定数に評価する必要があります。
* *message*: アサーションが に評価される`false`場合に使用されるメッセージです。 *メッセージ*は文字列リテラルである必要があります。


**戻り値**

* `true`- 条件が`true`
* 条件が に評価された場合に、セマンティ`false`ック エラーが発生します。

**メモ**

* `condition`は、クエリ分析フェーズ中に定数に評価する必要があります。 つまり、定数を参照する他の式から構築でき、行コンテキストにバインドすることはできません。

**使用例**

次のクエリは、入力`checkLength()`文字列の長さをチェックし、入力`assert`長パラメータの検証に使用する関数を定義しています (0 より大きいかどうかをチェックします)。

```kusto
let checkLength = (len:long, s:string)
{
    assert(len > 0, "Length must be greater than zero") and 
    strlen(s) > len
};
datatable(input:string)
[
    '123',
    '4567'
]
| where checkLength(len=long(-1), input)
```

このクエリを実行すると、エラーが発生します。  
`assert() has failed with message: 'Length must be greater than zero'`


有効な`len`入力を使用して実行する例:

```kusto
let checkLength = (len:long, s:string)
{
    assert(len > 0, "Length must be greater than zero") and strlen(s) > len
};
datatable(input:string)
[
    '123',
    '4567'
]
| where checkLength(len=3, input)
```

|input|
|---|
|4567|