---
title: Azure データエクスプローラーおよび Azure Monitor のために Kusto にマップする
description: ログクエリを記述する Kusto クエリ言語について学習するために、このようなユーザーのための概念マッピング。
ms.service: data-explorer
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 8679b47a2c698c88c4d1773f9c50dee495532ae7
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783235"
---
# <a name="splunk-to-kusto-query-language-map"></a>Kusto クエリ言語マップについて

この記事は、kusto でログクエリを作成するための Kusto クエリ言語について説明しています。 2つの間に直接比較が行われ、重要な相違点と類似点が強調表示されるため、既存の知識を基に構築できます。

## <a name="structure-and-concepts"></a>構造と概念

次の表は、出力と Kusto のログの概念とデータ構造を比較したものです。

 | 概念 | Splunk | Kusto |  コメント |
 |:---|:---|:---|:---|
 | 展開単位  | cluster |  cluster |  Kusto では、任意のクラスター間クエリを実行できます。 Splunk ではできません。 |
 | データキャッシュ |  バケット  |  キャッシュと保持ポリシー |  データの期間とキャッシュ レベルを制御します。 この設定は、クエリのパフォーマンスと展開のコストに直接影響します。 |
 | データの論理パーティション  |  インデックス (index)  |  database  |  データの論理的な分離を可能にします。 どちらの実装でも、これらのパーティション間の和集合と結合が可能です。 |
 | 構造化されたイベントのメタデータ | 該当なし | table |  イベントメタデータの概念を検索言語に公開することはできません。 Kusto ログには、列を持つテーブルという概念があります。 各イベント インスタンスは行にマップされます。 |
 | データレコード | イベント | 行 |  用語の変更のみです。 |
 | データレコード属性 | フィールド |  column |  Kusto では、この設定はテーブル構造の一部として事前定義されています。 Splunk では、イベントごとに固有のフィールドのセットがあります。 |
 | types | データ型 |  データ型 |  Kusto データ型は、列に設定されているため、より明示的になります。 どちらも、データ型とほぼ同等のデータセット (JSON サポートを含む) を使用して動的に作業できます。 |
 | クエリと検索  | 検索 | query |  基本的に、Kusto との間での概念は同じです。 |
 | イベントインジェスト時間 | システム時刻 | `ingestion_time()` |  この場合、各イベントは、イベントのインデックスが作成された時刻のシステムタイムスタンプを取得します。 Kusto では、 [ingestion_time ()](ingestiontimefunction.md)関数を通じて参照できるシステム列を公開する[ingestion_time](../management/ingestiontimepolicy.md)というポリシーを定義できます。 |

## <a name="functions"></a>機能

次の表では、Kusto に含まれる関数を指定しています。

|Splunk | Kusto |コメント
|:---|:---|:---
| `strcat` | `strcat()` | (1) |
| `split`  | `split()` | (1) |
| `if`     | `iff()`   | (1) |
| `tonumber` | `todouble()`<br />`tolong()`<br />`toint()` | (1) |
| `upper`<br />`lower` |toupper()<br />`tolower()`|(1) |
| `replace` | `replace()` | (1)<br /> ただし、 `replace()` どちらの製品でも3つのパラメーターを受け取るが、パラメーターが異なることに注意してください。 |
| `substr` | `substring()` | (1)<br />Splunk では 1 から始まるインデックスを使用することにも注意してください。 Kusto notes の0から始まるインデックス。 |
| `tolower` |  `tolower()` | (1) |
| `toupper` | `toupper()` | (1) |
| `match` | `matches regex` |  (2)  |
| `regex` | `matches regex` | Splunk では、`regex` は演算子です。 Kusto では、これは関係演算子です。 |
| `searchmatch` | == | Splunk の `searchmatch` では、厳密な文字列を検索できます。
| `random` | rand()<br />rand(n) | の関数は、0から 2<sup>31</sup>-1 までの数値を返します。 Kusto は、0.0 ~ 1.0 の範囲の数値を返します。または、パラメーターが指定されている場合は 0 ~ n-1 の範囲の値を返します。
| `now` | `now()` | (1)
| `relative_time` | `totimespan()` | (1)<br />Kusto では、と同じ意味です `relative_time(datetimeVal, offsetVal)` `datetimeVal + totimespan(offsetVal)` 。<br />たとえば、 `search` &#124; が `eval n=relative_time(now(), "-1d@d")` &#124; になり `...` `extend myTime = now() - totimespan("1d")` ます。

(1) の場合、関数は演算子を使用して呼び出され `eval` ます。 Kusto では、またはの一部として使用され `extend` `project` ます。<br />(2) 式では、演算子を使用して関数が呼び出され `eval` ます。 Kusto では、演算子と共に使用でき `where` ます。


## <a name="operators"></a>演算子

次のセクションでは、さまざまな演算子を使用する方法の例について説明します。

> [!NOTE]
> 次の例では、[出力] フィールドを `rule` Kusto のテーブルにマップし、[既定のタイムスタンプ] を [ログ分析] 列にマップし `ingestion_time()` ます。

### <a name="search"></a>検索

Splunk では、`search` キーワードを省略し、引用符なしの文字列を指定することができます。 Kusto では、を使用して各クエリを開始する必要があります `find` 。引用符で囲まれていない文字列は列名であり、参照値は引用符で囲まれた文字列である必要があります。 

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `search` | `search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h` |
| Kusto | `find` | `find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)` |


### <a name="filter"></a>Assert

Kusto ログクエリは、が適用された表形式の結果セットから開始 `filter` します。 Splunk では、フィルター処理は現在のインデックスに対する既定の操作です。 また、演算子を使用することもでき `where` ます。ただし、この操作はお勧めしません。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `search` | `Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h` |
| Kusto | `where` | `Office_Hub_OHubBGTaskError`<br />&#124; `where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)` |

### <a name="get-n-events-or-rows-for-inspection"></a>検査のために *n 個* のイベントまたは行を取得する

Kusto ログクエリは、の `take` 別名としてもサポート `limit` します。 この場合、結果が順序付けされている場合は、 `head` 最初の *n* 件の結果が返されます。 Kusto では、は並べ替えられません `limit` が、最初の *n 個* の行が返されます。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `head` | `Event.Rule=330009.2`<br />&#124; `head 100` |
| Kusto | `limit` | `Office_Hub_OHubBGTaskError`<br />&#124; `limit 100` |

### <a name="get-the-first-n-events-or-rows-ordered-by-a-field-or-column"></a>フィールドまたは列で並べ替えられた最初の *n 個* のイベントまたは行を取得します。

一番下の結果を表示するには、を使用し `tail` ます。 Kusto では、を使用して順序付けの方向を指定でき `asc` ます。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `head` |  `Event.Rule="330009.2"`<br />&#124; `sort Event.Sequence`<br />&#124; `head 20` |
| Kusto | `top` | `Office_Hub_OHubBGTaskError`<br />&#124; `top 20 by Event_Sequence` |

### <a name="extend-the-result-set-with-new-fields-or-columns"></a>新しいフィールドまたは列を使用して結果セットを拡張する

この関数には、関数があり `eval` ますが、 `eval` Kusto の演算子と比較することはできません。 `eval` `extend` 関数と Kusto の演算子は両方とも、スカラー関数と算術演算子のみをサポートしています。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `eval` |  `Event.Rule=330009.2`<br />&#124; `eval state= if(Data.Exception = "0", "success", "error")` |
| Kusto | `extend` | `Office_Hub_OHubBGTaskError`<br />&#124; `extend state = iif(Data_Exception == 0,"success" ,"error")` |

### <a name="rename"></a>[名前の変更]

Kusto は、演算子を使用して `project-rename` フィールドの名前を変更します。 演算子では `project-rename` 、フィールドに対して事前に作成されたインデックスをクエリで利用できます。 `rename`同じ操作を行う演算子があります。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `rename` |  `Event.Rule=330009.2`<br />&#124; `rename Date.Exception as execption` |
| Kusto | `project-rename` | `Office_Hub_OHubBGTaskError`<br />&#124; `project-rename exception = Date_Exception` |

### <a name="format-results-and-projection"></a>結果と投影の書式設定

式に似た演算子がないよう `project-away` です。 UI を使用して、フィールドを除外することができます。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `table` |  `Event.Rule=330009.2`<br />&#124; `table rule, state` |
| Kusto | `project`<br />`project-away` | `Office_Hub_OHubBGTaskError`<br />&#124; `project exception, state` |

### <a name="aggregation"></a>集計

使用可能な [集計関数の一覧](summarizeoperator.md#list-of-aggregation-functions) を参照してください。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `stats` |  `search (Rule=120502.*)`<br />&#124; `stats count by OSEnv, Audience` |
| Kusto | `summarize` | `Office_Hub_OHubBGTaskError`<br />&#124; `summarize count() by App_Platform, Release_Audience` |


### <a name="join"></a>Join

`join` には、大きな制限があります。 サブクエリには、(配置構成ファイルで設定された) 1万の結果の制限があり、使用可能な結合の種類の数に制限があります。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `join` |  `Event.Rule=120103* &#124; stats by Client.Id, Data.Alias` <br />&#124; `join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]` |
| Kusto | `join` | `cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions`<br />&#124; `where  Data_Hresult== -2147221040`<br />&#124; `join kind = inner (Office_System_SystemHealthMetadata`<br />&#124; `summarize by Client_Id, Data_Alias)on Client_Id`   |

### <a name="sort"></a>並べ替え

順序を昇順に並べ替えるには、演算子を使用する必要があり `reverse` ます。 Kusto では、先頭または末尾に null を格納する場所を定義することもできます。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `sort` |  `Event.Rule=120103`<br />&#124; `sort Data.Hresult` <br />&#124; `reverse` |
| Kusto | `order by` | `Office_Hub_OHubBGTaskError`<br />&#124; `order by Data_Hresult,  desc` |

### <a name="multivalue-expand"></a>複数値の展開

複数値の展開演算子は、両方とも、すべての値が同じで、Kusto に似ています。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `mvexpand` |  `mvexpand solutions` |
| Kusto | `mv-expand` | `mv-expand solutions` |

### <a name="result-facets-interesting-fields"></a>結果ファセット、興味深いフィールド

Azure portal での Log Analytics では、最初の列のみが展開されます。 すべての列は、API を介して利用します。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `fields` |  `Event.Rule=330009.2`<br />&#124; `fields App.Version, App.Platform` |
| Kusto | `facets` | `Office_Excel_BI_PivotTableCreate`<br />&#124; `facet by App_Branch, App_Version` |

### <a name="deduplicate"></a>重複除去

Kusto では、を使用し `summarize arg_min()` て、選択したレコードの順序を逆にすることができます。

| 製品 | 演算子 | 例 |
|:---|:---|:---|
| Splunk | `dedup` |  `Event.Rule=330009.2`<br />&#124; `dedup device_id sortby -batterylife` |
| Kusto | `summarize arg_max()` | `Office_Excel_BI_PivotTableCreate`<br />&#124; `summarize arg_max(batterylife, *) by device_id` |

## <a name="next-steps"></a>次のステップ

- [Kusto クエリ言語](tutorial.md?pivots=azuremonitor)のチュートリアルについて説明します。
