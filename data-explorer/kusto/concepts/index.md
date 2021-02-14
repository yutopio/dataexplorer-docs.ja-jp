---
title: Kusto の概要
description: この記事では、Kusto の概要について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: 5bf3e67439f9903d3d17830e92b1d7b2efbbbec1
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359626"
---
# <a name="getting-started-with-kusto"></a>Kusto の概要

Azure Data Explorer は、ビッグ データに対する対話型分析を格納して実行するためのサービスです。

これはリレーショナル データベース管理システムをベースにしており、データベース、テーブル、列などのエンティティをサポートしています。 複雑な分析クエリは、Kusto クエリ言語を使用して作成されます。 クエリ演算子には次のようなものがあります。
* 計算列
* 行の検索とフィルター処理
* グループ化 - 集計
* 結合関数

このサービスは、優れたデータ インジェストとクエリ パフォーマンスを提供します。 
* これにより、個々の行とテーブル間の制約またはトランザクションのインプレース更新を実行する能力が犠牲になります。 
* このサービスは、OLTP やデータ ウェアハウジングなどのシナリオで、従来の RDBMS システムを置き換えるのではなく、補完します。
* 構造化データ、半構造化データ (たとえば、JSON に似た入れ子にされた型) や非構造化データ (フリーテキスト) のいずれも同様に適切に処理されます。

## <a name="interacting-with-azure-data-explorer"></a>Azure Data Explorer との対話

ユーザーが Azure Data Explorer (Kusto) と対話するための主な方法:
* [クエリ ツール](../../tools-integrations-overview.md#azure-data-explorer-query-tools)のいずれかを使用します。 
* [SQL クエリ](../api/tds/t-sql.md)。
*  [Kusto クエリ言語](../query/index.md)は、対話の主な手段です。 KQL を使用すると、データ クエリを送信したり、[制御コマンド](../management/index.md)を使用してエンティティを管理したり、メタデータを検出したりすることができます。
クエリと制御コマンドは、どちらも短いテキスト "プログラム" です。

## <a name="kusto-queries"></a>Kusto クエリ

クエリは読み取り専用の要求であり、データを処理し、その処理の結果を返します。データやメタデータが修正されることはありません。 Kusto クエリでは、[SQL 言語](../api/tds/t-sql.md)または [Kusto クエリ言語](../query/index.md)を使用できます。 後者の例として、次のクエリでは、`Logs` テーブルの行のうち、`Level` 列の値が文字列 `Critical` と等しいものをカウントします。

```kusto
Logs
| where Level == "Critical"
| count
```

> [!NOTE]
> クエリは、ドット (`.`) 文字またはハッシュ (`#`) 文字で始めることはできません。

## <a name="control-commands"></a>管理コマンド

制御コマンドは、データまたはメタデータを処理し、変更する可能性がある Kusto への要求です。 たとえば、次の管理コマンドでは、2 つの列 `Level` と `Text` を持つ新しい Kusto テーブルが作成されます。

```kusto
.create table Logs (Level:string, Text:string)
```

管理コマンドには独自の構文があり、これは Kusto クエリ言語構文に含まれていませんが、この 2 つは多くの概念を共有しています。 特に、コマンドのテキストの最初の文字をドット (`.`) 文字 (クエリの先頭にすることができない) にすることで、管理コマンドはクエリと区別されます。
この区別により、クエリ内での管理コマンドの埋め込みが防止されるため、さまざまな種類のセキュリティ攻撃を防ぐことができます。

すべての管理コマンドでデータまたはメタデータが変更されるわけではありません。 `.show` で始まる大きなクラスのコマンドは、メタデータまたはデータを表示するために使用されます。 たとえば、`.show tables` コマンドからは、現在のデータベース内のすべてのテーブルの一覧が返されます。
