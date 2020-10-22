---
title: ランナウェイクエリ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのランナウェイクエリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4f2cf559863eecbd267855cd4a22f2dbe8673dc7
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356607"
---
# <a name="runaway-queries"></a>ランナウェイ クエリ

*ランナウェイクエリ*は、クエリの実行中に内部[クエリの制限](querylimits.md)を超えた場合に発生する、[部分的なクエリエラー](partialqueryfailures.md)の一種です。 

たとえば、次のエラーが報告される場合があります。 `HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete.`

いくつかのアクションが考えられます。
* 使用するリソースが少なくなるようにクエリを変更します。 たとえば、クエリの結果セットが大きすぎることがエラーによって示された場合、次のことが可能です。
  * クエリによって返されるレコードの数を制限する
     * [Take 演算子](../query/takeoperator.md)を使用する
     * その他の[where 句](../query/whereoperator.md)の追加
  * によってクエリによって返される列の数を減らします。
     * [Project 演算子](../query/projectoperator.md)の使用
     * プロジェクト外の[オペレーター](../query/projectawayoperator.md)の使用
     * [Project-keep 演算子](../query/project-keep-operator.md)を使用する
  * 集計 [演算子](../query/summarizeoperator.md) を使用すると、集計データを取得できます。
* クエリの関連するクエリ制限を一時的に増やします。 詳細については、「 [クエリの制限-反復子ごとのメモリの制限](querylimits.md)」を参照してください。 ただし、この方法は推奨されません。 クラスターを保護し、1つのクエリがクラスターで実行されている同時実行クエリを中断しないようにするには、制限があります。
