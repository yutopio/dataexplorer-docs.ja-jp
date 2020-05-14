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
ms.openlocfilehash: 41a9851405ff85210bba7728a3737fc3b0a57f70
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382388"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>クエリ結果セットが内部...制限

*クエリの結果セットが内部...limit*は、クエリの結果が次の2つの制限のいずれかを超えた場合に発生する[部分的なクエリエラー](partialqueryfailures.md)の一種です。
* レコード数の制限 ( `record count limit` 既定では50万に設定されます)。
* データの合計量に対する制限 ( `data size limit` 、既定では 67108864 (64 mb) に設定されます)。 

このような場合には、いくつかの対処方法があります。
* 使用するリソースが少なくなるようにクエリを変更します。 たとえば、クエリによって返されるレコードの数を制限する ( [take 演算子](../query/takeoperator.md)を使用するか、さらに[where 句](../query/whereoperator.md)を追加する) ことができます。または、クエリによって返される列の数 ([プロジェクト演算子](../query/projectoperator.md)またはプロジェクト外の[演算子](../query/projectawayoperator.md)を使用) を減らすか、[集計演算子](../query/summarizeoperator.md)を使用して集計データを取得することができます。
* クエリの関連するクエリ制限を一時的に増やします (「[クエリの制限](querylimits.md)」の「**結果の切り捨て**」を参照してください)。  
  クラスターを保護するために制限が設けられているため、クラスターで実行されている同時実行クエリが1つのクエリによって中断されないようにするため、この方法は一般的にはお勧めしません。