---
title: 実際のデータ型 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの実際のデータ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: f69b74670fefcff6f6c24d5235f58beb98b0405a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509675"
---
# <a name="the-real-data-type"></a>実際のデータ型

データ`real`型は、64 ビット幅の倍精度浮動小数点数を表します。

データ型の`real`リテラルは、 と同じ表現です。ネットの`System.Double`. `1.0`、、`0.1`および`1e5`は、すべての型のリテラル`real`です。

いくつかの特殊なリテラル形式があります。
* `real(null)`: は[null 値](null-values.md)です。
* `real(nan)`: 番号ではない (NaN)。 たとえば、 を 別`0.0``0.0`の で除算した結果です。
* `real(+inf)`: 正の無限大。 たとえば、 で`1.0``0.0`除算した結果などです。
* `real(-inf)`: 負の無限大。 たとえば、 で`-1.0``0.0`除算した結果などです。