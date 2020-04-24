---
title: ルックアップ演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの検索演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 1325a1b839b62baacade4cf7c64cd278aeeabaf5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513058"
---
# <a name="lookup-operator"></a>lookup 演算子

この`lookup`演算子は、ディメンション テーブル内で値を検索してファクト テーブルの列を拡張します。

```kusto
FactTable | lookup kind=leftouter (DimensionTable) on CommonColumn, $left.Col1 == $right.Col2
```

ここで、結果は、前のテーブルの各`FactTable`ペア`$left`( , `DimensionTable` ) を`$right`、後者のテーブルの各ペア`CommonColumn`(`Col``CommonColumn1`,`Col2`) で検索することによって、 ( によって参照される ) からのデータを使用して ( ) を拡張するテーブルです。 ファクト テーブルとディメンション テーブルの違いについては、[ファクト テーブルとディメンション テーブル](../concepts/fact-and-dimension-tables.md)を参照してください。 

演算子`lookup`は、次の相違点を[持つ結合演算子](joinoperator.md)と同様の操作を実行します。

* 結果は、結合操作の`$right`基礎となるテーブルの列を繰り返しません。
* では、2 種類の検索のみが`leftouter`サポート`inner`され、`leftouter`既定の検索がサポートされます。
* パフォーマンスの観点から見ると、デフォルトでは`$left`、テーブルが大きい (ファクト) テーブルが、`$right`テーブルが小さい (ディメンションの) テーブルであると仮定します。 これは、演算子が使用する仮定とは正`join`反対です。
* オペレーター`lookup`は、表を`$right``$left`表に自動的にブロードキャストします (基本的には、指定`hint.broadcast`されたかのように動作します)。 これはテーブルのサイズを制限していることに`$right`注意してください。

**構文**

*左テーブル*`|``lookup``kind` `=` [`leftouter`|`inner`( `(` ]*右テーブル属性*`)``on`*Attributes*

**引数**

* *左テーブル*: ルックアップの基礎となるテーブルまたは表形式の式。
  として表記`$left`されます。

* *RightTable*: ファクト テーブルの新しい列を "設定" するために使用されるテーブルまたは表形式の式。 として表記`$right`されます。

* *属性*: *LeftTable*の行が*RightTable*の行とどのように一致するかを示す 1 つ以上の規則のコンマ区切りのリスト。 論理演算子を使用して複数`and`の規則が評価されます。
  ルールは次のいずれかになります。

  |ルールの種類        |構文                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |名前による等しい |*[ColumnName]*                                    |`where`*左テーブル*.*列名*`==`*の右テーブル*。*列名*|
  |値による等しい値|`$left.`*左列*`==``$right.`*右列*|`where``$left.`*左*`==`列`$right.`*右列        |

  > [!Note] 
  > '値による等しい' の場合、列名は、表記と`$right`表記で示される適切な所有者テーブル`$left`で修飾*する必要があります*。

* `kind`: *LeftTable*で一致しない行を処理する方法に関する*RightTable*省略可能な命令です。 既定では、`leftouter`これらの行はすべて、演算子によって追加された*RightTable*列の欠損値に使用される NULL 値を持つ出力に表示されます。 使用`inner`される場合、そのような行は出力から除外されます。 (他の種類の結合は p`looku`演算子ではサポートされません。
  
**戻り値**

次の要素が含まれるテーブル:

* 照合キーを含め、2 つのテーブルそれぞれのすべての列に対応する列。
  名前の競合がある場合、右側の列の名前は自動的に変更されます。
* 入力テーブル間でのすべての一致に対応する行。 片方のテーブルから選択された、もう一方のテーブルに含まれる 1 つの行とすべての `on` フィールドの値が同じである行です。 
* 属性 (ルックアップ キー) は、出力テーブルに 1 回だけ表示されます。

 * `kind`未指定`kind=leftouter`

     内部の一致のほかに、左側と右側のどちらか一方または両方のすべての行に対応する行が含まれます (一致するものがない場合も)。 その場合、一致しない出力セルには null が含まれます。

 * `kind=inner`

     出力には、左右の一致行のすべての組み合わせに対応する行が含まれます。

**使用例**

```kusto
let FactTable=datatable(Row:string,Personal:string,Family:string) [
  "1", "Bill",   "Gates",
  "2", "Bill",   "Clinton",
  "3", "Bill",   "Clinton",
  "4", "Steve",  "Ballmer",
  "5", "Tim",    "Cook"
];
let DimTable=datatable(Personal:string,Family:string,Alias:string) [
  "Bill",  "Gates",   "billg",
  "Bill",  "Clinton", "billc",
  "Steve", "Ballmer", "steveb",
  "Tim",   "Cook",    "timc"
];
FactTable
| lookup kind=leftouter DimTable on Personal, Family
```

行     | Personal  | ファミリ   | エイリアス
--------|-----------|----------|--------
1       | Bill      | ゲート    | 法案
2       | Bill      | クリントン  | ビルク
3       | Bill      | クリントン  | ビルク
4       | Steve     | バルマー 氏  | Steveb
5       | ティム       | 調理     | ティムク