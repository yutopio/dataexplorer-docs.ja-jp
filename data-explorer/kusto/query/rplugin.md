---
title: R プラグイン (プレビュー) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの R プラグイン (プレビュー) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d815a75b241f7779a5f4ee9cae626c38ed54f9f4
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765998"
---
# <a name="r-plugin-preview"></a>R プラグイン (プレビュー)

::: zone pivot="azuredataexplorer"

R プラグインは、R スクリプトを使用してユーザー定義関数 (UDF) を実行します。 R スクリプトは、入力として表形式のデータを取得し、表形式の出力を生成することが期待されます。
プラグインのランタイムは、クラスターのノードで実行される隔離された安全な環境である[サンドボックス](../concepts/sandboxes.md)でホストされます。

### <a name="syntax"></a>構文

*T* `|` `=` `single` | `per_node` `r(` *output_schema* `,` *script* `,` [ ] ( ) output_schema スクリプト [ script_parameters ] *script_parameters* `evaluate` `hint.distribution``)`


### <a name="arguments"></a>引数

* *output_schema*: `type` R コードによって返される表形式データの出力スキーマを定義するリテラル。
    * 形式`typeof(`*は次のとおりです*`:`*ColumnType*。`)`、たとえば: `typeof(col1:string, col2:long)`。
    * 入力スキーマを拡張するには、次の構文を使用`typeof(*, col1:string, col2:long)`します。
* *script*:`string`実行される有効な R スクリプトであるリテラル。
* *script_parameters*:`dynamic`予約された`kargs`ディクショナリとして R スクリプトに渡される名前/値ペアのプロパティ バッグであるオプションのリテラルです ([予約済み R 変数](#reserved-r-variables)を参照)。
* *hint.distribution*: プラグインの実行を複数のクラスタノードに分散するためのオプションのヒント。
   既定値: `single`。
    * `single`: スクリプトの単一インスタンスがクエリ データ全体にわたって実行されます。
    * `per_node`: R ブロックの前のクエリが分散されている場合、スクリプトのインスタンスは、スクリプトに含まれるデータ上の各ノードで実行されます。


### <a name="reserved-r-variables"></a>予約済み R 変数

次の変数は、Kusto クエリ言語と R コードの間の相互作用のために予約されています。

* `df`: R データ フレームとしての入力`T`表形式データ (上記の値)。
* `kargs`: r ディクショナリとして*script_parameters*引数の値。
* `result`: R スクリプトによって作成された R データフレームで、その値は、プラグインに続く Kusto クエリ演算子に送信される表形式のデータになります。

### <a name="onboarding"></a>オンボード


* プラグインはデフォルトで無効になっています。
    * *クラスターでプラグインを有効にすることに興味がありますか?*
        
        * Azure ポータルの Azure データ エクスプローラー クラスターで、左側のメニューで **[新しいサポート要求**] を選択します。
        * プラグインを無効にする場合も、サポートチケットを開く必要があります。

### <a name="notes-and-limitations"></a>注意事項と制限事項

* R サンドボックス イメージは*R 3.4.4 for Windows*に基づいており、[アナコンダの R Essentials バンドル](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/)のパッケージが含まれています。
* R サンドボックスはネットワークへのアクセスを制限するため、R コードはイメージに含まれていない追加のパッケージを動的にインストールできません。特定のパッケージが必要な場合は、Azure ポータルで**新しいサポート 要求**を開きます。


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

:::image type="content" source="images/samples/sine-demo.png" alt-text="新しいデモ":::

### <a name="performance-tips"></a>パフォーマンスに関するヒント

* プラグインの入力データ・セットを、必要な最小量 (列/行) まで減らします。
    * ソース データセットに対して、可能な場合は Kusto クエリ言語を使用してフィルターを使用します。
    * ソース列のサブセットに対して計算を実行するには、プラグインを呼び出す前に、それらの列だけを射出します。
* スクリプト`hint.distribution = per_node`内のロジックが配布可能な場合は必ず使用します。
    * また、[パーティション演算子](partitionoperator.md)を使用して、入力データ・セットをパーティション化することもできます。
* 可能な場合は、Kusto クエリ言語を使用して、R スクリプトのロジックを実装します。

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

* Kusto 文字列の区切り文字と R の区切り文字の間の競合を避`'`けるため、Kusto クエリでは Kusto 文字列リテラルには一重引用符`"`( ) 、R スクリプトでは R 文字列リテラルには二重引用符 ( ) を使用することをお勧めします。
* [外部データ オペレーター](externaldata-operator.md)を使用して、Azure BLOB ストレージ、パブリック GitHub リポジトリなど、外部の場所に保存したスクリプトのコンテンツを取得します。
  
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

これは Azure モニターではサポートされていません。

::: zone-end

