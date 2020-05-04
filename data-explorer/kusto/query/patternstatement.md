---
title: pattern ステートメント-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの pattern ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 97fc8361feb3d8dddb7d0722ce741ef44bd4cec6
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737268"
---
# <a name="pattern-statement"></a>pattern ステートメント

::: zone pivot="azuredataexplorer"

**パターン**とは、定義済みの文字列の組をパラメーターなしの関数本体にマップする、名前付きビューのような構造です。 パターンは、次の2つの側面で一意です。

* パターンは、スコープテーブル参照に似た構文を使用することによって呼び出されます。
* パターンには、マップ可能な制御されたクローズ終了の引数値のセットがあり、マッププロセスは Kusto によって行われます。 つまり、パターンが宣言されていても定義されていない場合、Kusto はパターンへのすべての呼び出しをエラーとして識別してフラグを設定し、中間層アプリケーションでこれらのパターンを "解決" できるようにします。


## <a name="pattern-declaration"></a>パターン宣言
Pattern ステートメントは、パターンを宣言または定義するために使用されます。
たとえば、パターンとしてを宣言`app`する pattern ステートメントを次に示します。

```kusto
declare pattern app;
```

このステートメントは、パターンである`app` kusto に指示しますが、パターンの解決方法を kusto 指示することはありません。 その結果、クエリでこのパターンを呼び出そうとすると、そのような呼び出しのすべてを一覧表示する特定のエラーが発生します。 次に例を示します。

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

このクエリでは、Kusto からのエラーが生成されます。これは、次`app("ApplicationX")["StartEvents"]`の`app("ApplicationX")["StopEvents"]`パターン呼び出しを解決できないことを示します。

**構文**

`declare``pattern` *パターン名*

## <a name="pattern-definition"></a>パターンの定義

Pattern ステートメントを使用してパターンを定義することもできます。 パターン定義では、パターンのすべての可能な呼び出しが明示的にレイアウトされ、対応する表形式の式が指定されます。 次に Kusto でクエリを実行すると、各パターン呼び出しが対応するパターン本体に置き換えられます。 次に例を示します。

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

一致する各パターンに対して指定される式は、テーブル名か[let ステートメント](letstatement.md)への参照のいずれかです。

**構文**

`declare``pattern` *Pattern 名* =  `:` *ArgType* `,` argname argname [...]*ArgName* `(``)` [`[` *パス名* `:` *pathargtype* `]`]`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* [`,` *ArgValue2* ...]`)` [ `.[` * Pathvalue `]` ] `=` `{`*式*`};` `(` *ArgValue1_2* *ArgValue2_2* [ArgValue1_2 [`,` ArgValue2_2...] &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;`)` [ `.[` *PathValue_2* `{` *expression_2* ] `=` expression_2`};` .. &nbsp; . `]` &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ]        `}`

* *Pattern Name*: pattern キーワードの名前。 キーワードだけを定義する構文は、指定されたキーワードを使用してすべてのパターン参照を検出するために使用できます。
* *Argname*: pattern 引数の名前。 パターンでは、1つまたは複数の引数名を使用できます。
* *Argtype*: pattern 引数の型 (現時点でのみ`string`許可されています)
* *PathName*: パス引数の名前。 パターンでは、0個または1個のパス名を使用できます。
* *Pathtype*: パス引数の型 (現時点でのみ`string`許可されています)
* *ArgValue1*、 *ArgValue2*、...-pattern 引数の値 (現在はリテラル`string`のみを使用できます)
* *Pathvalue* -パターンパスの値 (現在はリテラル`string`のみを使用できます)
* *式*:*式*-テーブル式 (など`Logs | where Timestamp > ago(1h)`)、または関数を参照するラムダ式。

## <a name="pattern-invocation"></a>パターン呼び出し

パターン呼び出し構文は、スコープが指定されたテーブル参照構文に似ています。

* *Pattern name* `(` *ArgValue1* [`,` *ArgValue2* ...]`).` *Pathvalue*
* *Pattern name* `(` *ArgValue1* [`,` *ArgValue2* ...]`).["` *Pathvalue*`"]`

## <a name="remarks"></a>解説

**シナリオ**

Pattern ステートメントは、ユーザークエリを受け取り、これらのクエリを Kusto に送信する中間層アプリケーション向けに設計されています。 このようなアプリケーションでは、多くの場合、これらのユーザークエリの前に論理スキーマモデル (たとえば、 [restrict ステートメント](restrictstatement.md)でサフィックスが付けられている[let ステートメント](letstatement.md)のセット) を使用します。
場合によっては、これらのアプリケーションでは、事前に既知ではないエンティティを参照するようにユーザーに指定できる構文が必要になることがあります。また、潜在的なエンティティの数が大きすぎて論理スキーマで事前に定義されていない可能性があるためです。

パターンは、このシナリオを次のように解決します。 中間層アプリケーションはクエリを Kusto に送信します。すべてのパターンが宣言されていますが、定義されていません。 次に kusto はクエリを解析し、1つまたは複数のパターン呼び出しがある場合は、このようなすべての呼び出しが明示的に一覧表示された中間層アプリケーションにエラーを返します。 その後、中間層アプリケーションは、これらの参照をそれぞれ解決し、クエリを再実行します。今回は、完全に詳細なパターン定義でプレフィックスを付けます。

**Normalizations 場合**

Kusto はパターンを自動的に正規化します。したがって、たとえば、次の例では、同じパターンをすべて呼び出し、1つを返します。

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

これは、同じであると見なされるため、1つを同時に定義できないことも意味します。

**ワイルドカード**

Kusto は、パターンでワイルドカードを特別な方法では扱いません。 たとえば、次のクエリでは、

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto は、1つの見つからないパターン`app("ApplicationX").["*"]`呼び出しを報告します。

## <a name="examples"></a>例

複数のパターン呼び出しに対するクエリ:

```kusto
declare pattern A
{
    // ...
};
union (A('a1').Text), (A('a2').Text)
```

|アプリ|その他のテキスト|
|---|---|
|アプリの #1|これは無料のテキストです: 1|
|アプリの #1|これは無料のテキストです。2|
|アプリの #1|これは無料のテキストです: 3|
|アプリの #1|これは無料のテキストです: 4|
|アプリの #1|これは無料のテキストです: 5|
|アプリの #2|これは無料のテキストです: 9|
|アプリの #2|これは無料のテキストです: 8|
|アプリの #2|これは無料のテキストです: 7|
|アプリの #2|これは無料のテキストです: 6|
|アプリの #2|これは無料のテキストです: 5|

```kusto
declare pattern App;
union (App('a1').Text), (App('a2').Text)
```

セマンティックエラー:

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

セマンティックエラーが返されました:

    SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a2').['Metrics']","App('a3').['Metrics']"].

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
