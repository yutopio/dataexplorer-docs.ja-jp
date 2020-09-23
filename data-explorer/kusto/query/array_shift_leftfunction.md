---
title: array_shift_left ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの array_shift_left () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 883aa2e22c02584de34c705b89a979b86e10475e
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102802"
---
# <a name="array_shift_left"></a>array_shift_left()

`array_shift_left()` 配列内の値を左にシフトします。

## <a name="syntax"></a>構文

`array_shift_left(`*`arr`*, *`shift_count`* `[,` *`fill_value`* ]`)`

## <a name="arguments"></a>引数

* *`arr`*: 分割する入力配列は動的配列である必要があります。
* *`shift_count`*: 配列要素が左にシフトされる位置の数を指定する整数。 値が負の場合、要素は右にシフトされます。
* *`fill_value`*: 移動および削除された要素の代わりに要素を挿入するために使用されるスカラー値。 既定値: null 値または空の文字列 (型によって異なり *`arr`* ます)。

## <a name="returns"></a>戻り値

元の配列と同じ数の要素を格納している動的配列。 各要素は *shift_count*に従ってシフトされています。 削除された要素の代わりに追加される新しい要素の値は *fill_value*になります。

## <a name="see-also"></a>関連項目

* 配列を右に移動する方法については、「 [array_shift_right ()](array_shift_rightfunction.md)」を参照してください。
* 配列を右に回転する方法については、「 [array_rotate_right ()](array_rotate_rightfunction.md)」を参照してください。
* 配列を左に回転する方法については、「 [array_rotate_left ()](array_rotate_leftfunction.md)」を参照してください。

## <a name="examples"></a>例

* 次の2つの位置に左に移動します。

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1、2、3、4、5]|[3、4、5、null、null]|

* 2つの位置で左にシフトし、既定値を追加します。

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2, -1)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1、2、3、4、5]|[3、4、5、-1、-1]|


* 負の *shift_count* 値を使用して、2つの位置に右にシフトする。

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, -2, -1)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1、2、3、4、5]|[-1、-1、1、2、3]|
