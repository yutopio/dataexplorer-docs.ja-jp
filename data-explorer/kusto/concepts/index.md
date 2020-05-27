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
ms.openlocfilehash: 0a56878c8b79e651afcd1b8ce18a3220dc304211
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863389"
---
# <a name="getting-started-with-kusto"></a>Kusto の概要

Azure Data Explorer は、ビッグ データに対する対話型分析を格納して実行するためのサービスです。

これは、データベース、テーブル、列などのエンティティをサポートするリレーショナル データベース管理システムをベースにしています。 複雑な分析クエリは、Kusto クエリ言語を使用して実行されます。 一部のクエリ演算子には、計算列、検索、フィルター処理、行、グループ集計、および結合が含まれます。

このサービスは、個々の行とテーブル間の制約/トランザクションのインプレース更新を実行する機能を "犠牲" にすることで、優れたデータ インジェストとクエリ パフォーマンスを提供します。 そのため、OLTP やデータ ウェアハウジングなどのシナリオでは、これによって従来の RDBMS システムが置き換えられるのではなく、取って代わられます。

構造化、半構造化 (たとえば、JSON のような入れ子にされた型)、および非構造化 (フリー テキスト) データがすべて同様に処理されます。

## <a name="interacting-with-azure-data-explorer"></a>Azure Data Explorer との対話

ユーザーが Kusto と対話するための主な方法は、使用できる多くの[クライアント ツール](../tools/index.md)のいずれかを使用することです。 [SQL クエリ](../api/tds/t-sql.md)はサポートされていますが、対話の主な手段は、[Kusto クエリ言語](../query/index.md)を使用してデータ クエリを送信する方法と、エンティティの管理やメタデータの検出などのために[管理コマンド](../management/index.md)を使用する方法があります。クエリと制御コマンドのどちらも、基本的に短いテキスト "プログラム" です。

## <a name="kusto-queries"></a>Kusto クエリ

クエリは読み取り専用の要求であり、データを処理し、その処理の結果を返します。データやメタデータが修正されることはありません。 Kusto クエリでは、[SQL 言語](../api/tds/t-sql.md)または [Kusto クエリ言語](../query/index.md)を使用できます。 後者の例として、次のクエリでは、`Level` 列の値が文字列 `Critical` と等しい `Logs` テーブル内の行の数をカウントしています。

```kusto
Logs
| where Level == "Critical"
| count
```

クエリの先頭をドット (`.`) 文字またはハッシュ (`#`) 文字にすることはできません。

## <a name="control-commands"></a>管理コマンド

制御コマンドは、データまたはメタデータを処理し、変更する可能性がある Kusto への要求です。 たとえば、次の管理コマンドでは、2 つの列 `Level` と `Text` を持つ新しい Kusto テーブルが作成されます。

```kusto
.create table Logs (Level:string, Text:string)
```

管理コマンドには独自の構文があります (これは Kusto クエリ言語構文の一部ではありませんが、2 つは多くの概念を共有しています)。 特に、コマンドのテキストの最初の文字をドット (`.`) 文字 (クエリの先頭にすることができない) にすることで、管理コマンドはクエリと区別されます。
この区別により、クエリ内での管理コマンドの埋め込みが防止されるため、さまざまな種類のセキュリティ攻撃を防ぐことができます。

すべての管理コマンドでデータまたはメタデータが変更されるわけではありません。 大きなクラスのコマンド (`.show` で始まるコマンド) は、メタデータまたはデータを表示するために使用されます。 たとえば、`.show tables` コマンドからは、現在のデータベース内のすべてのテーブルの一覧が返されます。
