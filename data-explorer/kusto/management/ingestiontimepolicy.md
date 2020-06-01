---
title: IngestionTime ポリシー-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの IngestionTime ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 50e0083b1cdbed06106507fe69fb0d039c923c43
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257809"
---
# <a name="ingestiontime-policy"></a>IngestionTime ポリシー

IngestionTime ポリシーは、テーブルで設定 (有効) できるオプションのポリシーです。

有効な場合、Kusto はという名前の非表示の `datetime` 列をテーブルに追加し `$IngestionTime` ます。 これで、新しいデータが取り込まれたされるたびに、インジェストの時間が非表示の列に記録されます。 この時間は、データがコミットされる直前に Kusto クラスターによって測定されます。 

> [!NOTE]
> すべてのレコードには独自の値があり `$IngestionTime` ます。

インジェスト time 列は非表示になっているため、その値に対して直接クエリを実行することはできません。
代わりに、 [ingestion_time ()](../query/ingestiontimefunction.md)と呼ばれる特殊な関数は、その値を取得します。 `datetime`テーブルに列がない場合、またはレコードが取り込まれたされたときに IngestionTime ポリシーが有効になっていない場合は、null 値が返されます。

IngestionTime ポリシーは、主に次の2つのシナリオ向けに設計されています。
* ユーザーが取り込みデータの待機時間を見積もることができるようにします。
  ログデータを含む多数のテーブルに timestamp 列があります。 タイムスタンプ値はソースによって取得され、レコードが生成された時刻を示します。 この列の値とインジェスト時間列を比較することによって、でデータを取得する際の待機時間を見積もることができます。 
  
  > [!NOTE]
  > 計算値は推定値です。ソースと Kusto のクロックが同期されているとは限らないためです。
  
* ユーザーが連続したクエリを実行できるようにする[データベースカーソル](../management/databasecursor.md)をサポートするために、クエリは、前のクエリから取り込まれたされたデータに制限されます。

詳しくは、 [IngestionTime ポリシーを管理するための制御コマンド](../management/ingestiontime-policy.md)を参照してください。
