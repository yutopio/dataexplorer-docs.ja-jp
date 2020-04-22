---
title: Python プラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで Python プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5192474779fb712595ff1c25785892bb543fda84
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765720"
---
# <a name="python-plugin"></a>Python プラグイン

::: zone pivot="azuredataexplorer"

Python プラグインは、Python スクリプトを使用してユーザー定義関数 (UDF) を実行します。 Python スクリプトは、入力として表形式のデータを取得し、表形式の出力を生成することが期待されます。
プラグインのランタイムは、クラスターのノード上で実行される[サンドボックス](../concepts/sandboxes.md)でホストされます。

## <a name="syntax"></a>構文

*T* `|` `=` `single` | `per_node` `python(` *output_schema* `,` `,` *script_parameters*`,` *external_artifacts* *script* [`hint.distribution` ] [ ] output_schemaスクリプト [ script_parameters ] [ external_artifacts ] `evaluate``)`

## <a name="arguments"></a>引数

* *output_schema*: `type` Python コードによって返される表形式データの出力スキーマを定義するリテラル。
    * 形式`typeof(`*は次のとおりです*`:`*ColumnType*。`)`、たとえば: `typeof(col1:string, col2:long)`。
    * 入力スキーマを拡張するには、次の構文を使用します。`typeof(*, col1:string, col2:long)`
* *script*:`string`実行する有効な Python スクリプトであるリテラル。
* *script_parameters*: `dynamic` Python スクリプトに予約辞書`kargs`として渡される名前/値ペアのプロパティ バッグであるオプションのリテラルです ([予約済み Python 変数](#reserved-python-variables)を参照)。
* *hint.distribution*: プラグインの実行を複数のクラスタノードに分散するためのオプションのヒント。
  * 既定値は `single` です。
  * `single`: スクリプトの単一インスタンスがクエリ データ全体にわたって実行されます。
  * `per_node`: Python ブロックの前のクエリが配布される場合、スクリプトのインスタンスは、そのブロックに含まれるデータ上の各ノードで実行されます。
* *external_artifacts*:`dynamic`クラウド ストレージからアクセス可能で、実行時にスクリプトで使用できるアーティファクトの名前& URL ペアのプロパティ バッグであるオプションのリテラル。
  * このプロパティ バッグで参照される URL は、次の目的で必要です。
  * クラスタの[コールアウト ポリシー](../management/calloutpolicy.md)に含まれる。
    2. 「ストレージ接続文字列」で説明されているように、公開されている場所にいるか、必要な資格情報[を提供します](../api/connection-strings/storage.md)。
  * アーティファクトは、ローカルの一時ディレクトリからスクリプトが使用できるようになっており、`.\Temp`プロパティ バッグに指定された名前がローカル ファイル名として使用されます (以下の[例](#examples)を参照)。
  * 詳細については、後述[の付録を](#appendix-installing-packages-for-the-python-plugin)参照してください。

## <a name="reserved-python-variables"></a>予約済み Python 変数

次の変数は、Kusto クエリ言語と Python コードの間の相互作用のために予約されています。

* `df`: 入力表形式データ (上記`T`の値)`pandas`をデータ フレームとして返します。
* `kargs`: python ディクショナリとして*script_parameters*引数の値。
* `result`: `pandas` Python スクリプトによって作成された DataFrame で、その値が、プラグインに続く Kusto クエリ演算子に送信される表形式のデータになります。

## <a name="onboarding"></a>オンボード

* プラグインはデフォルトで無効になっています。
* プラグインを有効にするための前提条件を[ここに](../concepts/sandboxes.md#prerequisites)示します。
* クラスターの[**[構成**] タブ](https://docs.microsoft.com/azure/data-explorer/language-extensions)の Azure Portal でプラグインを有効または無効にします。

## <a name="notes-and-limitations"></a>注意事項と制限事項

* Python サンドボックス イメージは *、Python 3.6*エンジンを使用した*Anaconda 5.2.0*ディストリビューションに基づいています。
  パッケージの一覧[はこちら](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/)から見つけることができます (ごく一部のパッケージは、プラグインが実行されるサンドボックスによって適用される制限と互換性がない可能性があります)。
* Python イメージには、一般的な`tensorflow`ML `keras``torch`パッケージ`hdbscan` `xgboost` 、 、 、、その他の便利なパッケージも含まれています。
* このプラグインは、デフォルトでは *、パンダ*&(のように`pd``np`*)numpy*をインポートします。  必要に応じて、他のモジュールをインポートできます。
* **クエリおよび[更新ポリシー](../management/updatepolicy.md)[からの取り込み](../management/data-ingestion/ingest-from-query.md)**
  * 次のようなクエリでプラグインを使用することが可能です。
      1. 更新ポリシーの一部として定義され、ソース表が*非ストリーミング*インジェストを使用して取り込まれます。
      2. クエリから取得するコマンドの一部として実行します (たとえば)。 `.set-or-append`
  * 上記のどちらの場合も、インジェストのボリュームと頻度、および Python ロジックの複雑さとリソース使用率が[サンドボックスの制限](../concepts/sandboxes.md#limitations)と、クラスターの使用可能なリソースに合致していることを確認することをお勧めします。
    この操作を行わないと、[調整エラーが発生する](../concepts/sandboxes.md#errors)可能性があります。
  * 更新ポリシーの一部として定義されたクエリでは、ソーステーブルが[ストリーミングインジェスト](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming)を使用して取り込まれるプラグインを使用*することはできません*。

## <a name="examples"></a>例

```kusto
range x from 1 to 360 step 1
| evaluate python(
//
typeof(*, fx:double),               //  Output schema: append a new fx column to original table 
//
'result = df\n'                     //  The Python decorated script
'n = df.shape[0]\n'
'g = kargs["gain"]\n'
'f = kargs["cycles"]\n'
'result["fx"] = g * np.sin(df["x"]/n*2*np.pi*f)\n'
//
, pack('gain', 100, 'cycles', 4)    //  dictionary of parameters
)
| render linechart 
```
:::image type="content" source="images/samples/sine-demo.png" alt-text="罪のデモ":::

```kusto
print "This is an example for using 'external_artifacts'"
| evaluate python(
    typeof(File:string, Size:string),
    "import os\n"
    "result = pd.DataFrame(columns=['File','Size'])\n"
    "sizes = []\n"
    "path = '.\\\\Temp'\n"
    "files = os.listdir(path)\n"
    "result['File']=files\n"
    "for file in files:\n"
    "    sizes.append(os.path.getsize(path + '\\\\' + file))\n"
    "result['Size'] = sizes\n"
    "\n",
    external_artifacts = 
        dynamic({"this_is_my_first_file":"https://kustoscriptsamples.blob.core.windows.net/samples/R/sample_script.r",
                 "this_is_a_script":"https://kustoscriptsamples.blob.core.windows.net/samples/python/sample_script.py"})
)
```

| ファイル                  | サイズ |
|-----------------------|------|
| this_is_a_script      | 120  |
| this_is_my_first_file | 105  |

## <a name="performance-tips"></a>パフォーマンスに関するヒント

* プラグインの入力データ・セットを、必要な最小量 (列/行) まで減らします。
    * 可能な場合は、Kusto のクエリ言語で、ソース データセットにフィルターを使用します。
    * ソース列のサブセットに対して計算を実行するには、プラグインを呼び出す前に、それらの列だけを射出します。
* スクリプト`hint.distribution = per_node`内のロジックが配布可能な場合は必ず使用します。
    * また、[パーティション演算子](partitionoperator.md)を使用して、入力データ・セットをパーティション化することもできます。
* 可能な限り Kusto のクエリ言語を使用して、Python スクリプトのロジックを実装します。

    例:

    ```kusto    
    .show operations
    | where StartedOn > ago(7d) // Filtering out irrelevant records before invoking the plugin
    | project d_seconds = Duration / 1s // Projecting only a subset of the necessary columns
    | evaluate hint.distribution = per_node python( // Using per_node distribution, as the script's logic allows it
        typeof(*, _2d:double),
        'result = df\n'
        'result["_2d"] = 2 * df["d_seconds"]\n' // Negative example: this logic should have been written using Kusto's query language
      )
    | summarize avg = avg(_2d)
    ```

## <a name="usage-tips"></a>使用上のヒント

* Python スクリプトを含む複数行の文字列を`Kusto.Explorer`生成するには、お気に入りの Python エディタ *(Jupyter* *、Visual Studio コード**、PyCharm*など) から Python スクリプトをコピーします。
    * *F2*キーを押して **[Python で編集]ウィンドウを**開きます。 スクリプトをこのウィンドウに貼り付けます。 **[OK]** を選択します。 スクリプトは引用符と新しい行で修飾され (Kusto で有効です)、自動的にクエリタブに貼り付けます。
    * クエリ タブに直接 Python コードを貼り付け、それらの行を選択し *、Ctrl + K*キーを押して Ctrl *+* K キーを押し、上のように修飾します *(Ctrl + K*キーを押して Ctrl *+* K キーを押します)。 [クエリ](../tools/kusto-explorer-shortcuts.md#query-editor)エディターのショートカットの完全な一覧を次に示します。
* Kusto 文字列の区切り文字と Python 文字列リテラルの間の競合を避けるため`'`、Kusto クエリの Kusto 文字列リテラルには一重引用符 (`"`) 、Python スクリプトの Python 文字列リテラルには二重引用符 ( ) を使用することをお勧めします。
* [外部データ演算子](externaldata-operator.md)を使用して、Azure BLOB ストレージなどの外部の場所に格納したスクリプトのコンテンツを取得します。
  
    **例**

    ```kusto
    let script = 
        externaldata(script:string)
        [h'https://kustoscriptsamples.blob.core.windows.net/samples/python/sample_script.py']
        with(format = raw);
    range x from 1 to 360 step 1
    | evaluate python(
        typeof(*, fx:double),
        toscalar(script), 
        pack('gain', 100, 'cycles', 4))
    | render linechart 
    ```

## <a name="appendix-installing-packages-for-the-python-plugin"></a>付録: Python プラグイン用のパッケージのインストール

次のいずれかの理由により、パッケージの自己インストールが必要になる場合があります。

* パッケージはプライベートで、あなた自身のものです。
* パッケージは公開されますが、プラグインの基本イメージには含まれていません。

パッケージは、次の手順でインストールできます。

1. 1 回限りの前提条件:
  
  a. パッケージをホストする BLOB コンテナーを作成します( できればクラスターと同じリージョンで)。
    * たとえば、(https://artifcatswestus.blob.core.windows.net/pythonクラスターが米国西部にある場合)
  
  b. クラスターの[コールアウト ポリシー](../management/calloutpolicy.md)を変更して、その場所へのアクセスを許可します。
    * これには[、すべてのデータベース管理者の](../management/access-control/role-based-authorization.md)アクセス許可が必要です。
    * たとえば、 にある BLOBhttps://artifcatswestus.blob.core.windows.net/pythonへのアクセスを有効にするには、次のコマンドを実行します。

      ```kusto
      .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
      ```

2. パブリックパッケージ[(PyPi](https://pypi.org/)またはその他のチャンネル)の場合は、 パッケージとその依存関係をダウンロードします。
  b. 必要に応じて、ホイール()`*.whl`ファイルにコンパイルします。
    * (ローカルの Python 環境で) cmd ウィンドウから実行します。
      ```python
      pip wheel [-w download-dir] package-name.
      ```

3. 必要なパッケージとその依存関係を含む zip ファイルを作成します。

    * パブリックパッケージの場合: 前の手順でダウンロードしたファイルを zip で圧縮します。
    * メモ:
        * ファイル自体を`.whl`圧縮し、親フォルダ*を圧縮しないように*してください。
        * ベースサンドボックスイメージ`.whl`に同じバージョンのパッケージが既に存在する場合は、ファイルをスキップできます。
    * プライベートパッケージの場合: パッケージのフォルダとその依存関係のフォルダを zip 圧縮します。

4. (ステップ 1 から) アーティファクトの場所の BLOB に zip 形式のファイルをアップロードします。

5. プラグインの`python`呼び出し:
    * 名前の`external_artifacts`プロパティ バッグと zip ファイル (BLOB の URL) への参照を持つパラメーターを指定します。
    * インライン Python コードで:`Zipackage`から`sandbox_utils`インポートし、zip ファイルの名前を使用してその`install()`メソッドを呼び出します。

### <a name="example"></a>例

偽のデータを生成する[Faker](https://pypi.org/project/Faker/)パッケージをインストールします。

```kusto
range Id from 1 to 3 step 1 
| extend Name=''
| evaluate python(typeof(*),
    'from sandbox_utils import Zipackage\n'
    'Zipackage.install("Faker.zip")\n'
    'from faker import Faker\n'
    'fake = Faker()\n'
    'result = df\n'
    'for i in range(df.shape[0]):\n'
    '    result.loc[i, "Name"] = fake.name()\n',
    external_artifacts=pack('faker.zip', 'https://artifacts.blob.core.windows.net/kusto/Faker.zip?...'))
```

| Id | 名前         |
|----|--------------|
|   1| ゲイリー・タティア   |
|   2| エマ・エヴァンス   |
|   3| アシュリー・ボーエン |

---

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
