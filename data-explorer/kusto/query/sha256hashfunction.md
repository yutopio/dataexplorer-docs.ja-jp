---
title: hash_sha256 ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの hash_sha256 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b813ce4c0901ef66177e8e7bdaa42a1744bd5912
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351116"
---
# <a name="hash_sha256"></a>hash_sha256()

入力値の sha256 ハッシュ値を返します。

## <a name="syntax"></a>構文

`hash_sha256(`*電源*`)`

## <a name="arguments"></a>引数

* *source*: ハッシュされる値。

## <a name="returns"></a>戻り値

16進数文字列としてエンコードされた、指定されたスカラーの sha256 ハッシュ値 (文字の文字列。それぞれが 0 ~ 255 の範囲の1つの16進数を表します)。

> [!WARNING]
> この関数 (SHA256) で使用されるアルゴリズムは、今後変更されることはありませんが、計算が非常に複雑です。 1つのクエリの実行中に "ライトウェイト" ハッシュ関数を必要とするユーザーには、代わりに関数[hash ()](./hashfunction.md)を使用することをお勧めします。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
h1=hash_sha256("World"),
h2=hash_sha256(datetime(2020-01-01))
```

|h1|h2|
|---|---|
|78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524|ba666752dc1a20eb750b0eb64e780cc4c968bc9fb8813461c1d7e750f302d71d|

次の例では、関数を使用して、 `hash_sha256()` 状態の SHA256 ハッシュ値に基づいて StormEvents を集計します。 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count() by State, StateHash=hash_sha256(State)
| top 5 by StormCount desc
```

|State|StateHash|StormCount|
|---|---|---|
|テキサス州|9087f20f23f91b5a77e8406846117049029e6798ebbd0d38aea68da73a00ca37|4701|
|カンザス|c80e328393541a3181b258cdb4da4d00587c5045e8cf3bb6c8fdb7016b69cc2e|3166|
|アイオワ州|f85893dca466f779410f65cd904fdc4622de49e119ad4e7c7e4a291ceed1820b|2337|
|イリノイ州|ae3eeabfd7eba3d9a4ccbfed6a9b8cff269dc43255906476282e0184cf81b7fd|2022|
|州|d15dfc28abc3ee73b7d1f664a35980167ca96f6f90e034db2a6525c0b8ba61b1|2016|
