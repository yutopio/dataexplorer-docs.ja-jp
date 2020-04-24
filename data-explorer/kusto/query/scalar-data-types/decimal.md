---
title: 10 進データ型 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの 10 進データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 62fd745c804ad4a50652e09cd722c17a4a61daac
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509896"
---
# <a name="the-decimal-data-type"></a>10 進データ型

データ`decimal`型は、128 ビット幅の 10 進数を表します。

データ型の`decimal`リテラルは、 と同じ表現です。ネットの`System.Data.SqlTypes.SqlDecimal`.

`decimal(1.0)`、、`decimal(0.1)`および`decimal(1e5)`は、すべての型のリテラル`decimal`です。

いくつかの特殊なリテラル形式があります。
* `decimal(null)`: は[null 値](null-values.md)です。