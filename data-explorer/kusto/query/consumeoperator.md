---
title: オペレーターを消費する - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの使用オペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 65c2f2befc074042131b5c0d705fa942a1622035
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517121"
---
# <a name="consume-operator"></a>consume 演算子

オペレータに渡される表形式のデータ ストリームを使用します。 

演算子`consume`は、実際に結果を呼び出し元に返すことなく、クエリの副作用をトリガーするために使用されます。

```kusto
T | consume
```

**構文**

`consume`[`decodeblocks` `=` *デコードブロック*]

**引数**

* *デコードブロック*: 定数のブール値。 に`true`設定するか、request プロパティ`perftrace`が に`true`設定されている場合、`consume`演算子は入力時にレコードを列挙するだけでなく、実際にはそれらのレコードの各値を圧縮解除およびデコードします。

この`consume`演算子を使用すると、実際に結果をクライアントに返すことなく、クエリのコストを見積もることができます。
(たとえば、推定はさまざまな理由で正確ではありません。たとえば、`consume`分散的に計算されるため`T | consume`、クラスターのノード間でテーブルのデータが送信されません)。

<!--
* *WithStats*: A constant Boolean value. If set to `true` (or if the global
  property `perftrace` is set), the operator will return a single
  row with a single column called `Stats` of type `dynamic` holding the statistics
  of the data source fed to the `consume` operator.
-->