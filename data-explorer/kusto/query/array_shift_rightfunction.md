---
title: array_shift_right ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの array_shift_right () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: a38eda3fb595256527c277b12a16f359ef9eb910
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102712"
---
# <a name="array_shift_right"></a>array_shift_right()

`array_shift_right()` 配列内の値を右にシフトします。

## <a name="syntax"></a>構文

`array_shift_right(`*`arr`*, *`shift_count`* [, *`fill_value`* ]`)`

## <a name="arguments"></a>引数

* *`arr`*: 分割する入力配列は動的配列である必要があります。
* *`shift_count`*: 配列要素が右にシフトされる位置の数を指定する整数。 値が負の場合、要素は左にシフトされます。
* *`fill_value`*: 移動および削除された要素の代わりに要素を挿入するために使用されるスカラー値。 既定値: null 値または空の文字列 ( *arr* の種類によって異なります)。

## <a name="returns"></a>戻り値

元の配列と同じ量の要素を格納している動的配列。 各要素は、に従ってシフトされてい *`shift_count`* ます。 削除された要素の代わりに追加される新しい要素の値は、になり *`fill_value`* ます。

## <a name="see-also"></a>関連項目

* 配列を左に移動する方法については、「 [array_shift_left ()](array_shift_leftfunction.md)」を参照してください。
* 配列を右に回転する方法については、「 [array_rotate_right ()](array_rotate_rightfunction.md)」を参照してください。
* 配列を左に回転する方法については、「 [array_rotate_left ()](array_rotate_leftfunction.md)」を参照してください。

## <a name="examples"></a>例

* 2つの位置で右に移動します。

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2)
    ```
    
    |→|arr_shift|
    |---|---|
    |[1、2、3、4、5]|[null、null、1、2、3]|

* 2つの位置で右にシフトし、既定値を追加します。

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2, -1)
    ```
    
    |→|arr_shift|
    |---|---|
    |[1、2、3、4、5]|[-1、-1、1、2、3]|

* 負の shift_count 値を使用して、2つの位置で左に移動します。

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, -2, -1)
    ```
    
    |→|arr_shift|
    |---|---|
    |[1、2、3、4、5]|[3、4、5、-1、-1]|
    