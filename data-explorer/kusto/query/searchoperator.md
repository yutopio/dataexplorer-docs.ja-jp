---
title: 検索オペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの検索演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de63aa3fde421996809334b8a746ea4dee8cb026
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509199"
---
# <a name="search-operator"></a>search 演算子

検索演算子は、複数テーブル/複数列の検索エクスペリエンスを提供します。

## <a name="syntax"></a>構文

* [*表形式ソース*`|`]`search` [`kind=`*大文字と小文字の区別 ]*[`in` `(`*テーブルソース*`)`]*検索述語*

## <a name="arguments"></a>引数

* *表形式ソース*: テーブル名、[共用体演算子](unionoperator.md)、表形式クエリの結果など、検索対象のデータ ソースとして機能するオプションの表形式式です。*TableSources*を含むオプションのフレーズと一緒に表示することはできません。

* *大文字と小文字の区別*: 大文字と小文字の区別`string`に関してすべてのスカラー演算子の動作を制御するオプションのフラグ。 `default`有効な値は、2 つの`case_insensitive`同義語と (大文字小文字を`has`区別しない演算子の既定値) と`case_sensitive`(すべての演算子を大文字と小文字を区別する一致モードに強制します) です。

* *テーブルソース*: 検索に参加する「ワイルドカード」テーブル名のカンマ区切りリストです。
  このリストは[、union 演算子](unionoperator.md)のリストと同じ構文を持ちます。
  *オプションの表形式ソース*と一緒に表示することはできません。

* *SearchPredicate*: 検索対象を定義する必須述語 (つまり、入力内のすべてのレコードについて評価され、返された`true`場合は レコードが出力されるブール式)。*SearchPredicate*の構文は、ブール式の通常の Kusto 構文を拡張および変更します。

  **文字列一致拡張**: *SearchPredicate*で用語として出現する文字列リテラルは、これらの演算子の反転 ( `has`) `hasprefix``hassuffix`または大文字小文字を区別`!``sc`するバージョンを使用して、すべての列とリテラルの間の用語の一致を示します。 を`has`適用するか、`hasprefix`または を適用`hassuffix`するかの決定は、リテラルがアスタリスク ( )`*`で開始するか終了するか (またはその両方) であるかによって異なります。 リテラル内のアスタリスクは使用できません。

    |リテラル   |演算子   |
    |----------|-----------|
    |`billg`   |`has`      |
    |`*billg`  |`hassuffix`|
    |`billg*`  |`hasprefix`|
    |`*billg*` |`contains` |
    |`bi*lg`   |`matches regex`|

  **列の制限**: デフォルトでは、文字列一致の拡張子は、データ セットのすべての列と照合しようとします。 この一致を特定の列に制限するには、次の構文を使用します *。*`:`*StringLiteral*

  **文字列の等価性**: 列が (用語一致ではなく) 文字列値に対して完全に一致する場合は *、"列名文字列*`==`*リテラル*" という構文を使用して行うことができます。

  **その他のブール式**: すべての標準の Kusto ブール式は、構文でサポートされています。
    たとえば、`"error" and x==123`列に用語`error`が含まれているレコードを検索し、その列に値`123`を`x`含めることを意味します。

  **正規表現一致**: 正規表現の一致は *、列*`matches regex`*文字列リテラル*構文*StringLiteral*を使用して示されます。

*表形式ソース*と*テーブルソース*の両方を省略すると、スコープ内のデータベースのすべての無制限のテーブルとビューに対して検索が実行されることに注意してください。

## <a name="summary-of-string-matching-extensions"></a>文字列一致の拡張機能の概要

  |# |構文                                 |意味 (`where`相当)           |説明|
  |--|---------------------------------------|---------------------------------------|--------|
  | 1|`search "err"`                         |`where * has "err"`                    ||
  | 2|`search in (T1,T2,A*) and "err"`       |<code>union T1,T2,A* &#124; where * has "err"<code>   ||
  | 3|`search col:"err"`                     |`where col has "err"`                  ||
  | 4|`search col=="err"`                    |`where col=="err"`                     ||
  | 5|`search "err*"`                        |`where * hasprefix "err"`              ||
  | 6|`search "*err"`                        |`where * hassuffix "err"`              ||
  | 7|`search "*err*"`                       |`where * contains "err"`               ||
  | 8|`search "Lab*PC"`                      |`where * matches regex @"\bLab\w*PC\b"`||
  | 9|`search *`                             |`where 0==0`                           ||
  |10|`search col matches regex "..."`       |`where col matches regex "..."`        ||
  |11|`search kind=case_sensitive`           |                                       |すべての文字列比較では大文字と小文字が区別されます。|
  |12|`search "abc" and ("def" or "hij")`    |`where * has "abc" and (* has "def" or * has hij")`||
  |13|`search "err" or (A>a and A<b)`        |`where * has "err" or (A>a and A<b)`   ||

## <a name="remarks"></a>解説

find[演算子](findoperator.md)とは`search`**異なり**、演算子は次をサポートしていません。

1. `withsource=`: 出力には、各レコードが取得`$table`されたテーブル`string`名 (またはソースがテーブルではなく複合式の場合はシステム生成名) を持つ型と呼ばれる列が常に含まれます。
2. `project=`、 `project-smart`: 出力スキーマは出力`project-smart`スキーマと同等です。

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
  | 1| 複数の連続`search`した演算子`search`に対して単一の演算子を使用することを好む|`search "billg" and ("steveb" or "satyan")`   |<code>search "billg" &#124; search "steveb" or "satyan"<code>           ||
  | 2| 演算子の内部をフィルタリング`search`することを好む                                       |`search "billg" and "steveb"`                 |<code>search * &#124; where * has "billg" and * has "steveb"<code>      ||
