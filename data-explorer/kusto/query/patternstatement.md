---
title: pattern ステートメント-Azure データエクスプローラー
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
ms.openlocfilehash: 03d183bd042bb75d8bb44f530575bd3b91cb2102
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793807"
---
# <a name="pattern-statement"></a>pattern ステートメント

::: zone pivot="azuredataexplorer"

**パターン**とは、定義済みの文字列の組をパラメーターなしの関数本体にマップする、名前付きビューのような構造です。 パターンは、次の2つの側面で一意です。

* パターンは、スコープテーブル参照に似た構文を使用することによって呼び出されます。
* パターンには、マップ可能な制御されたクローズ終了の引数値のセットがあり、マッププロセスは Kusto によって行われます。 パターンが宣言されていても定義されていない場合、Kusto は、パターンへのすべての呼び出しをエラーとして識別し、フラグを設定します。 この id を使用すると、中間層アプリケーションでこれらのパターンを "解決" できます。

## <a name="pattern-declaration"></a>パターン宣言

Pattern ステートメントは、パターンを宣言または定義するために使用されます。
たとえば、パターンとしてを宣言するパターンステートメントがあり `app` ます。

```kusto
declare pattern app;
```

このステートメントは、パターンである Kusto に指示し `app` ますが、パターンの解決方法を Kusto 通知しません。 その結果、クエリでこのパターンを呼び出そうとすると、特定のエラーが発生し、そのような呼び出しがすべて一覧表示されます。 次に例を示します。

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

このクエリでは、Kusto から次のパターン呼び出しを解決できないことを示すエラーが生成され `app("ApplicationX")["StartEvents"]` ます。 `app("ApplicationX")["StopEvents"]`

## <a name="syntax"></a>構文

`declare``pattern`*パターン名*

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

一致する各パターンに対して指定される式は、テーブル名か [let ステートメント](letstatement.md)への参照のいずれかです。

## <a name="syntax"></a>構文

`declare``pattern`*パターン*  =  名 `(`*Argname* `:`*Argtype* [ `,` ...] `)`[ `[` *パス名* `:` *pathargtype* `]` ]`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* [ `,` *ArgValue2* ...] `)` [ `.[` * pathvalue `]` ] `=` `{` *式* `};` &nbsp; &nbsp; &nbsp; &nbsp; [ &nbsp; &nbsp; &nbsp; &nbsp; `(` *ArgValue1_2* [ `,` *ArgValue2_2* ...] `)` [ `.[` *PathValue_2* `]` ] `=` `{` *expression_2* `};` &nbsp; &nbsp; &nbsp; &nbsp; ... &nbsp; &nbsp; &nbsp; &nbsp; ]        `}`

* *Pattern Name*: pattern キーワードの名前。 キーワードだけを定義する構文は、指定されたキーワードを使用してすべてのパターン参照を検出するために使用できます。
* *Argname*: pattern 引数の名前。 パターンでは、1つまたは複数の引数名を使用できます。
* *Argtype*: pattern 引数の型 (現時点でのみ `string` 許可されています)
* *PathName*: パス引数の名前。 パターンでは、0個または1個のパス名を使用できます。
* *Pathtype*: パス引数の型 (現時点でのみ `string` 許可されています)
* *ArgValue1*、 *ArgValue2*、...-pattern 引数の値 (現在 `string` はリテラルのみを使用できます)
* *Pathvalue* -パターンパスの値 (現在 `string` はリテラルのみを使用できます)
* *式*: *式* -テーブル式 (など `Logs | where Timestamp > ago(1h)` )、または関数を参照するラムダ式。

## <a name="pattern-invocation"></a>パターン呼び出し

パターン呼び出し構文は、スコープが指定されたテーブル参照構文に似ています。

* *パターン名* `(`*ArgValue1* [ `,` *ArgValue2* ...] `).`*Pathvalue*
* *パターン名* `(`*ArgValue1* [ `,` *ArgValue2* ...] `).["`*Pathvalue*`"]`

## <a name="notes"></a>メモ

**シナリオ**

Pattern ステートメントは、ユーザークエリを受け取り、これらのクエリを Kusto に送信する中間層アプリケーション向けに設計されています。 このようなアプリケーションでは、多くの場合、これらのユーザークエリの前に論理スキーマモデルを使用します。 このモデルは [let ステートメント](letstatement.md)のセットであり、場合によっては [restrict ステートメント](restrictstatement.md)がサフィックスとして使用されます。

アプリケーションによっては、ユーザーに提供される構文が必要になる場合があります。 この構文は、アプリケーションによって作成される論理スキーマで定義されているエンティティを参照するために使用されます。 ただし、エンティティが事前に認識されていない場合や、潜在的なエンティティの数が大きすぎて論理スキーマで事前に定義されていない場合があります。

パターンは、このシナリオを次のように解決します。 中間層アプリケーションはクエリを Kusto に送信します。すべてのパターンが宣言されていますが、定義されていません。 次に、クエリを解析します。 1つ以上のパターンの呼び出しがある場合、Kusto は中間層アプリケーションに対して、このようなすべての呼び出しが明示的に一覧表示されているエラーを返します。 その後、中間層アプリケーションは、これらの参照をそれぞれ解決し、クエリを再実行できます。 今回は、完全に詳細なパターン定義でプレフィックスを付けます。

**Normalizations 場合**

Kusto は、パターンを自動的に正規化します。 たとえば、次に示すのは、同じパターンのすべての呼び出しであり、1つのパターンが返されます。

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

これは、同じであると見なされるため、これらを一緒に定義できないことも意味します。

**ワイルドカード**

Kusto は、パターンでワイルドカードを特別な方法では扱いません。 たとえば、次のクエリではです。

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto は、1つの見つからないパターン呼び出しを報告します。 `app("ApplicationX").["*"]`

## <a name="examples"></a>例

複数のパターン呼び出しに対してクエリを行います。

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

**セマンティックエラー**:

> SEM0036: 1 つ以上のパターン参照が宣言されていません。 検出されたパターン参照: ["App (' a1 '). ['テキスト '] "、" App (' a2 ')。[' Text '] "]。

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

**セマンティックエラーが返されました**:

> SEM0036: 1 つ以上のパターン参照が宣言されていません。 検出されたパターン参照: ["App (' a2 '). ['メトリック '] "、" App (' a3 ')。[' メトリック '] "]。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
