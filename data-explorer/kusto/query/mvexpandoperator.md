---
title: mv-拡張演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの mv-expand 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: de0b2696ba5974a7261b4f397214e3ade59fc9cb
ms.sourcegitcommit: 31af2dfa75b5a2f59113611cf6faba0b45d29eb5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454101"
---
# <a name="mv-expand-operator"></a>mv-expand 演算子

複数値の配列またはプロパティバッグを展開します。

`mv-expand`は、[動的](./scalar-data-types/dynamic.md)に型指定された列に適用されます。これにより、コレクション内の各値が個別の行を取得できるようになります。 展開された行内のその他の列はすべて複製されます。 

**構文**

*T* `| mv-expand ` [ `bagexpansion=` ( `bag`  |  `array` )] [ `with_itemindex=` *indexcolumnname*] *ColumnName* [ `,` *columnname* ...] [ `limit` *rowlimit*]

*T* `| mv-expand ` [ `bagexpansion=` ( `bag`  |  `array` )] [*Name* `=` ] *arrayexpression* [ `to typeof(` *typename* `)` ] [, [*Name* `=` ] *arrayexpression* [ `to typeof(` *typename* `)` ]...] [ `limit` *rowlimit*]

**引数**

* *ColumnName:* 結果では、指定された列内の配列が複数の行に展開されます。 
* *ArrayExpression:* 配列を生成する式。 この形式を使用した場合は、新しい列が追加され、既存の列は保持されます。
* *Name:* 新しい列の名前。
* *Typename:* 配列の要素の基になる型を示します。これは、演算子によって生成される列の型になります。 配列内の準拠していない値は変換されません。 代わりに、これらの値は値になり `null` ます。
* *RowLimit:* 元の各行から生成される行の最大数。 既定値は2147483647です。 
  > [!Note] 
  > 演算子のレガシおよび旧形式では、行の上限として128が設定されてい `mvexpand` ます。
* *Indexcolumnname:*`with_itemindex`を指定した場合、出力には、最初に展開されたコレクション内の項目のインデックス (0 から始まる) を含む追加の列 ( *indexcolumnname*) が含まれます。 

**戻り値**

指定された列または配列式に含まれる配列内の値ごとに複数の行。
複数の列または式を指定した場合は、並列に拡張されます。 入力行ごとに、最も長い展開された式に含まれる要素の数と同数の出力行があります (短いリストには null が埋め込まれます)。 行の値が空の配列の場合、行は何も展開されません (結果セットには表示されません)。 ただし、行の値が配列でない場合、その行は結果セットのままになります。 

展開された列は常に動的な型を持ちます。 値を計算または集計する場合は、`todatetime()` や `tolong()` などのキャストを使います。

2 つのモードのプロパティ バッグの展開がサポートされています。
* `bagexpansion=bag`: プロパティ バッグは、単一エントリのプロパティ バッグに展開されます。 このモードは、既定の展開です。
* `bagexpansion=array`: プロパティバッグは2要素の `[` *キー* `,` *値*配列構造に展開され `]` 、キーと値への一貫したアクセスを可能にします (たとえば、プロパティ名に対して個別のカウントの集計を実行します)。 

**例**

1つの列の単純な展開:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|{"prop1": "a"}|
|1|{"prop2": "b"}|

2つの列を展開すると、該当する列が最初に "zip" され、次に展開されます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b, c 
```

|a|b|c|
|---|---|---|
|1|{"prop1": "a"}|5|
|1|{"prop2": "b"}||

2つの列を展開するデカルト積を取得する場合は、もう一方を展開します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b 
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|{"prop1": "a"}|5|
|1|{"prop2": "b"}|5|


配列の展開に使用する `with_itemindex` もの:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 4 step 1 
| summarize x = make_list(x) 
| mv-expand with_itemindex=Index  x 
```

|x|インデックス|
|---|---|
|1|0|
|2|1|
|3|2|
|4|3|


**その他の例**

[時間の経過に伴うライブアクティビティの数を](./samples.md#chart-concurrent-sessions-over-time)ご覧ください。

**参照**

- [mv-apply](./mv-applyoperator.md)演算子。
- [make_list () を集計](makelist-aggfunction.md)します。これにより、逆の関数が行われます。
- プロパティバッグキーを使用して動的 JSON オブジェクトを列に拡張するための[bag_unpack ()](bag-unpackplugin.md)プラグイン。
