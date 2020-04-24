---
title: インジェスティションタイム ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのインジェスティションタイム ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 0c1755115a9e9f4c60c7f5574eb9b784520ecb88
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520827"
---
# <a name="ingestiontime-policy"></a>IngestionTime ポリシー

インジェストタイム ポリシーは、テーブルに設定 (有効) に設定できるオプションのポリシーです。

このポリシーがテーブルで有効になっている場合、Kusto は`datetime`非表示の列を テーブル`$IngestionTime`に追加します。 その時点以降、新しいデータがテーブルに取り込まれるたびに、取り込み時間 (データがコミットされる直前に Kusto クラスターで測定される) が、取り込まれるすべてのレコードの非表示列に記録されます。 (他の列と同様に、`$IngestionTime`すべてのレコードに固有の値があることに注意してください。

インジェスト時間列が非表示になっているため、値を直接照会することはできません。
代わりに、その値を取得するために[ingestion_time()](../query/ingestiontimefunction.md)という特別な関数が提供されます。 テーブルにそのような列がない場合、またはレコードの取り込み時に InestionTime ポリシーが有効になっていない場合は、NULL 値が返されます。

インジェスティションタイム ポリシーは、2 つの主なシナリオに合わせて設計されています。
* ユーザーがデータの取り込みでエンド ツー エンドの待機時間を見積もることができるようにします。
  ログ データを保持する多くのテーブルには、レコードが生成された時刻を示すためにソースによって値が入力されるタイムスタンプ列があります。 その列の値とインジェスタイム列を比較することで、データを取得する際の待ち時間を見積もることができます。 (ソースと Kusto は必ずしもクロックが同期されるとは限らないので、これは見積もりに過ぎないことに注意してください)。
* データベース[カーソル](../management/databasecursor.md)をサポートするために、ユーザーが連続クエリを発行し、そのたびに、前のクエリ以降に取得されたデータにクエリを制限できるようにします。



インジェスティオンタイムポリシーを管理するための制御コマンドの詳細については、[こちらをご覧ください](../management/ingestiontime-policy.md)。