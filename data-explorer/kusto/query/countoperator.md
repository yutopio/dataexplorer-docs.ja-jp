---
title: カウント演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの count 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: bf595e27d7b4881dca7b5e2a370c90a8407a8c78
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252598"
---
# <a name="count-operator"></a>count 演算子

入力レコードセット内のレコードの数を返します。

## <a name="syntax"></a>構文

`T | count`

## <a name="arguments"></a>引数

*T*: カウントするレコードが含まれる表形式のデータ。

## <a name="returns"></a>戻り値

この関数は、1 つのレコードと `long`型の列を含むテーブルを返します。 唯一のセルの値は、 *T*内のレコードの数です。 

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
