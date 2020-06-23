---
title: クエリステートメント-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのクエリステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 778b8c276d6264b890c0b0be7b127f578663a64d
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85129058"
---
# <a name="query-statement-types"></a>クエリ ステートメントの種類

::: zone pivot="azuredataexplorer"

クエリは、セミコロン () で区切られた1つ以上の**クエリステートメント**で構成さ `;` れます。
これらのクエリステートメントの少なくとも1つは、[表形式の式ステートメント](./tabularexpressionstatements.md)である必要があります。
表形式の式ステートメントは、1つまたは複数の表形式の結果を生成します。
クエリに複数の表形式式ステートメントがある場合、クエリにはテーブル式ステートメントの[バッチ](./batches.md)が含まれ、これらのステートメントによって生成される表形式の結果はすべてクエリによって返されます。

クエリステートメントには、次の2種類があります。

* ユーザーが主に使用するステートメント ([ユーザークエリステートメント](#user-query-statements))
* 中間層アプリケーションがユーザークエリを受け取り、変更されたバージョンを Kusto に送信するシナリオをサポートするように設計されたステートメント ([アプリケーションクエリステートメント](#application-query-statements))。

クエリステートメントの中には、両方のシナリオで役に立つものがあります。

> [!NOTE]
> クエリステートメントの "効果" は、クエリ内でステートメントが出現する位置から開始し、クエリの末尾で終了します。 クエリが完了すると、すべてのリソースが解放され、将来のクエリには影響しません (クエリがすべてのクエリのログに記録されている場合や、結果がキャッシュされている場合など、副作用はありません)。

## <a name="user-query-statements"></a>ユーザークエリステートメント

ユーザークエリステートメントの一覧を次に示します。

* [Let ステートメント](./letstatement.md)では、名前と式の間のバインドを定義します。
  ステートメントを使用して、長いクエリをわかりやすい小さな名前付きパーツに分割できます。

* [Set ステートメント](./setstatement.md)では、クエリの処理方法と結果が返される方法に影響するクエリオプションを設定します。

* 最も重要なクエリステートメントである[テーブル式ステートメント](./tabularexpressionstatements.md)は、"興味深い" データを結果として返します。

## <a name="application-query-statements"></a>アプリケーションクエリステートメント

アプリケーションクエリステートメントの一覧を次に示します。

* [別名ステートメント](./aliasstatement.md)では、別のデータベース (同じクラスター内またはリモートクラスター内) への別名が定義されています。

* [Pattern ステートメント](./patternstatement.md)。 Kusto 上に構築されたアプリケーションによって使用され、クエリ言語をユーザーに公開してクエリ名の解決プロセスに挿入することができます。

* [クエリパラメーターステートメント](./queryparametersstatement.md)。 Kusto 上に構築されたアプリケーションが、注入攻撃から保護するために使用されます (コマンドパラメーターが sql インジェクション攻撃に対して sql を保護する方法と似ています)。

* [Restrict ステートメント](./restrictstatement.md)。 kusto 上に構築されたアプリケーションによって使用され、Kusto の特定のデータのサブセット (特定の列とレコードへのアクセスの制限を含む) に対するクエリを制限します。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
