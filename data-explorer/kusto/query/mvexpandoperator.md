---
title: mv-expand オペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの mv-expand 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: 01a4b56155d1c29727670b4e827cb7b9968ef7a9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512293"
---
# <a name="mv-expand-operator"></a>mv-expand 演算子

複数値配列またはプロパティ バッグを展開します。

`mv-expand`は[動的](./scalar-data-types/dynamic.md)型付き列に適用され、コレクション内の各値が個別の行を取得します。 展開された行内のその他の列はすべて複製されます。 

**構文**

`bag` |  *T* `| mv-expand ` `array``with_itemindex=``,` *ColumnName* *ColumnName* *IndexColumnName*[] [ ] [ インデックス列名 ] 列名 [ 列名 ..]`bagexpansion=`[`limit` *行限度*]

*T* `| mv-expand ` `bagexpansion=`[`bag` | `array`] [ ]`to typeof(`[*名前*`=`]*配列式*[`to typeof(`*型名*`)`] [ [ [ 名前 *]* `=`*配列式*[*型名*`)`] ..[`limit` *行限度*]

**引数**

* *ColumnName:* 結果では、指定された列内の配列が複数の行に展開されます。 
* *ArrayExpression:* 配列を生成する式。 この形式を使用した場合は、新しい列が追加され、既存の列は保持されます。
* *Name:* 新しい列の名前。
* *タイプ名:* 配列の要素の基になる型を示します。
    この型に準拠していない配列の値は変換されません。むしろ、彼らは価値を`null`引き受けるでしょう。
* *RowLimit:* 元の各行から生成される行の最大数。 デフォルトは 2147483647 です。 
*注*: 従来の形式と古い`mvexpand`形式の演算子の行の制限は、既定では 128 です。
* *インデックス列名:* 指定`with_itemindex`すると、元の展開されたコレクション内の項目のインデックス (0 から始まる) を含む追加の列 *(IndexColumnName*という名前) が出力に含まれます。 

**戻り値**

指定された列の配列か、配列の式の各値に対応する複数の行。
複数の列または式が指定されている場合、それらは並列に展開されるため、各入力行に対して、最長の展開式内の要素と同じ数の出力行が存在します (短いリストには NULL が埋め込まれます)。 行の値が空の配列の場合、行は何も表示されません (結果セットには表示されません)。 行の値が配列でない場合、その行は結果セット内のそのままの状態で保持されます。 

展開された列は常に動的な型を持ちます。 値を計算または集計する場合は、`todatetime()` や `tolong()` などのキャストを使います。

2 つのモードのプロパティ バッグの展開がサポートされています。
* `bagexpansion=bag`: プロパティ バッグは、単一エントリのプロパティ バッグに展開されます。 これが既定の展開です。
* `bagexpansion=array`: プロパティ バッグは 2`[`要素の*キー*`,`*値*`]`配列構造に展開され、キーと値に一様にアクセスできます (プロパティ名に対して個別の集計を実行するなど)。 

**使用例**

単一列の単純な展開:
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|{"prop1":"a"}|
|1|{"prop2":"b"}|


2 つの列を展開すると、まず該当する列が zip に zip され、次に展開されます。

```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b, c 
```

|a|b|c|
|---|---|---|
|1|{"prop1":"a"}|5|
|1|{"prop2":"b"}||

2 つの列を展開するデカルト積を取得する場合は、次の順に展開します。
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b 
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|{"prop1":"a"}|5|
|1|{"prop2":"b"}|5|


を使用して配列を`with_itemindex`拡張する:
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

時間[経過に関するライブアクティビティのグラフ数を](./samples.md#concurrent-activities)参照してください。

**参照**

- [mv-apply](./mv-applyoperator.md)演算子。
- [make_list()を要約](makelist-aggfunction.md)して、反対の関数を実行します。
- [bag_unpack()](bag-unpackplugin.md)プラグインは、プロパティバッグキーを使用して動的 JSON オブジェクトをカラムに拡張します。