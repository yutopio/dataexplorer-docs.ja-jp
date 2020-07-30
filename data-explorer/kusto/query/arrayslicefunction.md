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
ms.openlocfilehash: e2216361022f055078be66f37f3d2b084afaa4c6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349535"
---
# <a name="array_slice"></a>array_slice()

動的配列のスライスを抽出します。

## <a name="syntax"></a>構文

`array_slice`(*`arr`*, *`start`*, *`end`*)

## <a name="arguments"></a>引数

* *`arr`*: スライスを抽出する入力配列は動的配列でなければなりません。
* *`start`*: スライスの0から始まる (包括) 開始インデックス。負の値は array_length + start に変換されます。
* *`end`*: スライスの0から始まる (包括) 終了インデックス。負の値は array_length + end に変換されます。

注: 範囲外のインデックスは無視されます。

## <a name="returns"></a>戻り値

の範囲 [] の値の動的配列 `start..end` `arr` 。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|`arr`|`sliced`|
|---|---|
|[1, 2, 3]|[2, 3]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|`arr`|スライス|
|---|---|
|[1、2、3、4、5]|[3、4、5]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|`arr`|スライス|
|---|---|
|[1、2、3、4、5]|[3, 4]|
