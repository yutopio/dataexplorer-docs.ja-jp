---
title: カウント演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの count 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 969efc142f1cd823b319a5c98494542fb2603f24
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348736"
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
