---
title: materialized_view () (スコープ関数)-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの materialized_view () 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: e8dee5e1c33328eb70f984b20f61d4585abae550
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057189"
---
# <a name="materialized_view-function"></a>materialized_view () 関数

具体化された [ビュー](../management/materialized-views/materialized-view-overview.md)の具体化された部分を参照します。 

関数は、 `materialized_view()` ビューの *具体化* された部分に対してのみクエリを実行し、ユーザーが許容できる最大待機時間を指定する方法をサポートしています。 このオプションは、最新のレコードを返すことは保証されていませんが、常にビュー全体を照会するよりもパフォーマンスが高くなります。 この機能は、テレメトリダッシュボードなど、パフォーマンスのために鮮度を犠牲にする場合に便利です。

<!--- csl --->
```
materialized_view('ViewName')
```

## <a name="syntax"></a>構文

`materialized_view(`*ViewName* `,`[*max_age*]`)`

## <a name="arguments"></a>引数

* *ViewName*: の名前 `materialized view` 。
* *max_age*: 省略可能。 指定しない場合は、ビューの *具体化* された部分のみが返されます。 指定した場合、最後の具体化時間が [-max_age] よりも大きい場合、関数はビューの _具体化_ された部分を返し @now ます。 それ以外の場合は、ビュー全体が返されます ( *ViewName* を直接照会する場合と同じです)。 

## <a name="examples"></a>例

ビューの *具体化* された部分のみにクエリを実行し、最後に具体化されたときはに依存しないようにします。

<!-- csl -->
```
materialized_view("ViewName")
```

最後の10分間に具体化された場合にのみ、 *具体化* されたパーツに対してクエリを実行します。 具体化された部分が10分よりも前の場合は、完全なビューを返します。 このオプションは、具体化された部分をクエリするよりもパフォーマンスが低下することが予想されます。

<!-- csl -->
```
materialized_view("ViewName", 10m)
```

## <a name="notes"></a>Notes

* ビューを作成すると、データベース内の他のテーブルと同じようにクエリを実行できます。これには、クラスター間またはデータベース間クエリへの参加も含まれます。
* 具体化されたビューは、ワイルドカードの共用体または検索には含まれません。
* ビューにクエリを実行するための構文は、ビュー名です (テーブル参照など)。
* 具体化されたビューに対してクエリを実行すると、ソーステーブルに取り込まれたしたすべてのレコードに基づいて、常に最新の結果が返されます。 このクエリでは、ビューの具体化された部分と、ソーステーブル内の具体化されていないすべてのレコードが結合されます。 詳細については、「 [バックグラウンド](../management/materialized-views/materialized-view-overview.md#how-materialized-views-work) での詳細」を参照してください。
