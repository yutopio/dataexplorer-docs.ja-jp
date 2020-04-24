---
title: week_of_year() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで week_of_year() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 1c3702165f01ab321f80bad900e59968092be2ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504541"
---
# <a name="week_of_year"></a>week_of_year()

週番号を表す整数を返します。 週番号は[、ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)によると、最初の木曜日を含む 1 年の最初の週から計算されます。

```kusto
week_of_year(datetime("2015-12-14"))
```

**構文**

`week_of_year(`*a_date*`)`

**引数**

* `a_date`: `datetime`。

**戻り値**

`week number`- 指定された日付を含む週番号。

**使用例**

|入力                                    |出力|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()`は、この関数の古いバリアントです。 `weekofyear()`ISO 8601 準拠ではありません。年の最初の週は、その年の最初の水曜日を含む週として定義されました。
この関数の現在のバージョンは`week_of_year()`、ISO 8601 準拠です。年の最初の週は、その年の最初の木曜日を含む週として定義されます。