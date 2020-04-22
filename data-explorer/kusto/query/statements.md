---
title: クエリ ステートメント - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクエリ ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 20eeb1aa755fcf4e3068cba061a2738a375e1847
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765568"
---
# <a name="query-statements"></a>クエリ ステートメント

::: zone pivot="azuredataexplorer"

クエリは、セミコロン ( **query statements**)`;`で区切られた 1 つ以上のクエリ ステートメントで構成されます。
これらのクエリ ステートメントの少なくとも 1 つは[、表形式の式ステートメント](./tabularexpressionstatements.md)である必要があります。
表形式の式ステートメントは、1 つ以上の表形式の結果を生成します。
クエリに複数の表形式の式ステートメントがある場合、クエリには表形式の式ステートメントの[バッチ](./batches.md)があり、これらのステートメントによって生成された表形式の結果はすべてクエリによって返されます。

2 種類のクエリ ステートメント:

* 主にユーザーが使用するステートメント ([ユーザークエリステートメント](#user-query-statements))
* 中間層アプリケーションがユーザークエリを受け取り、それらの修正バージョンを Kusto ([アプリケーション クエリ ステートメント](#application-query-statements)) に送信するシナリオをサポートするように設計されたステートメント。

クエリ ステートメントの中には、両方のシナリオで役立つものがあります。

> [!NOTE]
> クエリ ステートメントの "効果" は、クエリ内でステートメントが出現する位置から始まり、クエリの最後に終了します。 クエリが完了すると、そのすべてのリソースが解放され、今後のクエリに影響を与えなくなります (すべてのクエリのログに記録されたクエリを実行したり、結果をキャッシュしたりするなどの副作用を除く)。

## <a name="user-query-statements"></a>ユーザー クエリ ステートメント

ユーザー クエリ ステートメントの一覧を次に示します。

* [let ステートメント](./letstatement.md)は、名前と式の間のバインディングを定義します。
  Let ステートメントを使用すると、長いクエリを、わかりやすくなる小さな名前付き部分に分割できます。

* [set ステートメント](./setstatement.md)は、クエリの処理方法とその結果を変更するクエリ オプションを設定します。

* 最も重要なクエリ[ステートメントである表形式の式ステートメント](./tabularexpressionstatements.md)は、"対象" データを結果として返します。

## <a name="application-query-statements"></a>アプリケーション クエリ ステートメント

アプリケーション クエリ ステートメントの一覧を次に示します。

* [alias ステートメント](./aliasstatement.md)は、(同じクラスター内またはリモート・クラスター内の) 別のデータベースへの別名を定義します。

* Kusto の上に構築され、クエリ名解決プロセスに自分自身を挿入するためにユーザーにクエリ言語を公開するアプリケーションで使用できる[パターン ステートメント](./patternstatement.md)。

* 挿入攻撃から身を守るために Kusto の上に構築されたアプリケーションで使用される[クエリ パラメーター ステートメント](./queryparametersstatement.md)(コマンド パラメーターが SQL インジェクション攻撃から SQL を保護する方法と同様)。

* [Restrict ステートメント](./restrictstatement.md)は、Kusto の上に構築されたアプリケーションで使用され、クエリを Kusto 内の特定のデータサブセットに制限します (特定の列とレコードへのアクセスの制限を含む)。

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
