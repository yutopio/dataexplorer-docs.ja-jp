---
title: mv-expand 演算子 - Azure Data Explorer
description: この記事では、Azure Data Explorer の mv-expand 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.localizationpriority: high
ms.openlocfilehash: e439fff119e005e44a0649fe22cadf3614ce036d
ms.sourcegitcommit: 555f3da35fe250fabd35fcc6014bf055ef8405db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972500"
---
# <a name="mv-expand-operator"></a>mv-expand 演算子

複数の値の配列またはプロパティ バッグを展開します。

`mv-expand` は、[dynamic](./scalar-data-types/dynamic.md) 型の配列またはプロパティ バッグ列に適用され、コレクション内の各値が個別の行になります。 展開された行内のその他の列はすべて複製されます。 

## <a name="syntax"></a>構文

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [`with_itemindex=`*IndexColumnName*] *ColumnName* [`,` *ColumnName* ...] [`limit` *Rowlimit*]

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] [, [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] ...] [`limit` *Rowlimit*]

## <a name="arguments"></a>引数

* *ColumnName:* 結果では、指定された列内の配列が複数の行に展開されます。 
* *ArrayExpression:* 配列を生成する式。 この形式を使用した場合は、新しい列が追加され、既存の列は保持されます。
* *Name:* 新しい列の名前。
* *Typename:* 配列の要素の基になる型を示します。これは、`mv-expand` 演算子によって生成される列の型になります。 型を適用する操作はキャストのみであり、解析や型変換は含まれません。 宣言されている型に準拠しない配列要素は `null` 値になります。
* *RowLimit:* 元の各行から生成される行の最大数。 既定値は 2147483647 です。 

  > [!NOTE]
  > `mvexpand` は、`mv-expand` 演算子の古いレガシ形式です。 レガシ バージョンでの既定の行の制限は 128 です。

* *IndexColumnName:* `with_itemindex` を指定すると、出力に追加の列 (*IndexColumnName* という名前) が含まれます。それには、元の展開されたコレクション内の項目のインデックス (0 から始まる) が含まれます。 

## <a name="returns"></a>戻り値

指定した列の配列または配列式の各値に対応する複数の行。
複数の列または式を指定した場合は、並列に展開されます。 入力行ごとに、展開された最も長い式に含まれる要素の数と同数の出力行が存在します (短いリストには null が埋め込まれます)。 行の値が空の配列の場合、行は展開されません (結果セットには表示されません)。 ただし、行の値が配列でない場合、その行は結果セットのままになります。 

展開された列は常に動的な型を持ちます。 値を計算または集計する場合は、`todatetime()` や `tolong()` などのキャストを使います。

2 つのモードのプロパティ バッグの展開がサポートされています。
* `bagexpansion=bag` または `kind=bag`: プロパティ バッグは、単一エントリのプロパティ バッグに展開されます。 このモードが既定の展開です。
* `bagexpansion=array` または `kind=array`: プロパティ バッグは 2 要素 (`[`*key*`,`*value*`]`) の配列構造に展開されるため、キーと値への一貫したアクセスが可能です (プロパティ名での個別のカウントの集計など)。 

## <a name="examples"></a>例

### <a name="single-column"></a>1 つの列

1 つの列の単純な展開:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|{"prop1":"a"}|
|1|{"prop2":"b"}|

### <a name="zipped-two-columns"></a>圧縮された 2 つの列

2 つの列を展開すると、該当する列が最初に "zip" され、次に展開されます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5, 4, 3])]
| mv-expand b, c
```

|a|b|c|
|---|---|---|
|1|{"prop1":"a"}|5|
|1|{"prop2":"b"}|4|
|1||3|

### <a name="cartesian-product-of-two-columns"></a>2 つの列のデカルト積

展開する 2 つの列のデカルト積を取得する場合は、一方を展開した後で他方を展開します。

<!-- csl: https://kuskusdfv3.kusto.windows.net/Kuskus -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)
  [
  1,
  dynamic({"prop1":"a", "prop2":"b"}),
  dynamic([5, 6])
  ]
| mv-expand b
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|{  "prop1": "a"}|5|
|1|{  "prop1": "a"}|6|
|1|{  "prop2": "b"}|5|
|1|{  "prop2": "b"}|6|

### <a name="convert-output"></a>出力の変換

mv-expand の出力を特定の型に強制的に展開する場合は (既定値は dynamic)、`to typeof` を使用します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:string, b:dynamic, c:dynamic)["Constant", dynamic([1,2,3,4]), dynamic([6,7,8,9])]
| mv-expand b, c to typeof(int)
| getschema 
```

ColumnName|ColumnOrdinal|DateType|[列の型]
-|-|-|-
a|0|System.String|string
b|1|System.Object|動的
c|2|System.Int32|INT

列 `b` は `dynamic` になり、`c` は `int` になることに注意してください。

### <a name="using-with_itemindex"></a>with_itemindex の使用

`with_itemindex` による配列の展開:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 4 step 1
| summarize x = make_list(x)
| mv-expand with_itemindex=Index x
```

|x|インデックス|
|---|---|
|1|0|
|2|1|
|3|2|
|4|3|
 
## <a name="see-also"></a>関連項目

* 他の例については、[経時的なライブ アクティビティの数のグラフ化](./samples.md#chart-concurrent-sessions-over-time)に関するページを参照してください。
* [mv-apply](./mv-applyoperator.md) 演算子。
* [summarize make_list()](makelist-aggfunction.md) は、mv-expand の逆の関数です。
* [bag_unpack()](bag-unpackplugin.md) は、プロパティ バッグ キーを使用して動的 JSON オブジェクトを列に展開するためのプラグインです。
