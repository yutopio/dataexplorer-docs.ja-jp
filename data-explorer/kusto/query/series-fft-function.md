---
title: series_fft ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_fft () 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: 32b3b978f57f1a346ccd697fa2d95d962b558a15
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502177"
---
# <a name="series_fft"></a>series_fft ()

系列に高速フーリエ変換 (FFT) を適用します。  

Series_fft () 関数は、時間/空間ドメイン内の一連の複素数を受け取り、 [高速フーリエ変換](https://en.wikipedia.org/wiki/Fast_Fourier_transform)を使用して frequency ドメインに変換します。 変換された複合系列は、元の系列に表示される周波数の大きさとフェーズを表します。 周期的関数 [series_ifft](series-ifft-function.md) を使用して、frequency ドメインから時刻/空間ドメインに変換します。

## <a name="syntax"></a>構文

`series_fft(`*x_real* [ `,` *x_imaginary*]`)`

## <a name="arguments"></a>引数

* *x_real*: 変換する系列の実際のコンポーネントを表す数値の動的配列。
* *x_imaginary*: 系列の虚数部を表す同様の動的配列。 このパラメーターは省略可能です。入力系列に複雑な数値が含まれている場合にのみ指定する必要があります。

## <a name="returns"></a>戻り値

関数は、2つの系列の複雑な逆 fft を返します。 実コンポーネントの最初の系列と虚数部の2番目の系列。

## <a name="example"></a>例

* 実数部と虚数部の周波数が異なる単純な正弦波である複雑な系列を生成します。 FFT を使用して、frequency ドメインに変換します。

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let sinewave=(x:double, period:double, gain:double=1.0, phase:double=0.0)
    {
        gain*sin(2*pi()/period*(x+phase))
    }
    ;
    let n=128;      //  signal length
    range x from 0 to n-1 step 1 | extend yr=sinewave(x, 8), yi=sinewave(x, 32)
    | summarize x=make_list(x), y_real=make_list(yr), y_imag=make_list(yi)
    | extend (fft_y_real, fft_y_imag) = series_fft(y_real, y_imag)
    | render linechart with(ysplit=panels)
    ```
    
    このクエリは *fft_y_real* と *fft_y_imag*を返します。  
    
    :::image type="content" source="images/series-fft-function/series-fft.png" alt-text="シリーズの fft" border="false":::
    
* 系列を frequency ドメインに変換し、逆変換を適用して元の系列を取得します。

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let sinewave=(x:double, period:double, gain:double=1.0, phase:double=0.0)
    {
        gain*sin(2*pi()/period*(x+phase))
    }
    ;
    let n=128;      //  signal length
    range x from 0 to n-1 step 1 | extend yr=sinewave(x, 8), yi=sinewave(x, 32)
    | summarize x=make_list(x), y_real=make_list(yr), y_imag=make_list(yi)
    | extend (fft_y_real, fft_y_imag) = series_fft(y_real, y_imag)
    | extend (y_real2, y_image2) = series_ifft(fft_y_real, fft_y_imag)
    | project-away fft_y_real, fft_y_imag   //  too many series for linechart with panels
    | render linechart with(ysplit=panels)
    ```
    
    このクエリは *y_real2* と * y_imag2 を返します。これは *y_real* および *y_imag*と同じです。  
    
    :::image type="content" source="images/series-fft-function/series-ifft.png" alt-text="シリーズ ifft" border="false":::
    