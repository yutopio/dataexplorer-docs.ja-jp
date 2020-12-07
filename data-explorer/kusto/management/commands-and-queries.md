---
title: コマンドとクエリの管理-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのコマンドとクエリの管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: 222d04939560342bd849c15f249b1a8a582316a2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321031"
---
# <a name="commands-and-queries-management"></a>コマンドとクエリの管理

## <a name="show-commands-and-queries"></a>。コマンドとクエリを表示します。 

`.show``commands-and-queries`管理コマンドと、最終状態に達したクエリを含むテーブルを返します。 これらのコマンドとクエリは30日間使用できます。

コマンドの出力に表示される情報は、 [ `.show` コマンド](commands.md)と[ `.show` クエリ](queries.md)に似ていますが、基本的には、両方の結果セットを簡単な方法で結合できます。

**構文**

`.show` `commands-and-queries`
 
**出力**
 
出力スキーマは次のとおりです。

| ColumnName               | [列の型] |
|--------------------------|------------|
| ClientActivityId         | string     |
| CommandType              | string     |
| テキスト                     | string     |
| データベース                 | string     |
| StartedOn                | DATETIME   |
| LastUpdatedOn            | DATETIME   |
| Duration                 | TimeSpan   |
| State                    | string     |
| FailureReason            | string     |
| RootActivityId           | guid       |
| User                     | string     |
| Application              | string     |
| プリンシパル                | string     |
| ClientRequestProperties  | 動的    |
| TotalCpu                 | TimeSpan   |
| MemoryPeak               | long       |
| CacheStatistics          | 動的    |
| ScannedExtentsStatistics | 動的    |
| ResultSetStatistics      | 動的    |

> [!NOTE]
> クエリの場合、の値 `CommandType` はに `Query` なります。
