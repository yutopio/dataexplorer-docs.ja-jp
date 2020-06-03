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
ms.openlocfilehash: c7f692739071496ce492d168c6036a2c2adac8fd
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329047"
---
# <a name="commands-and-queries-management"></a>コマンドとクエリの管理

## <a name="show-commands-and-queries"></a>。コマンドとクエリを表示します。 

`.show``commands-and-queries`管理コマンドと、最終状態に達したクエリを含むテーブルを返します。 これらのコマンドとクエリは30日間使用できます。

コマンドの出力に表示される情報は、「[コマンドの表示](commands.md)」と「」に似ています[が、基本的](queries.md)には、両方の結果セットを単純な方法で結合できます。

**構文**

`.show` `commands-and-queries`
 
**出力**
 
出力スキーマは次のとおりです。

| ColumnName               | [列の型] |
|--------------------------|------------|
| ClientActivityId         | string     |
| CommandType              | string     |
| Text                     | string     |
| データベース                 | string     |
| StartedOn                | DATETIME   |
| LastUpdatedOn            | DATETIME   |
| Duration                 | TimeSpan   |
| State                    | string     |
| FailureReason            | string     |
| RootActivityId           | guid       |
| ユーザー                     | string     |
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
