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
ms.openlocfilehash: 956c24fa70df89f5bf99d4de12a8b07da6cb6912
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359983"
---
# <a name="mv-expand-operator"></a>mv-expand 演算子

複数の値の動的配列またはプロパティ バッグを複数のレコードに展開します。

`mv-expand` は、複数の値を 1 つの[動的](./scalar-data-types/dynamic.md)に型指定された配列またはプロパティ バッグ (`summarize` ... `make-list()` や `make-series` など) にパックする集計演算子の反対のものとして説明できます。
(スカラー) 配列またはプロパティ バッグ内の各要素により、演算子の出力に新しいレコードが生成されます。 展開されない入力のすべての列は、出力のすべてのレコードに複製されます。

## <a name="syntax"></a>構文

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [`with_itemindex=`*IndexColumnName*] *ColumnName* [`to typeof(` *Typename*`)`] [`,` *ColumnName* ...] [`limit` *Rowlimit*]

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] *Name* `=` *ArrayExpression* [`to typeof(`*Typename*`)`] [, [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] ...] [`limit` *Rowlimit*]

## <a name="arguments"></a>引数

* *ColumnName*、*ArrayExpression*:配列またはプロパティ バッグを保持する `dynamic` 型の値を持つ列参照またはスカラー式。 配列またはプロパティ バッグの個々の最上位レベルの要素は、複数のレコードに展開されます。<br>
  *ArrayExpression* が使用され、*Name* がいずれの入力列名とも等しくない場合、展開された値は出力の新しい列に拡張されます。
  それ以外の場合は、既存の *ColumnName* が置き換えられます。

* *Name:* 新しい列の名前。

* *Typename:* 配列の要素の基になる型を示します。これは、`mv-expand` 演算子によって生成される列の型になります。 型を適用する操作はキャストのみであり、解析や型変換は含まれません。 宣言されている型に準拠しない配列要素は `null` 値になります。

* *RowLimit:* 元の各行から生成される行の最大数。 既定値は 2147483647 です。 

  > [!NOTE]
  > `mvexpand` は、`mv-expand` 演算子の古いレガシ形式です。 レガシ バージョンでの既定の行の制限は 128 です。

* *IndexColumnName:* `with_itemindex` を指定すると、出力に別の列 (*IndexColumnName* という名前) が含まれます。それには、元の展開されたコレクション内の項目のインデックス (0 から始まる) が含まれます。 

## <a name="returns"></a>戻り値

入力の各レコードについて、演算子は、次の方法で決定されるように、出力で 0 個、1 個、または多数のレコードを返します。

1. 展開されない入力列は、元の値で出力に表示されます。
   1 つの入力レコードが複数の出力レコードに展開された場合、その値はすべてのレコードに複製されます。

1. 展開される個々の *ColumnName* または *ArrayExpression* について、出力レコードの数は、[下記](#modes-of-expansion)の説明のように、それぞれの値に対して決定されます。 入力レコードごとに、出力レコードの最大数が計算されます。 すべての配列またはプロパティ バッグは "並行して" 展開され、欠損値 (存在する場合) は null 値に置き換えられます。

1. 動的な値が null の場合、その値 (null) に対して 1 つのレコードが生成されます。
   動的な値が空の配列またはプロパティ バッグの場合、その値に対してレコードは生成されません。
   それ以外の場合は、動的な値に含まれる要素と同数のレコードが生成されます。

展開された列は、`to typeof()` 句を使用して明示的に型指定されている場合を除き、`dynamic` 型です。

### <a name="modes-of-expansion"></a>展開のモード

2 つのモードのプロパティ バッグの展開がサポートされています。

* `bagexpansion=bag` または `kind=bag`: プロパティ バッグは、単一エントリのプロパティ バッグに展開されます。 これは既定のモードです。
* `bagexpansion=array` または `kind=array`: プロパティ バッグは 2 要素 (`[`*key*`,`*value*`]`) の配列構造に展開されるため、キーと値への一貫したアクセスが可能です。 このモードでは、たとえば、プロパティ名での個別のカウントの集計を実行することもできます。 

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

mv-expand の出力を特定の型に強制的に展開するには (既定値は dynamic)、`to typeof` を使用します。

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

列 `b` は `dynamic` として返されますが、`c` は `int` として返されることに注意してください。

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
