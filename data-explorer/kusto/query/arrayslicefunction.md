---
title: array_slice ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの array_slice () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: f2de8d91b73a3495c8b0902d710bd87c7fecbafa
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737557"
---
# <a name="array_slice"></a>array_slice()

動的配列のスライスを抽出します。

**構文**

`array_slice`(*`arr`*, *`start`*, *`end`*])

**引数**

* *`arr`*: スライスを抽出する入力配列は動的配列でなければなりません。
* *`start`*: スライスの0から始まる (包括) 開始インデックス。負の値は array_length + start に変換されます。
* *`end`*: スライスの0から始まる (包括) 終了インデックス。負の値は array_length + end に変換されます。

注: 範囲外のインデックスは無視されます。

**戻り値**

の範囲 [`start..end`] の値の動的配列`arr`。

**使用例**


```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|`arr`|`sliced`|
|---|---|
|[1, 2, 3]|[2, 3]|


```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|`arr`|スライス|
|---|---|
|[1、2、3、4、5]|[3、4、5]|


```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|`arr`|スライス|
|---|---|
|[1、2、3、4、5]|[3, 4]|