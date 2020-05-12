---
title: array_rotate_left ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの array_rotate_left () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: ae28848ee46a4313ac2f24fb8a796cd0ced3ba4d
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225821"
---
# <a name="array_rotate_left"></a>array_rotate_left()

`array_rotate_left()`配列内の値を左に回転します。

**構文**

`array_rotate_left(`*arr*、 *rotate_count*`)`

**引数**

* *arr*: 分割する入力配列。動的配列である必要があります。
* *rotate_count*: 配列要素が左に回転する位置の数を指定する整数。 値が負の場合、要素は右に回転します。

**戻り値**

元の配列と同じ量の要素を格納している動的配列。各要素は*rotate_count*に従って回転されています。

**参照**

* 配列を右に回転させる方法については、「 [array_rotate_right ()](array_rotate_rightfunction.md)」を参照してください。
* 配列を左にシフトする方法については、「 [array_shift_left ()](array_shift_leftfunction.md)」を参照してください。
* 配列を右にシフトする場合は、「 [array_shift_right ()](array_shift_rightfunction.md)」を参照してください。

**使用例**

* 次の2つの位置で左に回転します。

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, 2)
    ```
    
    |→|arr_rotated|
    |---|---|
    |[1、2、3、4、5]|[3、4、5、1、2]|

* 負の rotate_count 値を使用して、2つの位置に右に回転します。

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, -2)
    ```
    
    |→|arr_rotated|
    |---|---|
    |[1、2、3、4、5]|[4、5、1、2、3]|