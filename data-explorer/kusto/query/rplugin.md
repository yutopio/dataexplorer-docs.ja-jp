---
title: R プラグイン (プレビュー)-Azure データエクスプローラー |Microsoft Docs
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
ms.openlocfilehash: 514c67133980c9ab1c38b65cc51e4592dcb15eda
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618966"
---
# <a name="r-plugin-preview"></a>R プラグイン (プレビュー)

::: zone pivot="azuredataexplorer"

R プラグインは、R スクリプトを使用してユーザー定義関数 (UDF) を実行します。 R スクリプトは、表形式のデータを入力として取得し、表形式の出力を生成することが想定されています。
プラグインのランタイムは、クラスターのノード上で実行される分離された安全な環境である[サンドボックス](../concepts/sandboxes.md)でホストされます。

### <a name="syntax"></a>構文

*T* `|` `per_node``,` *script_parameters* *output_schema* *script* [`hint.distribution` (`single`)] `r(`output_schema`,`スクリプト [script_parameters] `evaluate` `=`  | `)`


### <a name="arguments"></a>引数

* *output_schema*: R `type`コードによって返される表形式データの出力スキーマを定義するリテラル。
    * 形式は次の`typeof(`とおりです: *ColumnName* `:` *ColumnType* [,...]`)`(例: `typeof(col1:string, col2:long)`)。
    * 入力スキーマを拡張するには、という`typeof(*, col1:string, col2:long)`構文を使用します。
* *script*: 実行`string`する有効な R スクリプトであるリテラル。
* *script_parameters*: 予約`kargs`さ`dynamic`れたディクショナリとして r スクリプトに渡される名前と値のペアのプロパティバッグである省略可能なリテラル (「[予約済み R 変数](#reserved-r-variables)」を参照)。
* *ヒント: distribution*: プラグインの実行を複数のクラスターノードに分散するための省略可能なヒントです。
   既定値:`single`。
    * `single`: スクリプトの1つのインスタンスがクエリデータ全体で実行されます。
    * `per_node`: R ブロックの前にクエリが分散されている場合、スクリプトのインスタンスは、含まれているデータを介して各ノードで実行されます。


### <a name="reserved-r-variables"></a>予約済み R 変数

次の変数は、Kusto クエリ言語と R コード間のやり取りのために予約されています。

* `df`: 入力表形式のデータ (上記の`T`値) は、R データフレームとして指定します。
* `kargs`: R ディクショナリとしての*script_parameters*引数の値。
* `result`: R スクリプトによって作成された R データフレーム。その値は、プラグインに従う任意の Kusto クエリ演算子に送信される表形式のデータになります。

### <a name="onboarding"></a>オンボード


* プラグインは既定で無効になっています。
    * *クラスターでプラグインを有効にすることに関心がありますか?*
        
        * Azure portal の Azure データエクスプローラークラスター内で、左側のメニューの [**新しいサポート要求**] を選択します。
        * プラグインを無効にするには、サポートチケットも開く必要があります。

### <a name="notes-and-limitations"></a>メモと制限事項

* R sandbox イメージは*r 3.4.4 For Windows*に基づいており、 [Anaconda の r Essentials バンドル](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/)のパッケージが含まれています。
* R サンドボックスはネットワークへのアクセスを制限するため、R コードでは、イメージに含まれていない追加のパッケージを動的にインストールすることはできません。特定のパッケージが必要な場合は、Azure portal で**新しいサポート要求**を開きます。


### <a name="examples"></a>例

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

### <a name="performance-tips"></a>パフォーマンスに関するヒント

* プラグインの入力データセットを必要な最小量 (列/行) に減らします。
    * 可能であれば、Kusto クエリ言語を使用して、ソースデータセットのフィルターを使用します。
    * ソース列のサブセットに対して計算を実行するには、プラグインを呼び出す前に、その列だけをプロジェクトに含めます。
* スクリプト`hint.distribution = per_node`内のロジックが再頒布可能な場合は常にを使用します。
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

### <a name="usage-tips"></a>使用上のヒント

* Kusto 文字列の区切り記号と R の文字の間の競合を避けるために、kusto クエリの Kusto 文字列リテラルには単一引用符 (`'`) を使用し`"`、R スクリプトでは r 文字列リテラルに二重引用符文字 () を使用することをお勧めします。
* [Externaldata オペレーター](externaldata-operator.md)を使用して、Azure blob storage、パブリック GitHub リポジトリなどの外部の場所に格納したスクリプトの内容を取得します。
  
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

これは、ではサポートされていません Azure Monitor

::: zone-end

