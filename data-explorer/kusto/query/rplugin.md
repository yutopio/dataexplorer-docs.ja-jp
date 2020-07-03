---
title: R プラグイン (プレビュー)-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの R プラグイン (プレビュー) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 429140a1ae74dc0d7189e4b8dec1a32428fe3bb3
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85902154"
---
# <a name="r-plugin-preview"></a>R プラグイン (プレビュー)

::: zone pivot="azuredataexplorer"

R プラグインは、R スクリプトを使用してユーザー定義関数 (UDF) を実行します。 このスクリプトは、表形式のデータを入力として取得し、表形式の出力を生成します。
プラグインのランタイムは、クラスターのノード上の[サンドボックス](../concepts/sandboxes.md)でホストされます。 サンドボックスは、分離された安全な環境を提供します。

## <a name="syntax"></a>構文

*T* `|` `evaluate` [ `hint.distribution` `=` ( `single`  |  `per_node` )] `r(` *output_schema* `,` *スクリプト*[ `,` *script_parameters*]`)`

## <a name="arguments"></a>引数

* *output_schema*: `type` R コードによって返される表形式データの出力スキーマを定義するリテラル。
    * 形式は、 `typeof(` *ColumnName* `:` *ColumnType*[,...] です。 `)` たとえば、のように `typeof(col1:string, col2:long)` なります。
    * 入力スキーマを拡張するには、という構文を使用し `typeof(*, col1:string, col2:long)` ます。
* *script*: `string` 実行する有効な R スクリプトであるリテラル。
* *script_parameters*: `dynamic` 予約されたディクショナリとして R スクリプトに渡される名前と値のペアのプロパティバッグである省略可能なリテラルです `kargs` 。 詳細については、「[予約済み R 変数](#reserved-r-variables)」を参照してください。
* *ヒント: distribution*: プラグインの実行を複数のクラスターノードに分散するための省略可能なヒントです。
   既定値:`single`。
    * `single`: スクリプトの1つのインスタンスがクエリデータ全体で実行されます。
    * `per_node`: R ブロックの前にクエリが分散されている場合、スクリプトのインスタンスは、含まれているデータを介して各ノードで実行されます。

## <a name="reserved-r-variables"></a>予約済み R 変数

次の変数は、Kusto クエリ言語と R コード間のやり取りのために予約されています。

* `df`: 入力表形式のデータ (上記の値) は、 `T` R データフレームとして指定します。
* `kargs`: R ディクショナリとしての*script_parameters*引数の値。
* `result`: R スクリプトによって作成された R データフレーム。 値は、プラグインに従う任意の Kusto クエリ演算子に送信される表形式のデータになります。

## <a name="enable-the-plugin"></a>プラグインの有効化

* プラグインは既定で無効になっています。
* クラスターの [**構成**] タブで、Azure portal のプラグインを有効または無効にします。 詳細については[、「Azure データエクスプローラークラスターでの言語拡張機能の管理 (プレビュー)](../../language-extensions.md) 」を参照してください。

## <a name="notes-and-limitations"></a>注意事項と制限事項

* R sandbox イメージは*r 3.4.4 For Windows*に基づいており、 [Anaconda の r Essentials バンドル](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/)のパッケージが含まれています。
* R サンドボックスは、ネットワークへのアクセスを制限します。 R コードでは、イメージに含まれていない追加のパッケージを動的にインストールすることはできません。 特定のパッケージが必要な場合は、Azure portal で**新しいサポート要求**を開きます。

## <a name="examples"></a>使用例

```kusto
range x from 1 to 360 step 1
| evaluate r(
//
typeof(*, fx:double),               //  Output schema: append a new fx column to original table 
//
'result <- df\n'                    //  The R decorated script
'n <- nrow(df)\n'
'g <- kargs$gain\n'
'f <- kargs$cycles\n'
'result$fx <- g * sin(df$x / n * 2 * pi * f)'
//
, pack('gain', 100, 'cycles', 4)    //  dictionary of parameters
)
| render linechart 
```

:::image type="content" source="images/plugin/sine-demo.png" alt-text="サインのデモ" border="false":::

## <a name="performance-tips"></a>パフォーマンスに関するヒント

* プラグインの入力データセットを必要な最小量 (列/行) に減らします。
    * 可能な場合は、Kusto クエリ言語を使用してソースデータセットのフィルターを使用します。
    * ソース列のサブセットに対して計算を行うには、プラグインを呼び出す前に、その列だけをプロジェクトに含めます。
* `hint.distribution = per_node`スクリプト内のロジックが再頒布可能な場合は常にを使用します。
    * 入力データセットをパーティション分割するために、 [partition 演算子](partitionoperator.md)を使用することもできます。
* 可能な場合は常に Kusto クエリ言語を使用して、R スクリプトのロジックを実装します。

    次に例を示します。

    ```kusto    
    .show operations
    | where StartedOn > ago(1d) // Filtering out irrelevant records before invoking the plugin
    | project d_seconds = Duration / 1s // Projecting only a subset of the necessary columns
    | evaluate hint.distribution = per_node r( // Using per_node distribution, as the script's logic allows it
        typeof(*, d2:double),
        'result <- df\n'
        'result$d2 <- df$d_seconds\n' // Negative example: this logic should have been written using Kusto's query language
      )
    | summarize avg = avg(d2)
    ```

## <a name="usage-tips"></a>使用上のヒント

* Kusto 文字列の区切り記号と R 文字列の区切り記号が競合しないようにするには、次のようにします。  
    * Kusto クエリの `'` kusto 文字列リテラルには単一引用符 () を使用します。
    * R `"` スクリプトで r 文字列リテラルに二重引用符文字 () を使用します。
* 外部[データ演算子](externaldata-operator.md)を使用して、Azure blob storage やパブリック GitHub リポジトリなどの外部の場所に格納したスクリプトの内容を取得します。
  
  次に例を示します。

    ```kusto
    let script = 
        externaldata(script:string)
        [h'https://kustoscriptsamples.blob.core.windows.net/samples/R/sample_script.r']
        with(format = raw);
    range x from 1 to 360 step 1
    | evaluate r(
        typeof(*, fx:double),
        toscalar(script), 
        pack('gain', 100, 'cycles', 4))
    | render linechart 
    ```

---

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end

