---
title: オペレーターを削減する - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのオペレーターの削減について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33d4ac202b61fdaa1b92291407cdd2783d947c6e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510508"
---
# <a name="reduce-operator"></a>reduce 演算子

値の類似性に基づいて、文字列のセットをグループ化します。

```kusto
T | reduce by LogMessage with threshold=0.1
```

このようなグループごとに、グループを最もよく記述する**パターン**(ワイルドカードを表す asterix (`*`) 文字を使用する) 、グループ内の値の数の**カウント**、およびグループの**代表**(グループ内の元の値の 1 つ) を出力します。

**構文**

*T* `|` `,` `characters` `=` *Characters* *Expr* `with` `threshold` `=` *Threshold* *ReduceKind* `by` [`kind` `=` ReduceKind ] [ [ しきい値 ] [ 文字 ] `reduce`

**引数**

* *Expr*:`string`値に評価される式。
* *しきい値* `real` : 範囲内のリテラル (0..1)。 デフォルトは 0.1 です。 入力のサイズが大きい場合は、しきい値を小さくする必要があります。 
* *文字*:`string`用語を区切らない文字のリストに追加する文字のリストを含むリテラル。 (たとえば、各用語を中断`aaa=bbbb`するのではなく`aaa:bbb`、各用語を含めたい場合`=`や`:`、文字列リテラルとして使用`":="`します。
* *ReduceKind*: 味を減らす値を指定します。 時間の有効な値は、 だけです`source`。

**戻り値**

この演算子は、3 つの列`Pattern`( `Count`、 `Representative`、、および ) と、グループがある行の数のテーブルを返します。 `Pattern`は、グループのパターン値で`*`、ワイルドカード (任意の挿入文字列を表す) として`Count`使用され、演算子への入力の行数がこのパターンで表される数をカウント`Representative`し、このグループに含まれる入力の 1 つの値です。

指定`[kind=source]`した場合、演算子は既存の`Pattern`テーブル構造に列を追加します。
このフレーバーのスキーマの構文は、将来の変更の影響を受ける可能性があることに注意してください。

たとえば、 `reduce by city` の結果には次のものが含まれます。 

|パターン     |Count |Representative|
|------------|------|--------------|
| San *      | 5182 |サンベルナール   |
| Saint *    | 2846 |セントルーシー    |
| Moscow     | 3726 |Moscow        |
| \* -on- \* | 2730 |ワンオン- ワン  |
| Paris      | 2716 |Paris         |

カスタマイズされたトークン化を使用する別の例:

```kusto
range x from 1 to 1000 step 1
| project MyText = strcat("MachineLearningX", tostring(toint(rand(10))))
| reduce by MyText  with threshold=0.001 , characters = "X" 
```

|パターン         |Count|Representative   |
|----------------|-----|-----------------|
|機械学習*|1000 |マシンラーニングX4|

**使用例**

次の例は、減らす前に`reduce`減らされる列の GUID を置き換える「サニタイズ」入力にオペレータを適用する方法を示しています

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

**参照**

[autocluster](./autoclusterplugin.md)

**メモ**

オペレータの`reduce`実装は、主にリスト・ヴァーランドによる[、イベント・ログからのマイニング・パターンのためのデータ・クラスタリング・アルゴリズム](https://ristov.github.io/publications/slct-ipom03-web.pdf)に基づいています。