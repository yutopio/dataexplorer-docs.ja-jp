---
title: array_slice() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで array_slice() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: ed5c320ef639f7545e19bcaabd9b53f4424c959d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518583"
---
# <a name="array_slice"></a>array_slice()

動的配列のスライスを抽出します。

**構文**

`array_slice(`*arr*,*開始*,*終了*]`)`

**引数**

* *arr*: スライスを抽出する入力配列は、動的配列でなければなりません。
* *start*: スライスの始点を 0 から始めるインデックスを 0 から始め、負の値は array_length+ start に変換されます。
* *end*: スライスの 0 から始まる (包括的な) 終了インデックスを指定すると、負の値は array_length+ end に変換されます。

注: 範囲外のインデックスは無視されます。

**戻り値**

範囲内の値の動的配列 [start..終了]arrから。

**使用例**

1.
```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|Arr|スライス|
|---|---|
|[1,2,3]|[2,3]|


2.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|Arr|スライス|
|---|---|
|[1,2,3,4,5]|[3,4,5]|


3.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|Arr|スライス|
|---|---|
|[1,2,3,4,5]|[3,4]|