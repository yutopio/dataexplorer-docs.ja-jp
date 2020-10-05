---
title: bag_remove_keys ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの bag_remove_keys () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/04/2020
ms.openlocfilehash: fffbda419ac123af8e2744b76c7acbe560c07ce9
ms.sourcegitcommit: 2764e739b4ad51398f4f0d3a9742d7168c4f5fd7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712349"
---
# <a name="bag_remove_keys"></a>bag_remove_keys ()

プロパティバッグからキーと関連付けられている値を削除 `dynamic` します。

## <a name="syntax"></a>Syntax

`bag_remove_keys(`*バッグ* `, `*キー*`)`

## <a name="arguments"></a>引数

* *バッグ*: `dynamic` プロパティバッグの入力。
* *keys*: `dynamic` 配列には、入力から削除するキーが含まれています。 キーは、プロパティバッグの1番目のレベルを指します。
入れ子になったレベルでのキーの指定はサポートされていません。

## <a name="returns"></a>戻り値

`dynamic`指定されたキーとその値を持たないプロパティバッグを返します。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(input:dynamic)
[
    dynamic({'key1' : 123,     'key2': 'abc'}),
    dynamic({'key1' : 'value', 'key3': 42.0}),
]
| extend result=bag_remove_keys(input, dynamic(['key2', 'key4']))
```

|input|結果|
|---|---|
|{<br>  "key1": 123、<br>  "key2": "abc"<br>}|{<br>  "key1": 123<br>}|
|{<br>  "key1": "value",<br>  "key3": 42.0<br>}|{<br>  "key1": "value",<br>  "key3": 42.0<br>}|
