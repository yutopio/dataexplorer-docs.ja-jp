---
title: 複数のデータベースにまたがるクロス & クエリ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのデータベース間およびクロスクラスタークエリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: dbab2bda9ee24c79e4b62427a7da5bd7db3f0077
ms.sourcegitcommit: b6f0f112b6ddf402e97c011a902bd70ba408e897
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94497447"
---
# <a name="cross-database-and-cross-cluster-queries"></a>複数のデータベースに対するクエリと複数のクラスターに対するクエリ

::: zone pivot="azuredataexplorer"

すべての Kusto クエリは、現在のクラスターおよび既定のデータベースのコンテキストで動作します。
* [Kusto Explorer](../tools/kusto-explorer.md)では、[[接続] パネル](../tools/kusto-explorer.md#connections-panel)で既定のデータベースが選択されています。現在のクラスターは、そのデータベースを含む接続です。
* [クライアントライブラリ](../api/netfx/about-kusto-data.md)を使用する場合、現在のクラスターと既定のデータベースは `Data Source` それぞれ、 `Initial Catalog` [接続文字列](../api/connection-strings/kusto.md)のプロパティとプロパティによって指定されます。

## <a name="queries"></a>クエリ
既定以外のデータベースからテーブルにアクセスするには、 *修飾名* の構文を使用する必要があります。

現在のクラスター内のデータベースにアクセスします。

```kusto
database("<database name>").<table name>
```

リモートクラスター内のデータベース。
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

*データベース名* では大文字と小文字が区別されます

*クラスター名* は大文字と小文字が区別されず、次のいずれかの形式にすることができます。
   * などの整形式の URL `http://contoso.kusto.windows.net:1234/` 。 HTTP および HTTPS スキームのみがサポートされています。
   * のような完全修飾ドメイン名 (FQDN) `contoso.kusto.windows.net` 。 この文字列はと同じです `https://` **`contoso.kusto.windows.net`** `:443/` 。
   * またはなど、短い名前 (ドメイン部分のないホスト名 [および地域] `contoso` ) `contoso.westus` 。 これらの文字列は、およびとして解釈され `https://` **`contoso`** `.kusto.windows.net:443/` `https://` **`contoso.westus`** `.kusto.windows.net:443/` ます。

> [!NOTE]
> 複数データベースにまたがるアクセスは、通常のアクセス許可のチェックに従います。
> クエリを実行するには、既定のデータベースに対する読み取り権限と、クエリで参照されている他のすべてのデータベース (現在のクラスターとリモートクラスター内) に対する読み取り権限を持っている必要があります。

*修飾名* は、テーブル名を使用できる任意のコンテキストで使用できます。

次のすべてが有効です。

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

*修飾名* が [union 演算子](./unionoperator.md)のオペランドとして表示されている場合、ワイルドカードを使用して複数のテーブルと複数のデータベースを指定できます。 クラスター名にワイルドカードは使用できません。

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
> * 既定のデータベースの名前も一致する可能性があるため、データベース ("&#42;") では、既定値を含むすべてのデータベースのすべてのテーブルが指定されます。
> * スキーマの変更がクラスター間クエリに与える影響の詳細については、「[クロスクラスタークエリとスキーマ変更](../concepts/crossclusterandschemachanges.md)」を参照してください。

## <a name="access-restriction"></a>アクセス制限

限定 [アクセス](./restrictstatement.md) ステートメントに修飾名またはパターンを含めることもできます。クラスター名のワイルドカードは許可されません。

```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

上の例では、次のエンティティへのクエリアクセスが制限されています。

* 既定のデータベースの " *my.* .." で始まる任意のエンティティ名。 
* 現在のクラスターの *Myother...* という名前のすべてのデータベース内の任意のテーブル。
* クラスター *OtherCluster.kusto.windows.net* 内の *my2* という名前のすべてのデータベース内の任意のテーブル。

## <a name="functions-and-views"></a>関数とビュー

関数とビュー (永続化および作成されたインライン) は、データベースとクラスターの境界を越えてテーブルを参照できます。 次のコードは有効です。

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

永続的な関数とビューには、同じクラスター内の別のデータベースからアクセスできます。

の表形式関数 (ビュー) `OtherDb` 。

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

のスカラー関数 `OtherDb` 。

```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

既定のデータベース。

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>クラスター間関数呼び出しの制限事項

表形式の関数またはビューは、クラスター間で参照できます。 次の制限事項が適用されます。

* リモート関数は表形式スキーマを返す必要があります。 スカラー関数は、同じクラスター内でのみアクセスできます。
* リモート関数は、スカラーパラメーターのみを受け入れることができます。 1つ以上のテーブル引数を取得する関数は、同じクラスター内でのみアクセスできます。
* パフォーマンス上の理由から、最初の呼び出しの後に、呼び出し元のクラスターによってリモートエンティティのスキーマがキャッシュされます。 そのため、リモートエンティティに加えられた変更によって、キャッシュされたスキーマ情報が一致しなくなる可能性があります。これは、クエリの失敗につながる可能性があります。 詳細については、「 [クロスクラスタークエリとスキーマ変更](../concepts/crossclusterandschemachanges.md)」を参照してください。

次のクラスター間呼び出しが有効です。

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

次のクエリは、リモートスカラー関数を呼び出し `MyCalc` ます。
この呼び出しは、規則 #1 に違反するため、有効ではありません。

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

次のクエリは、リモート関数を呼び出し、 `MyCalc` 表形式パラメーターを提供します。
この呼び出しは、規則 #2 に違反するため、有効ではありません。

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] )
```

次のクエリは、 `SomeTable` パラメーターに基づいて変数スキーマの出力を持つリモート関数を呼び出し `tablename` ます。
この呼び出しは、規則 #3 に違反するため、有効ではありません。

の表形式関数 `OtherDb` 。

```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

既定のデータベース。

```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

次のクエリは、 `GetDataPivot` データに基づく変数スキーマの出力を持つリモート関数を呼び出します ([pivot () プラグイン](pivotplugin.md) には動的出力があります)。
この呼び出しは、規則 #3 に違反するため、有効ではありません。

の表形式関数 `OtherDb` 。

```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

既定のデータベースの表形式関数。

```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>データの表示

クライアントにデータを返すステートメントは、演算子が特に使用されていない場合でも、返されるレコードの数によって暗黙的に制限され `take` ます。 この制限を引き上げるには、 `notruncation` クライアント要求オプションを使います。

グラフィカルな形式でデータを表示するには、 [render 演算子](renderoperator.md)を使用します。

::: zone-end

::: zone pivot="azuremonitor"

複数のデータベースにまたがるクエリとクロスクラスタークエリは、Azure Monitor ではサポートされていません。 複数のワークスペースとアプリにまたがるクエリについては、「 [Azure Monitor でのクロスワークスペースクエリ](/azure/azure-monitor/log-query/cross-workspace-query) 」を参照してください。

::: zone-end
