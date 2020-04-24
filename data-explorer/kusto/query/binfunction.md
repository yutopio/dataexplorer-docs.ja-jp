---
title: ビン() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの bin() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3fb827c71fa63fde031a91bc9aec7f0ed108fd5c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517427"
---
# <a name="bin"></a>bin()

値を切り捨てて、指定された bin サイズの倍数である整数にします。 

と組み合[`summarize by ...`](./summarizeoperator.md)わせて頻繁に使用されます。
値が分散している場合に、特定の値ごとの小さなセットにグループ化されます。

NULL 値、null ビン サイズ、または負のビン サイズは NULL になります。 

エイリアスを`floor()`使用して機能します。

**構文**

`bin(`*値*`,`*ラウンドTo*`)`

**引数**

* *値*: 数値、日付、または期間。 
* *ラウンドTo*: "ビンサイズ" *value*を分割する数値、日付、または期間です。 

**戻り値**

*value* 未満で、*roundTo* の最も近い倍数。  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

**使用例**

式 | 結果
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


次の式は、バケットのサイズを 1 秒として、各期間のヒストグラムを計算します。

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```