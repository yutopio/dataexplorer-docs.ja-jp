---
title: コマンドとクエリ管理 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのコマンドとクエリ管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: f8c01709d69a0b439ce51b4782eb8f747db15d65
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521915"
---
# <a name="commands-and-queries-management"></a>コマンドとクエリの管理

## <a name="show-commands-and-queries"></a>.show コマンドとクエリ 

`.show``commands-and-queries`は、最終的な状態に達した管理コマンドとクエリを含むテーブルを返します。 これらのコマンドとクエリは、30 日間のクエリに使用できます。

コマンドの出力に表示される情報は[、.show コマンド](commands.md)と[.show クエリ](queries.md)で示される情報と似ていますが、基本的には両方の結果セットを簡単に結合できます。

**構文**

`.show` `commands-and-queries`
 
**出力**
 
出力スキーマは次のとおりです。

| ColumnName               | [列の型] |
|--------------------------|------------|
| クライアントアクティビティId         | string     |
| CommandType              | string     |
| Text                     | string     |
| データベース                 | string     |
| スタートンオン                | DATETIME   |
| ラストアップデートオン            | DATETIME   |
| Duration                 | TimeSpan   |
| State                    | string     |
| FailureReason            | string     |
| RootActivityId           | guid       |
| User                     | string     |
| Application              | string     |
| プリンシパル                | string     |
| クライアント要求プロパティ  | 動的    |
| 合計Cpu                 | TimeSpan   |
| メモリピーク               | long       |
| キャッシュ統計          | 動的    |
| スキャンドエクステント統計 | 動的    |
| 結果セット統計      | 動的    |

クエリの場合、の値`CommandType`は`Query`です。