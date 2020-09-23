---
title: reduce 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの reduce 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d844f693b1509a823702b12bd28b85a9f19a07bd
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102895"
---
# <a name="reduce-operator"></a>reduce 演算子

値の類似性に基づいて、一連の文字列をまとめてグループ化します。

```kusto
T | reduce by LogMessage with threshold=0.1
```

このようなグループごとに、グループについて最もよく説明する **パターン** (たとえば、 `*` ワイルドカードを表すためにアスタリスク () 文字を使用する)、グループ内の値の **数、** グループの **代表者** (グループ内の元の値の1つ) が出力されます。

## <a name="syntax"></a>構文

*T* `|` `reduce` [ `kind` `=` *reducekind*] `by` *Expr* [ `with` [ `threshold` `=` *しきい値*] [ `,` `characters` `=` *文字*]]

## <a name="arguments"></a>引数

* *Expr*: 値に評価される式 `string` 。
* *Threshold*: `real` 範囲 (0 ..1) のリテラル。 既定値は0.1 です。 入力のサイズが大きい場合は、しきい値を小さくする必要があります。 
* *文字*: `string` 語句を改行しない文字の一覧に追加する文字の一覧を含むリテラル。 (たとえば、とをそれぞれ1つの語句にする場合は、とを改行するので `aaa=bbbb` `aaa:bbb` はなく、 `=` `:` `":="` 文字列リテラルとしてを使用します)。
* *Reducekind*: 縮小フレーバーを指定します。 時間の有効な値は、のみです `source` 。

## <a name="returns"></a>戻り値

この演算子は、3つの列 ( `Pattern` 、 `Count` 、および `Representative` ) と、グループと同じ数の行を含むテーブルを返します。 `Pattern` は、グループのパターン値で、 `*` ワイルドカードとして使用されています (任意の挿入文字列を表します)。は、 `Count` 演算子への入力の行数をカウントし `Representative` ます。これは、このグループに含まれる入力の1つの値です。

を指定した場合は、 `[kind=source]` 既存の `Pattern` テーブル構造に列が追加されます。
構文では、このフレーバーのスキーマが将来の変更の対象になっている可能性があることに注意してください。

たとえば、 `reduce by city` の結果には次のものが含まれます。 

|パターン     |Count |Representative|
|------------|------|--------------|
| San *      | 5182 |San Bernard   |
| Saint *    | 2846 |サン Lucy    |
| Moscow     | 3726 |Moscow        |
| \* -on- \* | 2730 |一対一  |
| Paris      | 2716 |Paris         |

トークン化をカスタマイズした別の例を次に示します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 1000 step 1
| project MyText = strcat("MachineLearningX", tostring(toint(rand(10))))
| reduce by MyText  with threshold=0.001 , characters = "X" 
```

|パターン         |Count|Representative   |
|----------------|-----|-----------------|
|各ラーニング *|1000 |MachineLearningX4|

## <a name="examples"></a>例

次の例は、"サニタイズされた" 入力に演算子を適用する方法を示しています。 `reduce` これは、縮小される列の guid が減少する前に置き換えられることを示しています。

```kusto
// Start with a few records from the Trace table.
Trace | take 10000
// We will reduce the Text column which includes random GUIDs.
// As random GUIDs interfere with the reduce operation, replace them all
// by the string "GUID".
| extend Text=replace(@"[[:xdigit:]]{8}-[[:xdigit:]]{4}-[[:xdigit:]]{4}-[[:xdigit:]]{4}-[[:xdigit:]]{12}", @"GUID", Text)
// Now perform the reduce. In case there are other "quasi-random" identifiers with embedded '-'
// or '_' characters in them, treat these as non-term-breakers.
| reduce by Text with characters="-_"
```

## <a name="see-also"></a>関連項目

[autocluster](./autoclusterplugin.md)

**メモ**

演算子の実装 `reduce` は、主に、Risto Vaarandi によって [イベントログからマイニングパターンを作成するためのデータクラスターアルゴリズム](https://ristov.github.io/publications/slct-ipom03-web.pdf)です。
