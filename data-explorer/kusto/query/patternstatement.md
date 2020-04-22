---
title: パターン ステートメント - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのパターン ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d7ad021fe7b458d54beeb24b908420324b45ad39
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765671"
---
# <a name="pattern-statement"></a>パターンステートメント

::: zone pivot="azuredataexplorer"

**パターン**は、定義済みの文字列タプルをパラメーターなしの関数本体にマップする名前付きビューに似た構造です。 パターンは、次の 2 つの側面で一意です。

* パターンは、スコープ指定されたテーブル参照に似た構文を使用して"呼び出される" 。
* パターンには、マップできる、厳密に処理された一連の引数値があり、マッピング プロセスは Kusto によって実行されます。 これは、パターンが宣言されているが定義されていない場合、Kusto はパターンに対するすべての呼び出しをエラーとして識別してフラグを付け、中間層アプリケーションによってこれらのパターンを「解決」することを可能にすることを意味します。


## <a name="pattern-declaration"></a>パターン宣言
パターンステートメントは、パターンの宣言または定義に使用されます。
たとえば、次はパターンとして宣言`app`するパターンステートメントです。

```kusto
declare pattern app;
```

この文は、パターンである`app`Kustoに指示しますが、パターンの解決方法は Kusto に指示しません。 その結果、クエリでこのパターンを呼び出そうとすると、そのような呼び出しがすべてリストされた特定のエラーが発生します。 次に例を示します。

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

このクエリは Kusto からエラーを生成し`app("ApplicationX")["StartEvents"]``app("ApplicationX")["StopEvents"]`、次のパターン呼び出しを解決できないことを示します。

**構文**

`declare``pattern`*パターン名*

## <a name="pattern-definition"></a>パターン定義

パターン・ステートメントは、パターンを定義するためにも使用できます。 パターン定義では、パターンの呼び出しの可能性がすべて明示的にレイアウトされ、対応する表形式式が指定されます。 Kusto は、クエリを実行すると、各パターン呼び出しを対応するパターン本体に置き換えます。 次に例を示します。

```kusto
declare pattern app = (applicationId:string)[eventType:string]
{
    ("ApplicationX").["StopEvents"] = { database("AppX").Events | where EventType == "StopEvent" };
    ("ApplicationX").["StartEvents"] = { database(applicationId).Events | where EventType == eventType } ;
};
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

一致する各パターンに対して指定される式は、テーブル名または[let ステートメント](letstatement.md)への参照です。

**構文**

`declare``pattern`*パターン* = 名`(`*ArgName* `:` *ArgType* [`,` ..`)` [`[` *パス名*`:`*パス引数 ]* `]``{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* `,` [ *ArgValue2* ..`)` [ `.[` *パス`]`値`=``{`] &nbsp; *式*`};`&nbsp;&nbsp;&nbsp;&nbsp;`,`*ArgValue2_2*[ `(` *ArgValue1_2* ArgValue1_2 [ ArgValue2_2 . &nbsp; &nbsp; &nbsp;`)` [ `.[` *PathValue_2* `]` PathValue_2 `=` `{`] *expression_2.* `};` &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ]        `}`

* *パターン名*: パターン キーワードの名前。 キーワードのみを定義する構文は、指定されたキーワードを使用してすべてのパターン参照を検出する場合に使用できます。
* *ArgName*: パターン引数の名前。 パターンでは、1 つ以上の引数名を使用できます。
* *ArgType*: パターン引数の型 (`string`現在は許可されている場合のみ)
* *パス名*: パス引数の名前。 パターンでは、0 または 1 つのパス名を指定できます。
* *パスタイプ*: パス引数の型 (`string`現在のみ許可されています)
* *ArgValue1* *、ArgValue2*、.- パターン引数の値`string`(現在はリテラルのみが許可されています)
* *PathValue* - パターンパスの値(現在`string`はリテラルのみが許可されています)
* *式*: 式 *-* 表形式の式`Logs | where Timestamp > ago(1h)`(たとえば) または関数を参照するラムダ式。

## <a name="pattern-invocation"></a>パターン呼び出し

パターン呼び出し構文は、スコープ指定テーブル参照構文に似ています。

* *パターン名*`(` *ArgValue1* [`,` *ArgValue2* ..]`).`*パス値*
* *パターン名*`(` *ArgValue1* [`,` *ArgValue2* ..]`).["`*パス値*`"]`

## <a name="remarks"></a>解説

**シナリオ**

パターン ステートメントは、ユーザー クエリを受け入れ、これらのクエリを Kusto に送信する中間層アプリケーション用に設計されています。 このようなアプリケーションは、多くの場合、これらのユーザー クエリの前に論理スキーマ モデル (一連の[let ステートメント](letstatement.md)、 場合によっては[restrict ステートメント](restrictstatement.md)のサフィックス) を付けます。
場合によっては、これらのアプリケーションは、事前に認識できず、作成する論理スキーマで定義されているエンティティを参照するようにユーザーに提供できる構文を必要とします (潜在的なエンティティの数が論理スキーマで事前に定義するには大きすぎるため、事前に知られていないため)。

パターンは、次の方法でこのシナリオを解決します。 中間層アプリケーションは、宣言されたすべてのパターンを含む Kusto にクエリを送信しますが、定義されていません。 Kusto はクエリを解析し、1 つ以上のパターン呼び出しが存在する場合は、中間層アプリケーションにエラーを返し、そのような呼び出しがすべて明示的にリストされます。 中間層アプリケーションは、これらの各参照を解決し、クエリを再実行して、今度は完全に詳細なパターン定義をプレフィックスとして使用できます。

**正規化**

Kusto は自動的にパターンを正規化するので、例えば次の例は同じパターンの呼び出しすべてであり、1 つのパターンが報告されます。

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

これは、同じと見なされるため、それらを一緒に定義できないことを意味します。

**ワイルドカード**

Kusto はワイルドカードをパターンで特別な方法で扱いません。 たとえば、次のクエリを使用します。

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto は、単一の欠落パターン`app("ApplicationX").["*"]`呼び出しを報告します: .

## <a name="examples"></a>例

複数のパターン呼び出しに対するクエリ:

```kusto
declare pattern A
{
    // ...
};
union (A('a1').Text), (A('a2').Text)
```

|アプリ|いくつかのテキスト|
|---|---|
|アプリ#1|これはフリーテキストです: 1|
|アプリ#1|これはフリーテキストです: 2|
|アプリ#1|これはフリーテキストです: 3|
|アプリ#1|これはフリーテキストです: 4|
|アプリ#1|これはフリーテキストです: 5|
|アプリ#2|これはフリーテキストです: 9|
|アプリ#2|これはフリーテキストです: 8|
|アプリ#2|これはフリーテキストです: 7|
|アプリ#2|これはフリーテキストです: 6|
|アプリ#2|これはフリーテキストです: 5|

```kusto
declare pattern App;
union (App('a1').Text), (App('a2').Text)
```

セマンティック エラー:

     SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a1').['Text']","App('a2').['Text']"].

```kusto
declare pattern App;
declare pattern App = (applicationId:string)[scope:string]  
{
    ('a1').['Data']    = { range x from 1 to 5 step 1 | project App = "App #1", Data    = x };
    ('a1').['Metrics'] = { range x from 1 to 5 step 1 | project App = "App #1", Metrics = rand() };
    ('a2').['Data']    = { range x from 1 to 5 step 1 | project App = "App #2", Data    = 10 - x };
    ('a3').['Metrics'] = { range x from 1 to 5 step 1 | project App = "App #3", Metrics = rand() };
};
union (App('a2').Metrics), (App('a3').Metrics) 
```

セマンティック エラーが返されました:

    SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a2').['Metrics']","App('a3').['Metrics']"].

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
