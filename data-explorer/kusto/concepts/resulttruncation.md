---
title: クエリ結果セットが内部 .制限 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この資料では、クエリの結果セットが内部 .は、Azure データ エクスプローラーで制限されます。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: efb07e68922a88e2cf59ef1eefeabd3af085aed4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523020"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>クエリ結果セットが内部 .制限

*クエリ結果セットが内部 .limit*は、クエリの結果が次の 2 つの制限のいずれかを超えたときに発生する[部分的なクエリ エラー](partialqueryfailures.md)の一種です。
* レコード数の制限 (`record count limit`既定で 500,000 に設定されます)。
* データの合計量の制限 (`data size limit`既定で 67,108,864 (64MB) に設定されます。 

このような場合、いくつかの可能なアクション コースがあります。
* 使用するリソースを減らすには、クエリを変更します。 たとえば、クエリによって返されるレコードの数を制限したり[(take 演算子](../query/takeoperator.md)を使用したり[、where 句](../query/whereoperator.md)を追加したり)、クエリによって返される列の数を減らしたり ([プロジェクト演算子](../query/projectoperator.md)またはプロジェクト[アウェイ演算子](../query/projectawayoperator.md)を使用して)、[集計](../query/summarizeoperator.md)データを取得したりできます。
* そのクエリに対して関連するクエリの制限を一時的に増やします ([クエリ制限](querylimits.md)の結果**の切り捨て**を参照)。  
  これは、クラスターを保護するための制限が存在するため、1 つのクエリがクラスターで実行される同時実行クエリを中断しないようにするため、一般的には推奨されないことに注意してください。