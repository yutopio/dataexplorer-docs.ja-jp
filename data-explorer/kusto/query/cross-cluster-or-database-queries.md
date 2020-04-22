---
title: クロスデータベース クエリとクロスクラスター クエリ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクロスデータベース クエリとクロスクラスター クエリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8bb0cf2674bae801fb98d14d93518f5617af6807
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766066"
---
# <a name="cross-database-and-cross-cluster-queries"></a>複数のデータベースに対するクエリと複数のクラスターに対するクエリ

::: zone pivot="azuredataexplorer"

すべての Kusto クエリは、現在のクラスターと既定のデータベースのコンテキストで動作します。
* [Kusto Explorer](../tools/kusto-explorer.md)では、デフォルトのデータベースは[[接続] パネル](../tools/kusto-explorer.md#connections-panel)で選択されたデータベースで、現在のクラスタは、そのデータベースを含む接続です。
* [Kusto クライアント ライブラリ](../api/netfx/about-kusto-data.md)を使用する場合、現在のクラスタと既定の`Data Source`データベース`Initial Catalog`は、それぞれ[Kusto 接続文字列](../api/connection-strings/kusto.md)の プロパティと プロパティで指定されます。

## <a name="queries"></a>クエリ
デフォルト以外のデータベースから表にアクセスするには、*修飾名*構文を使用する必要があります。
```kusto
database("<database name>").<table name>
```
リモート クラスタのデータベース:
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

*データベース名*は大文字と小文字が区別されます

*クラスタ名*は大文字と小文字を区別せず、次のいずれかの形式にすることができます。
* 整形式の URL:`http://contoso.kusto.windows.net:1234/`例として、HTTP および HTTPS スキームのみがサポートされています。
* 完全修飾ドメイン名 (FQDN): たとえば`contoso.kusto.windows.net`、 - と同等になります。`https://`**`contoso.kusto.windows.net`**`:443/`
* ドメイン部分のない短い名前(ホスト名 [およびリージョン]):`contoso`たとえば、`https://`**`contoso`**`.kusto.windows.net:443/`または`contoso.westus`- として解釈される`https://`**`contoso.westus`**`.kusto.windows.net:443/`

> [!NOTE]
> データベース間アクセスは、通常の権限チェックの対象となります。
> クエリを削除するには、既定のデータベースと、クエリで参照される他のすべてのデータベース (現在のクラスターおよびリモート クラスター) に対する読み取り権限が必要です。

*修飾名*は、表名を使用できる任意のコンテキストで使用できます。
以下のすべてが有効です。

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

*修飾名*が[共用体演算子](./unionoperator.md)のオペランドとして指定されている場合、ワイルドカードを使用して複数の表および複数のデータベースを指定できます。 ワイルドカードはクラスタ名では使用できません。

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
>* デフォルトデータベースの名前も一致する可能性があるため、database("&#42;")*はデフォルトを含むすべてのデータベースのすべてのテーブルを指定します。
>* スキーマの変更がクラスター間クエリに与える影響については、「[クラスター間クエリとスキーマの変更」を](../concepts/crossclusterandschemachanges.md)参照してください。

## <a name="access-restriction"></a>アクセス制限 
限定名またはパターンは[、制限アクセス](./restrictstatement.md)ステートメントに含めることもできます (クラスター名のワイルドカードは使用できません)。
```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

上記の場合、クエリへのアクセスは次の entit に制限されます。

* 既定のデータベース内で*my...* で始まるエンティティ名。 
* 現在のクラスターのすべての*MyOther.* という名前のデータベース内の任意のテーブル。
* *クラスタ内*のすべてのデータベース内の*my2. OtherCluster.kusto.windows.net*テーブル

## <a name="functions-and-views"></a>関数とビュー

関数とビュー (永続的なインラインで作成された) は、データベース境界とクラスター境界を越えてテーブルを参照できます。 以下は有効です。

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

永続関数およびビューには、同じクラスター内の別のデータベースからアクセスできます。

表形式関数 (ビュー `OtherDb`) の :

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

スカラー関数`OtherDb`:
```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

既定のデータベース:

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>クラスタ間関数呼び出しの制限

表形式の関数またはビューは、クラスター間で参照できます。 以下の制限が適用されます。

1. リモート関数は、表形式のスキーマを返す必要があります。 スカラー関数は、同じクラスター内でのみアクセスできます。
2. リモート関数はスカラーパラメータのみを受け入れることができます。 1 つ以上のテーブル引数を取得する関数は、同じクラスター内でのみアクセスできます。
3. リモート関数のスキーマは、そのパラメータの既知の不変である必要があります ([クラスタ間クエリおよびスキーマ変更](../concepts/crossclusterandschemachanges.md)も参照してください)。

次のクラスタ間呼び出しは**有効です**。

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

次のクエリは、リモート ス`MyCalc`カラー関数 を呼び出します。
これはルール#1に違反しているため、**無効です**。

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

次のクエリは、リモート`MyCalc`関数を呼び出し、表形式のパラメーターを提供します。
これはルール#2に違反しているため、**無効です**。

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] ) 
```

次のクエリは、パラメータ`SomeTable``tablename`に基づいてスキーマ出力が変数を持つリモート関数を呼び出します。
これは規則違反#3であるため、**無効です**。

の表形式`OtherDb`関数:
```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

既定のデータベース:
```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

次のクエリは、データ`GetDataPivot`に基づく変数スキーマ出力を持つリモート関数を呼び出します[(pivot() プラグイン](pivotplugin.md)には動的出力があります)。
これは規則違反#3であるため、**無効です**。

の表形式`OtherDb`関数:
```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

既定のデータベースの表形式関数:
```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>データの表示

クライアントにデータを返すステートメントは、演算子を使用しない場合でも、返されるレコードの数によって暗黙的に制限されます`take`。 この制限を引き上げるには、 `notruncation` クライアント要求オプションを使います。

データをグラフィカルな形式で表示するには[、render オペレータ](renderoperator.md)を使用します。

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
