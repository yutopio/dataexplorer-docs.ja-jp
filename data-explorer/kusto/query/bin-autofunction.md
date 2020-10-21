---
title: bin_auto ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの bin_auto () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dba71c3b9b52a4edaf3a9b1260f56fc94eb935e3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245436"
---
# <a name="bin_auto"></a>bin_auto()

値を固定サイズの "ビン" に丸めます。これは、クエリプロパティによって提供されるビンサイズと開始位置を制御します。

## <a name="syntax"></a>構文

`bin_auto``(`*式*`)`

## <a name="arguments"></a>引数

* *式*: 丸め対象の値を示す数値型のスカラー式。

**クライアント要求のプロパティ**

* `query_bin_auto_size`: 各ビンのサイズを示す数値リテラル。
* `query_bin_auto_at`: "固定ポイント" (つまり、値) である *Expression* の1つの値を示す数値リテラル `fixed_point` `bin_auto(fixed_point)` == `fixed_point` 。

## <a name="returns"></a>戻り値

次の式の最も近い倍数は、 `query_bin_auto_at` *Expression* `query_bin_auto_at` それ自体に変換されるようにシフトされます。

## <a name="examples"></a>例

```kusto
set query_bin_auto_size=1h;
set query_bin_auto_at=datetime(2017-01-01 00:05);
range Timestamp from datetime(2017-01-01 00:05) to datetime(2017-01-01 02:00) step 1m
| summarize count() by bin_auto(Timestamp)
```

|Timestamp                    | count_|
|-----------------------------|-------|
|2017-01-01 00:05: 00.0000000  | 60    |
|2017-01-01 01:05: 00.0000000  | 56    |