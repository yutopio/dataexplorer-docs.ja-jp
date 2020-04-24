---
title: トダブル()/トリアル() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで todouble()/toreal() を説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: eb9c976f1646f71fcf8b345899037461f58f4ef0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506326"
---
# <a name="todoubletoreal"></a>トダブル()/トリアル()

入力を type 型に変換します`real`。 (`todouble()`と`toreal()`は同義語です。

```kusto
toreal("123.4") == 123.4
```

**構文**

`toreal(`*エクス*`)`
プル`todouble(`*エクスプル*`)`

**引数**

* *Expr*: 値が type の値に変換される`real`式。

**戻り値**

変換が成功すると、結果は type の値`real`になります。
変換が成功しなかった場合、結果は値`real(null)`です。

*注意*: 可能な場合[は double() または real() を](./scalar-data-types/real.md)使用することを優先します。