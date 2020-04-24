---
title: データ シャーディング ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのデータ シャーディング ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 6ed9cf3a1667fb57271a44dcfe7c6dd5318c4010
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520028"
---
# <a name="data-sharding-policy"></a>データシャーディングポリシー

シャーディング ポリシーは、Azure データ エクスプローラー クラスター内の[エクステント (データ シャード) を](../management/extents-overview.md)シールするかどうか、およびどのようにシールするかを定義します。

> [!NOTE]
> このポリシーは、[データ・インジェスト](../management/data-ingestion/index.md)のコマンド[、.merge コマンド、.rebuild コマンド](../management/extents-commands.md#merge-extents)など、新しいエクステントを作成するすべての操作に適用されます。

データ シャーディング ポリシーには、次のプロパティが含まれます。

- **最大行数**:
    - インジェクション操作または再作成操作によって作成されたエクステントの最大行数。
    - デフォルトは 750,000 です。
    - [マージ操作](mergepolicy.md)**には無効**です。
        - マージ操作によって作成されるエクステントの行数を制限する必要がある場合は、`RowCountUpperBoundForMerge`エンティティの[エクステント マージ ポリシー](mergepolicy.md)でプロパティを調整します。
- **次の値が表示されます**。
    - マージ操作によって作成されたエクステントの最大許容圧縮データ・サイズ (メガバイト単位)。
    - マージ**操作に対[merge](mergepolicy.md)してのみ有効です**。
    - デフォルトは 1,024 (1 GB) です。

- **最大オリジナルサイズインMb:**
    - 再構築操作で作成されたエクステントの、許可される元のデータ・サイズの最大値 (メガバイト単位)。
    - 再構築**操作に対[rebuild](mergepolicy.md)してのみ有効です**。
    - デフォルトは 2,048 (2 GB) です。

> [!WARNING]
> データ シャーディング ポリシーを変更する前に、Azure データ エクスプローラー チームに相談してください。

データベースが作成されると、既定のデータ シャーディング ポリシーが含まれます。 このポリシーは、データベースで作成されたすべてのテーブルによって継承されます (ポリシーがテーブル レベルで明示的にオーバーライドされている場合を除く)。

[シャーディング・ポリシー制御コマンド](../management/sharding-policy.md)) を使用して、データベースおよび表のデータ・シャーディング・ポリシーを管理します。
 