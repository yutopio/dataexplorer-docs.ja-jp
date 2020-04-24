---
title: 長いデータ型 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの長いデータ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b8c51f216b8515e483daf64d9783210ff35ceef3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509709"
---
# <a name="the-long-data-type"></a>長いデータ型

データ`long`型は、符号付きの 64 ビット幅の整数を表します。

## <a name="long-literals"></a>長いリテラル

データ型のリテラル`long`は、次の構文で指定できます。

`long` `(` *値* `)`

ここで *、値*は次の形式を取ることができます。
* 1 桁以上の数字で、その場合、リテラル値はこれらの数字の 10 進数表現です。 たとえば、`long(12)`型の 12 の`long`数です。
* 接頭`0x`辞の後に 1 桁以上の 16 進数の数字を付けます。 たとえば、`long(0xf)` は、`long(15)` と同じです。
* マイナス記号`-`() の後に 1 つ以上の数字が続きます。 たとえば、`long(-1)`数値から 1 つの型を`long`引いた値を指定します。
* `null`この場合、データ型の`long` [null 値](null-values.md)になります。 したがって、型`long`の null 値`long(null)`は です。

Kusto は、最初の`long`2`long(`/`)`つのフォームに対してのみプレフィックス/suffi を使用しない型のリテラルもサポートしています。 したがって、`123``long`型`0x123`のリテラルは そのままですが`-2`、リテラル**ではありません**(現在は long 型のリテラル`-``2`に適用される単項関数として解釈されます)。
 
long を 16 進文字列に変換する場合は[、tohex() 関数を](../tohexfunction.md)参照してください。