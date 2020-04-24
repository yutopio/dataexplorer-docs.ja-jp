---
title: array_rotate_right() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで array_rotate_right() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 4f45db14f6ca2fe990f8d139c608ebed1915aa16
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518804"
---
# <a name="array_rotate_right"></a>array_rotate_right()

`array_rotate_right()`配列内の値を右に回転します。

**構文**

`array_rotate_right(`*arr*, *rotate_count*`)`

**引数**

* *arr*: 分割する入力配列は動的配列でなければなりません。
* *rotate_count*: 配列要素を右に回転させる位置の数を指定する整数。 値が負の場合、要素は左に回転します。

**戻り値**

元の配列と同じ量の要素を含む動的配列で、各要素は*rotate_count*に従って回転されます。

**参照**

* 左に配列を回転させる場合は[、array_rotate_left()](array_rotate_leftfunction.md)を参照してください。
* 配列を左にシフトする場合は[、array_shift_left()](array_shift_leftfunction.md)を参照してください。
* 配列を右にシフトする場合は[、array_shift_right()](array_shift_rightfunction.md)を参照してください。

**使用例**

* 2 つの位置で右に回転:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, 2)
    ```
    
    |Arr|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[4,5,1,2,3]|

* 負のrotate_count値を使用して、2 つの位置で左に回転する:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, -2)
    ```
    
    |Arr|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,1,2]|