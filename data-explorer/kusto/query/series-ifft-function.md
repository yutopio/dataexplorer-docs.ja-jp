---
title: series_ifft ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_ifft () 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: c5862e9c18959726f27ea7d4237058f36a408b5c
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502164"
---
# <a name="series_ifft"></a>series_ifft ()

系列に対して反転高速フーリエ変換 (IFFT) を適用します。  

Series_ifft () 関数は、frequency ドメイン内の一連の複素数を受け取り、 [高速フーリエ変換](https://en.wikipedia.org/wiki/Fast_Fourier_transform)を使用してタイム/空間ドメインに戻します。 この関数は、 [series_fft](series-fft-function.md)の補完的な機能です。 通常、元のシリーズは、スペクトル処理の場合は frequency ドメインに変換され、その後はタイム/空間ドメインに戻されます。

## <a name="syntax"></a>構文

`series_ifft(`*fft_real* [ `,` *fft_imaginary*]`)`

## <a name="arguments"></a>引数

* *fft_real*: 変換する系列の実際のコンポーネントを表す数値の動的配列。
* *fft_imaginary*: 系列の虚数部を表す同様の動的配列。 このパラメーターは省略可能です。入力系列に複雑な数値が含まれている場合にのみ指定する必要があります。

## <a name="returns"></a>戻り値

関数は、2つの系列の複雑な逆 fft を返します。 実コンポーネントの最初の系列と虚数部の2番目の系列。

## <a name="example"></a>例

「」を参照してください [series_fft](series-fft-function.md#example)
