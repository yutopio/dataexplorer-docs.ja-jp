---
title: now ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーで () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 63b99b4774c46872b83e526ca00a5d974f0b0c19
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249799"
---
# <a name="now"></a>now()

現在の UTC クロック時刻を返します。オプションで、指定した timespan でオフセットします。
この関数はステートメント内で複数回使用することができます。また、参照される時刻はすべてのインスタンスで同じになります。

```kusto
now()
now(-2d)
```

## <a name="syntax"></a>構文

`now(`[*オフセット*]`)`

## <a name="arguments"></a>引数

* *offset*: `timespan` 現在の UTC 時刻に追加された。 既定値は0。

## <a name="returns"></a>戻り値

`datetime`型の現在の UTC 時刻。

`now()` + *影* 

## <a name="example"></a>例

述語によって特定されるイベント以降の期間を判断します。

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```