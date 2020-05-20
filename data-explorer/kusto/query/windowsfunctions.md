---
title: ウィンドウ関数-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのウィンドウ関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: d876f26de796008e83b620e4511a31cdb4e23888
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550692"
---
# <a name="window-functions"></a>ウィンドウ関数

ウィンドウ関数は、一度に複数の行 (レコード) を行セットで操作します。 集計関数とは異なり、ウィンドウ関数では、行セット内の行をシリアル化する必要があります (特定の順序を指定できます)。 ウィンドウ関数は、結果を判断するために順序に依存する場合があります。

ウィンドウ関数は、シリアル化されたセットでのみ使用できます。 行セットをシリアル化する最も簡単な方法は、 [serialize 演算子](./serializeoperator.md)を使用することです。 この演算子は、任意の方法で行の順序を "凍結" します。 シリアル化された行の順序が意味的に重要である場合は、 [sort 演算子](./sortoperator.md)を使用して特定の順序を強制します。

シリアル化プロセスには、それに関連する重要ではないコストがあります。 たとえば、多くのシナリオでは、クエリの並列化が妨げられる可能性があります。 したがって、シリアル化を不必要に適用しないでください。 必要に応じて、可能な限り最小の行セットでシリアル化を実行するようにクエリを再配置します。

## <a name="serialized-row-set"></a>シリアル化された行セット

任意の行セット (テーブルや表形式演算子の出力など) は、次のいずれかの方法でシリアル化できます。

1. 行セットを並べ替える。 並べ替えられた行セットを生成する演算子の一覧については、以下を参照してください。
2. [Serialize 演算子](./serializeoperator.md)を使用します。

多くの表形式演算子は、入力が既にシリアル化されている場合でも、結果がシリアル化されることを保証していない場合でも、出力をシリアル化します。 たとえば、このプロパティは、[拡張演算子](./extendoperator.md)、[プロジェクト演算子](./projectoperator.md)、および[where 演算子](./whereoperator.md)に対して保証されます。

## <a name="operators-that-emit-serialized-row-sets-by-sorting"></a>並べ替えによってシリアル化された行セットを出力する演算子

* [order 演算子](./orderoperator.md)
* [sort 演算子](./sortoperator.md)
* [top 演算子](./topoperator.md)
* [top-hitters 演算子](./tophittersoperator.md)
* [top-nested 演算子](./topnestedoperator.md)

## <a name="operators-that-preserve-the-serialized-row-set-property"></a>シリアル化された行セットプロパティを保持する演算子

* [extend 演算子](./extendoperator.md)
* [mv-expand 演算子](./mvexpandoperator.md)
* [parse 演算子](./parseoperator.md)
* [project 演算子](./projectoperator.md)
* [project-away 演算子](./projectawayoperator.md)
* [project-rename 演算子](./projectrenameoperator.md)
* [take 演算子](./takeoperator.md)
* [where 演算子](./whereoperator.md)
