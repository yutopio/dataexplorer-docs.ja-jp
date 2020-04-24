---
title: ユーザー分析 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのユーザー分析について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/17/2019
ms.openlocfilehash: 37b5962b9441c3eb7362a239404189e489b1c3ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504983"
---
# <a name="user-analytics"></a>ユーザー分析

このセクションでは、ユーザー分析シナリオの Kusto エクステント (プラグイン) について説明します。

|シナリオ|プラグイン|詳細|ユーザー エクスペリエンス|
|--------|------|--------|-------|
| 時間経過に合った新規ユーザーのカウント | [activity_counts_metrics](activity-counts-metrics-plugin.md)|各タイム ウィンドウのカウント/dcounts/新しいカウントを返します。 各タイム ウィンドウは、*以前のすべての*タイム ウィンドウと比較されます。|クスト・エクスプローラー: レポートギャラリー|
| 期間:保持/解約率と新規ユーザー | [activity_metrics](activity-metrics-plugin.md)|各タイム ウィンドウの dcounts、保持/終了率を返します。 各タイム ウィンドウは *、前*のタイム ウィンドウと比較されます。|クスト・エクスプローラー: レポートギャラリー|
| ユーザーはスライディングウィンドウでカウントとカウント | [sliding_window_counts](sliding-window-counts-plugin.md)|各タイム ウィンドウについて、スライディング ウィンドウ方式でルックバック期間のカウントとカウントを返します。|
| 新規ユーザーのコホート: 保持/チャーン率と新規ユーザー | [new_activity_metrics](new-activity-metrics-plugin.md)|新しいユーザー (タイム ウィンドウで 1 番目に表示されたすべてのユーザー) のコホート間で比較します。 各コホートは、すべての以前のコホートと比較されます。 比較では、*以前のすべての*時間枠が考慮されます|クスト・エクスプローラー: レポートギャラリー|
|アクティブユーザー: 個別のカウント |[active_users_count](active-users-count-plugin.md)|各時間枠の個別のユーザーを返しますが、ユーザーは、spcified ルックバック期間内の少なくとも X の異なる期間に表示される場合にのみ考慮されます。|
|ユーザーエンゲージメント: DAU/WAU/MAU|[activity_engagement](activity-engagement-plugin.md)|コンピューティングエンゲージメント(例えば、DAU/WAU)の内部時間枠(例えば、毎日)とアウター(例えば、毎週)の間で比較します。|クスト・エクスプローラー: レポートギャラリー|
|セッション: アクティブセッションのカウント|[session_count](session-count-plugin.md)|セッションが期間によって定義されるセッションをカウントします - 現在のレコードからのルックバック期間に表示されていない場合、ユーザー レコードは新しいセッションと見なされます。|
||||
|ファネル: 前と次の状態シーケンス分析 | [funnel_sequence](funnel-sequence-plugin.md)|一連のイベントを受け取った個別のユーザーをカウントし、その後に続く / の順序を導いた前/次のイベントをカウントします。 [サンキー図](https://en.wikipedia.org/wiki/Sankey_diagram)の構築に便利||
|漏斗:シーケンス完了解析|[funnel_sequence_completion](funnel-sequence-completion-plugin.md)|各タイム ウィンドウで指定されたシーケンスを*完了*したユーザーの個別の数を計算します。|
||||