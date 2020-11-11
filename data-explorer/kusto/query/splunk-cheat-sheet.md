---
title: Azure Monitor の Kusto ログクエリ言語について
description: Kusto log クエリの詳細について理解しているユーザーのためのヘルプ。
ms.service: data-explorer
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: f574493f2029d08fd71b44c858592a6fd8467c82
ms.sourcegitcommit: b6f0f112b6ddf402e97c011a902bd70ba408e897
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94499109"
---
# <a name="splunk-to-kusto-query-language"></a>Kusto クエリ言語について

この記事の目的は、kusto にログクエリを記述する Kusto クエリ言語について説明します。 2 つを直接比較し、主要な相違点と、既存の知識を活用できる類似点を明らかにします。

## <a name="structure-and-concepts"></a>構造と概念

次の表は、出力と Kusto のログの概念とデータ構造を比較したものです。

 | 概念 | Splunk | Kusto |  解説 |
 |:---|:---|:---|:---|
 | 展開単位  | cluster |  cluster |  Kusto では、任意のクロスクラスタークエリを使用できます。 Splunk ではできません。 |
 | データ キャッシュ |  バケット  |  キャッシュおよび保持ポリシー |  データの期間とキャッシュ レベルを制御します。 この設定は、クエリのパフォーマンスと展開のコストに直接影響します。 |
 | データの論理パーティション  |  インデックス (index)  |  database  |  データの論理的な分離を可能にします。 どちらの実装でも、これらのパーティション間の和集合と結合が可能です。 |
 | 構造化されたイベント メタデータ | 該当なし | table |  Splunk には、イベント メタデータの検索言語に対して公開される概念はありません。 Kusto ログには、列を持つテーブルという概念があります。 各イベント インスタンスは行にマップされます。 |
 | データ レコード | イベント | 行 |  用語の変更のみです。 |
 | データ レコード属性 | フィールド |  column |  Kusto では、これはテーブル構造の一部として事前に定義されています。 Splunk では、イベントごとに固有のフィールドのセットがあります。 |
 | 型 | データ型 |  データ型 |  Kusto データ型は、列に対して設定されるため、より明示的になります。 どちらにもデータ型を動的に操作する機能があり、JSON のサポートを含めて、データ型のセットはほぼ同等です。 |
 | クエリと検索  | 検索 | query |  概念は、基本的に Kusto との両方で同じです。 |
 | イベント取り込み時刻 | システム時刻 | ingestion_time() |  Splunk では、イベントのインデックスが作成された時刻のシステム タイムスタンプが各イベントに設定されます。 Kusto では、ingestion_time () 関数を通じて参照できるシステム列を公開する ingestion_time というポリシーを定義できます。 |

## <a name="functions"></a>関数

次の表では、Kusto に含まれる関数を指定しています。

|Splunk | Kusto |コメント
|:---|:---|:---
| `strcat` | `strcat()` | (1) |
| `split`  | `split()` | (1) |
| `if`     | `iff()`   | (1) |
| `tonumber` | `todouble()`<br>`tolong()`<br>`toint()` | (1) |
| `upper`<br>`lower` |toupper()<br>`tolower()`|(1) |
| `replace` | `replace()` | (1)<br> どちらの製品も `replace()` が受け取るパラメーターの数は 3 つですが、パラメーターは異なることにも注意してください。 |
| `substr` | `substring()` | (1)<br>Splunk では 1 から始まるインデックスを使用することにも注意してください。 Kusto notes の0から始まるインデックス。 |
| `tolower` |  `tolower()` | (1) |
| `toupper` | `toupper()` | (1) |
| `match` | `matches regex` |  (2)  |
| `regex` | `matches regex` | Splunk では、`regex` は演算子です。 Kusto では、これは関係演算子です。 |
| `searchmatch` | == | Splunk の `searchmatch` では、厳密な文字列を検索できます。
| `random` | rand()<br>rand(n) | Splunk の関数は、0 から 2<sup>31</sup>-1 までの値を返します。 Kusto ' は、0.0 ~ 1.0 の範囲の数値を返します。または、パラメーターが指定されている場合は 0 ~ n-1 の範囲の数値を返します。
| `now` | `now()` | (1)
| `relative_time` | `totimespan()` | (1)<br>Kusto では、relative_time (datetimeVal、offsetVal) は datetimeVal + totimespan (offsetVal) に相当します。<br>たとえば、<code>search &#124; eval n=relative_time(now(), "-1d@d")</code> を <code>...  &#124; extend myTime = now() - totimespan("1d")</code> にします。

(1) Splunk では、関数は `eval` 演算子で呼び出されます。 Kusto では、またはの一部として使用され `extend` `project` ます。<br>(2) Splunk では、関数は `eval` 演算子で呼び出されます。 Kusto では、演算子と共に使用でき `where` ます。


## <a name="operators"></a>オペレーター

次のセクションでは、さまざまな演算子を使用する例について説明します。

> [!NOTE]
> 次の例では、"" というフィールド _ルール_ が Kusto のテーブルにマップされ、[ログ分析 _ingestion_time ()_ ] 列にマップされる既定のタイムスタンプがあります。

### <a name="search"></a>検索
Splunk では、`search` キーワードを省略し、引用符なしの文字列を指定することができます。 Kusto では、を使用して各クエリを開始する必要があります `find` 。引用符で囲まれていない文字列は列名であり、参照値は引用符で囲まれた文字列である必要があります。 

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `search` | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Kusto | `find` | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |


### <a name="filter"></a>Assert
Kusto ログクエリは、フィルターが適用された表形式の結果セットから開始します。 Splunk では、フィルター処理は現在のインデックスに対する既定の操作です。 Splunk でも `where` 演算子を使用できますが、推奨されません。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `search` | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Kusto | `where` | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |

### <a name="getting-n-eventsrows-for-inspection"></a>検査のために n 個のイベント/行を取得する 
Kusto ログクエリは、の `take` 別名としてもサポート `limit` します。 Splunk では、結果が順序付けされている場合、`head` は最初の n 個の結果を返します。 Kusto では、limit は順序付けされませんが、最初の n 個の行が返されます。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `head` | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Kusto | `limit` | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |

### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>フィールド/列で順序づけされた最初の n イベント/行を取得する
下位の結果の場合、Splunk では `tail` を使用します。 Kusto では、順序付けの方向をで指定でき `asc` ます。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `head` |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Kusto | `top` | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |

### <a name="extending-the-result-set-with-new-fieldscolumns"></a>新しいフィールド/列で結果セットを拡張する
Splunk には `eval` 関数もありますが、`eval` 演算子と同等ではありません。 関数と Kusto の演算子は両方とも、 `eval` `extend` スカラー関数と算術演算子のみをサポートしています。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `eval` |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Kusto | `extend` | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |

### <a name="rename"></a>名前の変更 
Kusto は、演算子を使用して `project-rename` フィールドの名前を変更します。 `project-rename` によって、クエリでフィールドにあらかじめ構築されているインデックスを利用できるようにします。 Splunk では、同じことを行うために `rename` 演算子が用意されています。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `rename` |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Kusto | `project-rename` | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |

### <a name="format-resultsprojection"></a>形式の書式設定/プロジェクション
Splunk には、`project-away` と似た演算子はないようです。 UI を使用してフィールドをフィルター処理できます。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `table` |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Kusto | `project`<br>`project-away` | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |

### <a name="aggregation"></a>集計
さまざまな集計関数の [集計関数の一覧](summarizeoperator.md#list-of-aggregation-functions) を参照してください。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `stats` |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Kusto | `summarize` | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |


### <a name="join"></a>Join
Splunk での結合には重要な制限があります。 サブクエリには 10000 件の結果の制限があり (展開構成ファイルで設定)、結合の種類の数に制限があります。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `join` |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Kusto | `join` | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |

### <a name="sort"></a>並べ替え
Splunk では、昇順に並べ替えるには、`reverse` 演算子を使用する必要があります。 Kusto では、最初または最後に null を格納する場所を定義することもできます。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `sort` |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Kusto | `order by` | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |

### <a name="multivalue-expand"></a>複数値の展開
これは、両方とも同じような演算子であり、Kusto です。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `mvexpand` |  `mvexpand foo` |
| Kusto | `mvexpand` | `mvexpand foo` |

### <a name="results-facets-interesting-fields"></a>Results facets, interesting fields
Azure portal での Log Analytics では、最初の列のみが展開されます。 すべての列は、API を介して利用します。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `fields` |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Kusto | `facets` | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |

### <a name="de-duplicate"></a>重複除去
選択されたレコードの順序を逆にする代わりに、`summarize arg_min()` を使用できます。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `dedup` |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Kusto | `summarize arg_max()` | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |

## <a name="next-steps"></a>次の手順

- [Kusto クエリ言語](tutorial.md?pivots=azuremonitor)に関するチュートリアルを紹介します。
