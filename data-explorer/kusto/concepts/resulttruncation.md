---
title: Kusto クエリの結果セットが内部制限を超えています-Azure データエクスプローラー
description: この記事では、クエリ結果セットが内部...Azure データエクスプローラーの制限。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 9706cce08a99989441aa657c5d85465294be837e
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512420"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>クエリ結果セットが内部...制限

*クエリの結果セットが内部...limit*は、クエリの結果が次の2つの制限のいずれかを超えた場合に発生する[部分的なクエリエラー](partialqueryfailures.md)の一種です。
* レコード数の制限 ( `record count limit` 、既定で50万に設定)
* データの合計量に対する制限 ( `data size limit` 、既定では 67108864 (64 mb) に設定)

アクションには、次のようないくつかのコースがあります。

* 使用するリソースが少なくなるようにクエリを変更します。 
  たとえば、次のように操作できます。
  * [Take 演算子](../query/takeoperator.md)を使用するか、またはその他の[where 句](../query/whereoperator.md)を追加して、クエリによって返されるレコードの数を制限します。
  * クエリによって返される列の数を減らしてみてください。 [Project 演算子](../query/projectoperator.md)を使用するか、または[プロジェクトの移動演算子](../query/projectawayoperator.md)を使用します)
  * 集計[演算子](../query/summarizeoperator.md)を使用して集計データを取得する
* クエリの関連するクエリ制限を一時的に増やします。 詳細については、「[クエリの制限](querylimits.md)」の「**結果の切り捨て**」を参照してください)。

 > [!NOTE] 
 > クラスターを保護するための制限があるため、クエリの制限を増やすことはお勧めしません。 制限により、1つのクエリがクラスターで実行されている同時実行クエリを中断しないようにします。
  
