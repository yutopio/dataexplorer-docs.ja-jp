---
title: 検索演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの検索演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 24e79b7feeb51a0626ed270a90c3d323fa94cbf3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250282"
---
# <a name="search-operator"></a>search 演算子

検索操作では、複数テーブル/複数列の検索エクスペリエンスが提供されます。

## <a name="syntax"></a>構文

* [*TabularSource* `|` ]`search`[ `kind=` *CaseSensitivity*] [ `in` `(` *TableSources* `)` ] *searchpredicate*

## <a name="arguments"></a>引数

* *TabularSource*: テーブル名、 [union 演算子](unionoperator.md)、表形式クエリの結果など、検索対象のデータソースとして機能するオプションの表形式の式。 *TableSources*を含むオプションの語句と共に使用することはできません。

* *CaseSensitivity*: `string` 大文字小文字の区別に関して、すべてのスカラー演算子の動作を制御する省略可能なフラグ。 有効な値は、2つのシノニム `default` と (などの演算子 (たとえば、大文字と小文字を区別しない) および (大文字と小文字を区別 `case_insensitive` `has` `case_sensitive` する一致モードにすべての演算子を強制する) です。

* *TableSources*: 検索の一部として使用する "ワイルドカード" テーブル名のコンマ区切りのリストです (省略可能)。
  この一覧には、 [union 演算子](unionoperator.md)の一覧と同じ構文があります。
  オプションの *TabularSource*と一緒に表示することはできません。

* *Searchpredicate*: 検索対象を定義する必須の述語です (つまり、入力内のすべてのレコードに対して評価されるブール式であり、返される場合、 `true` レコードは出力されます)。 *Searchpredicate* の構文は、ブール式の通常の Kusto 構文を拡張し、次のように変更します。

  **文字列照合拡張機能**: *searchpredicate* の用語として表示される文字列リテラルは `has` 、、、 `hasprefix` `hassuffix` 、および反転 ( `!` )、または大文字と小文字を区別する ( `sc` ) バージョンの演算子を使用して、すべての列とリテラルの用語が一致することを示します。 、、またはを適用するかどうかは、 `has` `hasprefix` `hassuffix` リテラルの開始または終了 (またはその両方) がアスタリスク () であるかどうかによって決まり `*` ます。 リテラル内のアスタリスクは使用できません。

    |リテラル   |演算子   |
    |----------|-----------|
    |`billg`   |`has`      |
    |`*billg`  |`hassuffix`|
    |`billg*`  |`hasprefix`|
    |`*billg*` |`contains` |
    |`bi*lg`   |`matches regex`|

  **列の制限**: 既定では、文字列照合拡張機能は、データセットのすべての列に対して照合を試みます。 次の構文を使用して、この照合を特定の列に限定することができます: *ColumnName* `:` *stringliteral*。

  **文字列の等価性**: 文字列値に対する列の完全一致 (用語一致ではなく) は、 *ColumnName* `==` *stringliteral*構文を使用して行うことができます。

  **その他のブール式**: すべての正規 Kusto ブール式は、構文によってサポートされています。
    たとえば、 `"error" and x==123` は、いずれかの列に用語が含まれ、 `error` 列に値があるレコードを検索し `123` `x` ます。

  **Regex の一致**: 正規表現の一致は、stringliteral で表現される構文*を使用し*て示されます `matches regex` *StringLiteral* 。 *stringliteral*は、regex パターンです。

*TabularSource*と*TableSources*の両方を省略した場合、スコープ内のデータベースの制限のないすべてのテーブルとビューに対して検索が実行されることに注意してください。

## <a name="summary-of-string-matching-extensions"></a>文字列一致拡張機能の概要

  |# |構文                                 |意味 (同等 `where` )           |説明|
  |--|---------------------------------------|---------------------------------------|--------|
  | 1|`search "err"`                         |`where * has "err"`                    ||
  | 2|`search in (T1,T2,A*) and "err"`       |<code>union T1,T2,A* &#124; where * has "err"<code>   ||
  | 3|`search col:"err"`                     |`where col has "err"`                  ||
  | 4|`search col=="err"`                    |`where col=="err"`                     ||
  | 5|`search "err*"`                        |`where * hasprefix "err"`              ||
  | 6|`search "*err"`                        |`where * hassuffix "err"`              ||
  | 7|`search "*err*"`                       |`where * contains "err"`               ||
  | 8|`search "Lab*PC"`                      |`where * matches regex @"\bLab.*PC\b"`||
  | 9|`search *`                             |`where 0==0`                           ||
  |10|`search col matches regex "..."`       |`where col matches regex "..."`        ||
  |11|`search kind=case_sensitive`           |                                       |すべての文字列比較で大文字と小文字が区別されます|
  |12|`search "abc" and ("def" or "hij")`    |`where * has "abc" and (* has "def" or * has hij")`||
  |13|`search "err" or (A>a and A<b)`        |`where * has "err" or (A>a and A<b)`   ||

## <a name="remarks"></a>解説

[検索演算子](findoperator.md)**とは異なり**、 `search` 演算子は次の機能をサポートしていません。

1. `withsource=`: 出力には、各レコードを取得したテーブル名を値とする型の列が常に含まれます `$table` `string` (ソースがテーブルではなく、複合式である場合は、システムによって生成された名前です)。
2. `project=`、 `project-smart` : 出力スキーマは出力スキーマに相当し `project-smart` ます。

## <a name="examples"></a>例

```kusto
// 1. Simple term search over all unrestricted tables and views of the database in scope
search "billg"

// 2. Like (1), but looking only for records that match both terms
search "billg" and ("steveb" or "satyan")

// 3. Like (1), but looking only in the TraceEvent table
search in (TraceEvent) and "billg"

// 4. Like (2), but performing a case-sensitive match of all terms
search "BillB" and ("SteveB" or "SatyaN")

// 5. Like (1), but restricting the match to some columns
search CEO:"billg" or CSA:"billg"

// 6. Like (1), but only for some specific time limit
search "billg" and Timestamp >= datetime(1981-01-01)

// 7. Searches over all the higher-ups
search in (C*, TF) "billg" or "davec" or "steveb"

// 8. A different way to say (7). Prefer to use (7) when possible
union C*, TF | search "billg" or "davec" or "steveb"
```

## <a name="performance-tips"></a>パフォーマンスに関するヒント

  |# |ヒント                                                                                  |Prefer                                        |Over                                                                    |
  |--|-------------------------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------|
  | 1| 複数の連続する演算子に対して1つの演算子を使用することを優先します。 `search` `search`|`search "billg" and ("steveb" or "satyan")`   |<code>search "billg" &#124; search "steveb" or "satyan"<code>           ||
  | 2| 演算子内のフィルター処理を優先します `search`                                       |`search "billg" and "steveb"`                 |<code>search * &#124; where * has "billg" and * has "steveb"<code>      ||
