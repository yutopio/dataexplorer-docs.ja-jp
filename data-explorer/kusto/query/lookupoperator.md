---
title: lookup 操作-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの lookup 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 5eda79977ee641d7ca7835d3d394cb943b4ebac4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347053"
---
# <a name="lookup-operator"></a>lookup 演算子

操作は、 `lookup` ディメンションテーブルで検索された値を使用して、ファクトテーブルの列を拡張します。

```kusto
FactTable | lookup kind=leftouter (DimensionTable) on CommonColumn, $left.Col1 == $right.Col2
```

ここでは、() の各ペア (、) を元のテーブルから、2番目のテーブルの各ペア (,) `FactTable` `$left` に対し `DimensionTable` `$right` て参照を実行することによって (が参照) を使用して () を `CommonColumn` `Col` `CommonColumn1` 拡張する `Col2` テーブルが生成されます。 ファクトテーブルとディメンションテーブルの違いについては、「[ファクトテーブルとディメンションテーブル](../concepts/fact-and-dimension-tables.md)」を参照してください。 

`lookup`演算子は[join 演算子](joinoperator.md)と同様の操作を実行しますが、次のような違いがあります。

* この結果では、 `$right` 結合操作の基礎となるテーブルの列は繰り返されません。
* 2種類の参照のみがサポートされて `leftouter` おり `inner` 、 `leftouter` 既定値はです。
* パフォーマンスの観点から、既定では、システムは `$left` テーブルが大きな (ファクト) テーブルであり、 `$right` テーブルが小さい (ディメンション) テーブルであると想定しています。 これは、演算子で使用される仮定と完全に逆です `join` 。
* 演算子はテーブル `lookup` をテーブルに自動的にブロードキャストし `$right` `$left` ます (基本的には、が指定されている場合と同様に動作し `hint.broadcast` ます)。 これにより、テーブルのサイズが制限されることに注意 `$right` してください。

## <a name="syntax"></a>構文

*左テーブル* `|``lookup`[ `kind` `=` ( `leftouter` | `inner` )] `(` *右テーブル* `)` `on` *属性*

## <a name="arguments"></a>引数

* *左テーブル*: 参照の基礎となるテーブルまたは表形式の式。
  として表さ `$left` れます。

* [*右テーブル*]: ファクトテーブルの新しい列を "設定" するために使用されるテーブルまたは表形式の式です。 として表さ `$right` れます。

* *属性*:*左テーブル*から行を*右テーブル*の行と照合する方法を記述する1つ以上のルールのコンマ区切りの一覧です。 複数のルールは、論理演算子を使用して評価され `and` ます。
  ルールには次のいずれかを指定できます。

  |ルールの種類        |構文                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |名前による等値 |*[ColumnName]*                                    |`where`*左テーブル*。*ColumnName* `==`*右テーブル*。*ColumnName*|
  |値による等値|`$left.`*左の列* `==``$right.`*右の列*|`where``$left.`*左の列* `==` `$right.`* 右列        |

  > [!Note] 
  > "値による等値" の場合、列名はと表記で示される適用可能な所有者テーブルで修飾*する必要があり* `$left` `$right` ます。

* `kind`:*右テーブル*で一致しない行を*左テーブル*に処理する方法に関する省略可能な命令です。 既定で `leftouter` は、が使用されます。これは、これらのすべての行が、演算子によって追加された*右テーブル*の列の欠損値に使用される null 値で出力に表示されることを意味します。 `inner`が使用されている場合、このような行は出力から除外されます。 (他の種類の結合は、p 演算子ではサポートされていません `looku` )。
  
## <a name="returns"></a>戻り値

次の要素が含まれるテーブル:

* 照合キーを含め、2 つのテーブルそれぞれのすべての列に対応する列。
  名前の競合がある場合は、右側の列の名前が自動的に変更されます。
* 入力テーブル間でのすべての一致に対応する行。 片方のテーブルから選択された、もう一方のテーブルに含まれる 1 つの行とすべての `on` フィールドの値が同じである行です。 
* 属性 (参照キー) は、出力テーブルに1回だけ表示されます。

 * `kind`不明`kind=leftouter`

     内部の一致のほかに、左側と右側のどちらか一方または両方のすべての行に対応する行が含まれます (一致するものがない場合も)。 その場合、一致しない出力セルには null が含まれます。

 * `kind=inner`

     出力には、左右の一致行のすべての組み合わせに対応する行が含まれます。

## <a name="examples"></a>例

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

行     | 個人用  | ファミリ   | エイリアス
--------|-----------|----------|--------
1       | Bill      | ゲート    | billg
2       | Bill      | クリントン  | billc
3       | Bill      | クリントン  | billc
4       | Steve     | Ballmer  | steveb
5       | Tim       | クック     | timc