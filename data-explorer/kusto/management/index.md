---
title: 管理 (制御コマンド) の概要 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer での管理 (制御コマンド) の概要について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7a0a2384409f4b1c7e55231d8bf7b68c44ab8a6d
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303256"
---
# <a name="management-control-commands-overview"></a>管理 (制御コマンド) の概要

このセクションでは、Kusto の管理に使用する制御コマンドについて説明します。
制御コマンドは、データベース テーブル内のデータとは限らない情報を取得したり、サービスの状態を変更したりするためのサービスへの要求です。

## <a name="differentiating-control-commands-from-queries"></a>制御コマンドとクエリを区別する

Kusto は、クエリと制御コマンドを区別するために、言語レベル、プロトコル レベル、API レベルの 3 つのメカニズムを使用します。 これはセキュリティ上の目的で行われます。

言語レベルでは、要求のテキストの最初の文字によって、要求が制御コマンドとクエリのどちらであるかが判別されます。 制御コマンドはドット (`.`) 文字で開始する必要がありますが、クエリをその文字で開始することはできません。

プロトコル レベルでは、クエリとは対照的に、さまざまな HTTP/HTTPS エンドポイントが制御コマンドに使用されます。

API レベルでは、クエリとは対照的に、制御コマンドを送信するためにさまざまな関数が使用されます。

## <a name="combining-queries-and-control-commands"></a>クエリと制御コマンドを結合する

制御コマンドは、クエリを参照できます (ただし、その逆はできません)。また、他の制御コマンドも参照できます。
サポートされているいくつかのシナリオがあります。

1. **AdminThenQuery**:制御コマンドが実行され、その結果 (一時データ テーブルとして表される) がクエリへの入力になります。
2. **AdminFromQuery**:クエリまたは `.show` 管理コマンドが実行され、その結果 (一時データ テーブルとして表される) が制御コマンドへの入力になります。

どの場合も、組み合わせ全体は、厳密にはクエリではなく制御コマンドであるため、要求のテキストはドット (`.`) 文字で始まっており、要求はサービスの管理エンドポイントに送信される必要があることに注意してください。

また、[クエリ ステートメント](../query/statements.md)がテキストのクエリ部分に含まれていることにも注意してください (コマンド自体の前には記述できません)。

>[!NOTE]
> [command-then-query] 操作を頻繁に実行しないでください。
> *command-then-query* により、制御コマンドの結果セットがパイプ処理され、フィルター/集計が適用されます。
>  * 例: `.show ... | where ... | summarize ...`
>   * `.show cluster extents | count` のような処理を実行すると (`| count` に注意)、Kusto により、クラスター内のすべてのエクステントのすべての詳細を保持するデータ テーブルが最初に準備されます。 その後、システムはそのメモリ内のみに存在するテーブルを Kusto エンジンに送信して、カウントを実行します。 システムでは、このような簡単な答えを得るために、実際には最適化されていないパスで非常に負荷の高い処理が実行されています。


**AdminThenQuery** は、次の 2 つの方法のいずれかで示されます。

1. パイプ (`|`) 文字を使用します。したがって、クエリは、制御コマンドの結果を、他のデータ生成クエリ演算子と同じように処理します。
2. セミコロン (`;`) 文字を使用します。これにより、制御コマンドの結果が `$command_results` という特殊記号に取り込まれます。これは、クエリ内で何回でも使用できます。

次に例を示します。

```kusto
// 1. Using pipe: Count how many tables are in the database-in-scope:
.show tables
| count

// 2. Using semicolon: Count how many tables are in the database-in-scope:
.show tables;
$command_results
| count

// 3. Using semicolon, and including a let statement:
.show tables;
let useless=(n:string){strcat(n,'-','useless')};
$command_results | extend LastColumn=useless(TableName)
```

**AdminFromQuery** は、`<|` 文字の組み合わせによって示されます。 たとえば、次の例では、まず 1 つの列 (`str` という名前で `string` 型) と 1 つの行を含むテーブルを生成するクエリを実行し、それを次のコンテキストでデータベースにテーブル名 `MyTable` として書き込みます。

```kusto
.set MyTable <|
let text="Hello, World!";
print str=text
```


