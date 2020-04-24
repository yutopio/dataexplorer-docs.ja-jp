---
title: ランナウェイ クエリ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのランナウェイ クエリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 076afea64cf3c4405f37e9e4014584b90d58ca07
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522986"
---
# <a name="runaway-queries"></a>ランナウェイ クエリ

*ランナウェイ クエリ*は、クエリの実行中に内部クエリの制限を超えたときに発生する[部分的な](partialqueryfailures.md)[クエリ](querylimits.md)エラーの一種です。

たとえば、次のエラーが報告される可能性があります。`HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete.`

このような場合、いくつかの可能なアクション コースがあります。
* 使用するリソースを減らすには、クエリを変更します。 たとえば、クエリの結果セットが大きすぎることがエラーで示されている場合は、クエリによって返されるレコードの数を制限するか[(take 演算子](../query/takeoperator.md)を使用するか[、または WHERE 句](../query/whereoperator.md)を追加するか)、クエリによって返される列数を減らしたり ([プロジェクト演算子](../query/projectoperator.md)または[プロジェクト アウェイ演算子](../query/projectawayoperator.md)を使用して)、[集計](../query/summarizeoperator.md)データを取得したりできます。
* そのクエリに対して関連するクエリの制限を一時的に増やします ([クエリ制限](querylimits.md)の下にある**結果セットの反復器ごとの最大メモリ**量を参照)。  
  これは、クラスターを保護するための制限が存在するため、1 つのクエリがクラスターで実行される同時実行クエリを中断しないようにするため、一般的には推奨されないことに注意してください。
  
  
  