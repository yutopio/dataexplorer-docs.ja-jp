---
title: ユーザー分析-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのユーザー分析について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/17/2019
ms.openlocfilehash: a63f68564bc0e2fec8b6e2d5faa12e50b7adcc7c
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741653"
---
# <a name="user-analytics"></a>ユーザー分析

このセクションでは、ユーザー分析シナリオ用の Kusto 拡張機能 (プラグイン) について説明します。

|シナリオ|プラグイン|詳細|ユーザーの作業|
|--------|------|--------|-------|
| 時間の経過に伴う新規ユーザーのカウント | [activity_counts_metrics](activity-counts-metrics-plugin.md)|各時間枠の数/dcounts/新しいカウントを返します。 各時間枠は、以前の*すべて*の時間枠と比較されます。|Kusto. エクスプローラー: レポートギャラリー|
| 期間 (期間): 保有期間/チャーン率と新しいユーザー | [activity_metrics](activity-metrics-plugin.md)|各`dcount`時間枠のリテンション期間またはチャーン率を返します。 各時間枠は*前*の時間枠と比較されます|Kusto. エクスプローラー: レポートギャラリー|
| ユーザー数と`dcount`スライド式ウィンドウ | [sliding_window_counts](sliding-window-counts-plugin.md)|時間枠ごとに、スライド式ウィンドウ`dcount`のように、カウントを返します。|
| 新規ユーザー cohort: 保有期間/チャーン率と新しいユーザー | [new_activity_metrics](new-activity-metrics-plugin.md)|新しいユーザーのコーホート (時間枠で最初に表示されたすべてのユーザー) を比較します。 各コーホートは、前のすべてのコーホートと比較されます。 比較では、以前の*すべて*の時間帯が考慮される|Kusto. エクスプローラー: レポートギャラリー|
|アクティブユーザー数: 個別のカウント |[active_users_count](active-users-count-plugin.md)|時間枠ごとに個別のユーザーを返します。 ユーザーは、指定されたルックバック期間内に少なくとも X 個の個別の期間に表示されている場合にのみ考慮されます。|
|ユーザーエンゲージメント: DAU/WAU/MAU|[activity_engagement](activity-engagement-plugin.md)|内部の時間枠 (たとえば、毎日) と、コンピューティングエンゲージメントの外部 (たとえば、毎週) を比較します (たとえば、DAU/WAU)。|Kusto. エクスプローラー: レポートギャラリー|
|セッション: アクティブなセッション数のカウント|[session_count](session-count-plugin.md)|セッションをカウントします。セッションは期間によって定義されます。ユーザーレコードは、現在のレコードからの元の場所にない場合、新しいセッションと見なされます。|
||||
|じょうご: 前の状態と次の状態のシーケンス分析 | [funnel_sequence](funnel-sequence-plugin.md)|一連のイベントを取得した個別のユーザーと、その前後に発生した、またはシーケンスが最後に発生した前後のイベントをカウントします。 [Sankey ダイアグラム](https://en.wikipedia.org/wiki/Sankey_diagram)の構築に役立ちます||
|じょうご: シーケンス完了の分析|[funnel_sequence_completion](funnel-sequence-completion-plugin.md)|各時間枠で指定されたシーケンスを*完了*したユーザーの個別のカウントを計算します。|
||||