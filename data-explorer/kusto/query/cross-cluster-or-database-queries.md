---
title: 複数のデータベースにまたがるクエリとクロスクラスタークエリ-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのデータベース間およびクロスクラスタークエリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: eaf42247840bfc5446c61bcefbb205c9e49706c3
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737744"
---
# <a name="cross-database-and-cross-cluster-queries"></a>複数のデータベースに対するクエリと複数のクラスターに対するクエリ

::: zone pivot="azuredataexplorer"

すべての Kusto クエリは、現在のクラスターと既定のデータベースのコンテキストで動作します。
* [Kusto Explorer](../tools/kusto-explorer.md)では、[[接続] パネル](../tools/kusto-explorer.md#connections-panel)で既定のデータベースが選択されています。現在のクラスターは、そのデータベースを含む接続です。
* [Kusto クライアントライブラリ](../api/netfx/about-kusto-data.md)を使用する場合、現在のクラスターと既定のデータベースは`Data Source` 、 `Initial Catalog`それぞれ[kusto 接続文字列](../api/connection-strings/kusto.md)のプロパティとプロパティによって指定されます。

## <a name="queries"></a>クエリ
既定以外のデータベースからテーブルにアクセスするには、次のように*修飾名*構文を使用する必要があります。現在のクラスター内のデータベースにアクセスするには、次のようにします。
```kusto
database("<database name>").<table name>
```
リモートクラスター内のデータベース:
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

*データベース名*では大文字と小文字が区別されます

*クラスター名*は大文字と小文字が区別されず、次のいずれかの形式にすることができます。
* 整形式 URL: 例`http://contoso.kusto.windows.net:1234/`として、HTTP および HTTPS スキームのみがサポートされています。
* 完全修飾ドメイン名 (FQDN): たとえば`contoso.kusto.windows.net` 、に相当する`https://`**`contoso.kusto.windows.net`**`:443/`
* 短い名前 (ドメイン部分を含まないホスト名 [およびリージョン]): インスタンス`contoso` `https://` **`contoso`** `.kusto.windows.net:443/`の場合、または`contoso.westus`と解釈されます。`https://`**`contoso.westus`**`.kusto.windows.net:443/`

> [!NOTE]
> 複数データベースにまたがるアクセスは、通常のアクセス許可のチェックに従います。
> クエリを excute するには、既定のデータベースに対する読み取り権限と、クエリで参照されている他のすべてのデータベース (現在のクラスターとリモートクラスター内) に対する読み取り権限を持っている必要があります。

*修飾名*は、テーブル名を使用できる任意のコンテキストで使用できます。
次のすべてが有効です。

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

*修飾名*が[union 演算子](./unionoperator.md)のオペランドとして表示されている場合、ワイルドカードを使用して複数のテーブルと複数のデータベースを指定できます。 クラスター名にワイルドカードを使用することはできません。

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
>* 既定のデータベースの名前も一致する可能性があるため、データベース ("&#42;"). * 既定値を含むすべてのデータベースのすべてのテーブルを指定します。
>* スキーマの変更がクラスター間クエリに与える影響、「[クラスター間クエリとスキーマの変更](../concepts/crossclusterandschemachanges.md)」を参照してください。

## <a name="access-restriction"></a>アクセス制限 
[制限アクセス](./restrictstatement.md)ステートメントに修飾名またはパターンを含めることもできます (クラスター名のワイルドカードは使用できません)
```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

上の例では、クエリアクセスを次のエンティティに制限しています。

* 既定のデータベースの " *my.* .." で始まる任意のエンティティ名。 
* 現在のクラスターの*Myother...* という名前のすべてのデータベース内の任意のテーブル。
* クラスター *OtherCluster.kusto.windows.net*内の*my2*という名前のすべてのデータベース内の任意のテーブル。

## <a name="functions-and-views"></a>関数とビュー

関数およびビュー (永続化および作成されたインライン) は、データベースとクラスターの境界を越えてテーブルをリファレンスことができます。 有効なのは次のとおりです。

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

永続的な関数とビューには、同じクラスター内の別のデータベースからアクセスできます。

の`OtherDb`表形式関数 (ビュー):

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

次の`OtherDb`スカラー関数:
```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

既定のデータベース:

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>クラスター間関数呼び出しの制限事項

表形式の関数またはビューは、クラスター間で参照できます。 次の制限が適用されます。

1. リモート関数は表形式スキーマを返す必要があります。 スカラー関数は、同じクラスター内でのみアクセスできます。
2. リモート関数は、スカラーパラメーターのみを受け入れることができます。 1つ以上のテーブル引数を取得する関数は、同じクラスター内でのみアクセスできます。
3. リモート関数のスキーマは、そのパラメーターが既知で不変である必要があります (「[クラスター間クエリとスキーマ変更](../concepts/crossclusterandschemachanges.md)」も参照してください)。

次のクラスター間呼び出しが**有効**です:

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

次のクエリは、リモートスカラー `MyCalc`関数を呼び出します。
これは規則 #1 に違反しているため、有効では**ありません**。

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

次のクエリは、リモート`MyCalc`関数を呼び出し、表形式パラメーターを提供します。
これは規則 #2 に違反しているため、有効では**ありません**。

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] ) 
```

次のクエリは、パラメーター `SomeTable` `tablename`に基づいて変数スキーマの出力を持つリモート関数を呼び出します。
これは規則 #3 に違反しているため、有効では**ありません**。

次の`OtherDb`表形式関数:
```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

既定のデータベース:
```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

次のクエリは、データ`GetDataPivot`に基づく変数スキーマの出力を持つリモート関数を呼び出します ([pivot () プラグイン](pivotplugin.md)には動的出力があります)。
これは規則 #3 に違反しているため、有効では**ありません**。

次の`OtherDb`表形式関数:
```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

既定のデータベースの表形式関数:
```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>データの表示

クライアントにデータを返すステートメントは、 `take`演算子が特に使用されていない場合でも、返されるレコードの数によって暗黙的に制限されます。 この制限を引き上げるには、 `notruncation` クライアント要求オプションを使います。

グラフィカルな形式でデータを表示するには、 [render 演算子](renderoperator.md)を使用します。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
