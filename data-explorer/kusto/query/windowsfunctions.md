---
title: ウィンドウ関数 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのウィンドウ関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: ab4f6da2478ba4de81b2034c1cb07458daa80bd0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504252"
---
# <a name="window-functions"></a>ウィンドウ関数

ウィンドウ関数は、一度に 1 つの行セット内の複数の行 (レコード) を操作します。
集計関数とは異なり、ウィンドウ関数は結果を決定する順序に依存する可能性があるため、行セットの行を**シリアル化**する必要があります (特定の順序を持つ)。

ウィンドウ関数は、シリアル化されていない行セットでは使用できず、クエリによってこのようなコンテキストで使用するとエラーになります。 行セットをシリアル化する最も簡単な方法は、[シリアル化演算子](./serializeoperator.md)を使用することです。
シリアル化された行の順序が意味的に重要な場合は、[並べ替え演算子](./sortoperator.md)を使用して特定の順序を強制できます。

シリアル化プロセスには、単純なコストが関連付けられます。 たとえば、多くのシナリオでクエリの並列処理を防ぐことができます。 したがって、シリアル化を不必要に適用せず、必要に応じてクエリを並べ替え、可能な限り最小の行セットに対してシリアル化を実行することを強くお勧めします。

## <a name="serialized-row-set"></a>シリアル化された行セット

任意の行セット (テーブルや表形式の演算子の出力など) は、次のいずれかの方法でシリアル化できます。

1. 行セットを並べ替えます。 ソートされた行セットを出力する演算子のリストについては、以下を参照してください。
2. [シリアル化演算子](./serializeoperator.md)を使用します。

多くの表形式演算子は、それ自体では結果がシリアル化されることを保証するものではありませんが、入力がシリアル化される場合は出力も同じであるというプロパティを持っています。 たとえば、このプロパティは[、拡張演算子](./extendoperator.md)、[プロジェクト演算子](./projectoperator.md)、および[where 演算子](./whereoperator.md)に対して保証されます。

## <a name="operators-that-emit-serialized-row-sets-by-sorting"></a>並べ替えによってシリアル化された行セットを出力する演算子

* [order 演算子](./orderoperator.md)
* [sort 演算子](./sortoperator.md)
* [トップ演算子](./topoperator.md)
* [top-hitters 演算子](./tophittersoperator.md)
* [top-nested 演算子](./topnestedoperator.md)

## <a name="operators-that-preserve-the-serialized-row-set-property"></a>シリアル化された行セット プロパティを保持する演算子

* [extend 演算子](./extendoperator.md)
* [mv-expand 演算子](./mvexpandoperator.md)
* [parse 演算子](./parseoperator.md)
* [project 演算子](./projectoperator.md)
* [project-away 演算子](./projectawayoperator.md)
* [project-rename 演算子](./projectrenameoperator.md)
* [take 演算子](./takeoperator.md)
* [ここで演算子](./whereoperator.md)