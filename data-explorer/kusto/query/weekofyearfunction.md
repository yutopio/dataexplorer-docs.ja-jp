---
title: week_of_year ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの week_of_year () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 18cad42dfd0f652daa4c8da80524ba40ace9b153
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251226"
---
# <a name="week_of_year"></a>week_of_year ()

週番号を表す整数を返します。 週番号は、 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)に従って、1年の最初の週 (最初の木曜日を含む週) から計算されます。

```kusto
week_of_year(datetime("2015-12-14"))
```

## <a name="syntax"></a>構文

`week_of_year(`*a_date*`)`

## <a name="arguments"></a>引数

* `a_date`:`datetime`。

## <a name="returns"></a>戻り値

`week number` -指定された日付を含む週番号。

## <a name="examples"></a>例

|入力                                    |出力|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()` は、この関数の古い形式です。 `weekofyear()` が ISO 8601 に準拠していませんでした。1年の最初の週は、年の最初の水曜日を含む週として定義されています。
この関数の現在のバージョンは、ISO 8601 に準拠しています。 `week_of_year()` 年の最初の週は、年の最初の木曜日を含む週として定義されます。