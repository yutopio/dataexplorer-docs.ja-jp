---
title: エンドフウィーク() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの週末の週末() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 83c080c60e34dbfdf19f7dde870621e34ded836d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515829"
---
# <a name="endofweek"></a>endofweek()

指定した場合、日付を含む週の終わり (オフセットでシフト) を返します。

週の最終日は土曜日と見なされます。

**構文**

`endofweek(`*日付*`,`[*オフセット*]`)`

**引数**

* `date`: 入力日付。
* `offset`: 入力日付からのオフセット週数 (整数、デフォルト - 0)。

**戻り値**

指定された*日付*値の週の終わりを表す日時。(オフセットが指定されている場合)。

**例**

```kusto
  range offset from -1 to 1 step 1
 | project weekEnd = endofweek(datetime(2017-01-01 10:10:17), offset)  

```

|週末|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-07 23:59:59.9999999|
|2017-01-14 23:59:59.9999999|