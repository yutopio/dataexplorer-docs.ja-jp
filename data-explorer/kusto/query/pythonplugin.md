---
title: Python プラグイン-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの Python プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5ceafde1361c87d368237d0f8c71ad8d0708aec1
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108509"
---
# <a name="python-plugin"></a>Python プラグイン

::: zone pivot="azuredataexplorer"

Python プラグインは、Python スクリプトを使用してユーザー定義関数 (UDF) を実行します。 Python スクリプトは、入力として表形式のデータを取得し、表形式の出力を生成することが想定されています。
プラグインのランタイムは、クラスターのノード上で実行される[サンド](../concepts/sandboxes.md)ボックスでホストされます。

## <a name="syntax"></a>構文

*T* `|` `single``,` *script* *output_schema* `,` *external_artifacts*[`hint.distribution` (`per_node`)] `python(`output_schema スクリプト [`,` *script_parameters*] [external_artifacts] |  `evaluate` `=``)`

## <a name="arguments"></a>引数

* *output_schema*: Python `type`コードによって返される表形式データの出力スキーマを定義するリテラル。
    * 形式は次の`typeof(`とおりです: *ColumnName* `:` *ColumnType* [,...]`)`(例: `typeof(col1:string, col2:long)`)。
    * 入力スキーマを拡張するには、次の構文を使用します。`typeof(*, col1:string, col2:long)`
* *script*: 実行`string`する有効な Python スクリプトであるリテラル。
* *script_parameters*: 予約`kargs`さ`dynamic`れたディクショナリとして python スクリプトに渡される名前と値のペアのプロパティバッグである省略可能なリテラル ([予約済みの python 変数](#reserved-python-variables)を参照)。
* *ヒント: distribution*: プラグインの実行を複数のクラスターノードに分散するための省略可能なヒントです。
  * 既定値は `single` です。
  * `single`: スクリプトの1つのインスタンスがクエリデータ全体で実行されます。
  * `per_node`: Python ブロックの前にクエリが分散されている場合、スクリプトのインスタンスは、含まれているデータを介して各ノードで実行されます。
* *external_artifacts*: クラウドストレージ`dynamic`からアクセスできるアーティファクトの名前 & URL ペアのプロパティバッグであり、実行時にスクリプトで使用できるようにするための、省略可能なリテラルです。
  * このプロパティバッグで参照される Url は、次の場合に必要です。
  * クラスターの[コールアウトポリシー](../management/calloutpolicy.md)に含めます。
    2. 「[ストレージ接続文字列](../api/connection-strings/storage.md)」で説明されているように、一般公開されている場所にあるか、必要な資格情報を提供します。
  * アーティファクトは、スクリプトがローカル一時ディレクトリから使用できるようになり`.\Temp`ます。また、プロパティバッグに指定された名前がローカルファイル名として使用されます (以下の[例](#examples)を参照してください)。
  * 詳細については、以下の[付録](#appendix-installing-packages-for-the-python-plugin)を参照してください。

## <a name="reserved-python-variables"></a>予約済み Python 変数

次の変数は、Kusto クエリ言語と Python コード間のやり取りのために予約されています。

* `df`: 入力表形式のデータ (上記の`T`値) は、 `pandas`データフレームとして指定します。
* `kargs`: Python ディクショナリとしての*script_parameters*引数の値。
* `result`: Python `pandas`スクリプトによって作成されたデータフレーム。値は、プラグインに従う Kusto クエリ演算子に送信される表形式のデータになります。

## <a name="onboarding"></a>オンボード

* プラグインは既定で無効になっています。
* プラグインを有効にするための前提条件については、[こちら](../concepts/sandboxes.md#prerequisites)を参照してください。
* [クラスターの [**構成**] タブで、Azure portal](https://docs.microsoft.com/azure/data-explorer/language-extensions)のプラグインを有効または無効にします。

## <a name="notes-and-limitations"></a>メモと制限事項

* Python sandbox イメージは、 *python 3.6*エンジンを使用した*Anaconda 5.2.0*ディストリビューションに基づいています。
  パッケージの一覧については、[こちら](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/)を参照してください (パッケージの小さな割合は、プラグインが実行されるサンドボックスによって適用される制限と互換性がない場合があります)。
* Python イメージ`tensorflow`には、共通の ML パッケージ、 `keras` `torch` `hdbscan`、、、 `xgboost`およびその他の便利なパッケージも含まれています。
* このプラグインは*numpy* 、既定で`np`numpy (as) & `pd`*パンダ*(as) をインポートします。  必要に応じて、他のモジュールをインポートできます。
* **クエリおよび[更新ポリシー](../management/updatepolicy.md) [からのインジェスト](../management/data-ingestion/ingest-from-query.md)**
  * 次のようなクエリでプラグインを使用することができます。
      1. 更新ポリシーの一部として定義され、ソーステーブルが*非ストリーミング*インジェストを使用するように取り込まれたます。
      2. クエリから取り込みするコマンドの一部として実行します ( `.set-or-append`例:)。
  * 上記のいずれの場合でも、取り込みの量と頻度、および Python ロジックの複雑さとリソースの使用率が[サンドボックスの制限](../concepts/sandboxes.md#limitations)とクラスターの使用可能なリソースに合わせて調整されていることを確認することをお勧めします。
    これを行わないと、[調整エラー](../concepts/sandboxes.md#errors)が発生する可能性があります。
  * 更新ポリシーの一部として定義されているクエリでプラグインを使用することはでき*ません*。このクエリは、[ストリーミングインジェスト](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming)を使用してソーステーブルを取り込まれたします。

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
:::image type="content" source="images/samples/sine-demo.png" alt-text="サインのデモ":::

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

* プラグインの入力データセットを必要な最小量 (列/行) に減らします。
    * 可能であれば、ソースデータセットのフィルターを Kusto のクエリ言語で使用します。
    * ソース列のサブセットに対して計算を実行するには、プラグインを呼び出す前に、その列だけをプロジェクトに含めます。
* スクリプト`hint.distribution = per_node`内のロジックが再頒布可能な場合は常にを使用します。
    * 入力データセットをパーティション分割するために、 [partition 演算子](partitionoperator.md)を使用することもできます。
* 可能な場合は常に Kusto のクエリ言語を使用して、Python スクリプトのロジックを実装します。

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

* で Python スクリプトを含む複数`Kusto.Explorer`行の文字列を生成するには、任意の python エディター (*Jupyter*、 *Visual Studio Code*、 *PyCharm*など) から python スクリプトをコピーし、次のいずれかの方法を使用します。
    * *F2*キーを押して、[ **Python で編集**] ウィンドウを開きます。 スクリプトをこのウィンドウに貼り付けます。 **[OK]** を選択します。 スクリプトは引用符と改行で修飾され (Kusto で有効)、[クエリ] タブに自動的に貼り付けられます。
    * Python コードを [クエリ] タブに直接貼り付けて、これらの行を選択し、 *ctrl + k*キー、 *ctrl + S*キーを押して、上記のように装飾します (逆にするには、 *ctrl + k*、 *ctrl + M*ホットキーを押します)。 クエリエディターのショートカットの完全な一覧を[次](../tools/kusto-explorer-shortcuts.md#query-editor)に示します。
* Kusto 文字列の区切り記号と Python 文字列リテラルの間の競合を避けるために、kusto クエリの Kusto 文字列リテラルには単一引用符 (`'`) を使用し`"`、Python スクリプトでは python 文字列リテラルに二重引用符文字 () を使用することをお勧めします。
* [Externaldata 演算子](externaldata-operator.md)を使用して、Azure Blob storage などの外部の場所に格納したスクリプトの内容を取得します。
  
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

## <a name="appendix-installing-packages-for-the-python-plugin"></a>付録: Python プラグインのパッケージのインストール

次のいずれかの理由により、パッケージの自動インストールが必要になる場合があります。

* パッケージはプライベートであり、独自のものです。
* パッケージは公開されていますが、プラグインの基本イメージに含まれていません。

パッケージをインストールするには、次の手順を実行します。

1. 1回限りの前提条件:
  
  a。 パッケージをホストする blob コンテナーを作成します。可能であれば、クラスターと同じリージョンで作成します。
    * 例: ( `https://artifcatswestus.blob.core.windows.net/python`クラスターが米国西部にあることを前提としています)
  
  b. クラスターの[コールアウトポリシー](../management/calloutpolicy.md)を変更して、その場所にアクセスできるようにします。
    * これには[All、admin](../management/access-control/role-based-authorization.md)のアクセス許可が必要です。
    * たとえば、に`https://artifcatswestus.blob.core.windows.net/python`ある blob へのアクセスを有効にするには、次のコマンドを実行します。

      ```kusto
      .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
      ```

2. パブリックパッケージ ( [PyPi](https://pypi.org/)またはその他のチャネル) の a。 パッケージとその依存関係をダウンロードします。
  b. 必要に応じて、wheel (`*.whl`) ファイルにコンパイルします。
    * (ローカルの Python 環境で) コマンドウィンドウから次のコマンドを実行します。
      ```python
      pip wheel [-w download-dir] package-name.
      ```

3. 必要なパッケージとその依存関係を含む zip ファイルを作成します。

    * パブリックパッケージの場合: 前の手順でダウンロードしたファイルを zip 圧縮します。
    * メモ:
        * 親フォルダーでは`.whl` *なく*、ファイル自体を zip 形式にしてください。
        * 基本サンドボックス`.whl`イメージで同じバージョンのパッケージが既に存在する場合は、ファイルをスキップできます。
    * プライベートパッケージの場合: パッケージのフォルダーとその依存関係のフォルダーを zip 圧縮します。

4. 圧縮ファイルをアーティファクトの場所 (手順 1.) の blob にアップロードします。

5. プラグインを`python`呼び出しています:
    * 名前の`external_artifacts`プロパティバッグと zip ファイルへの参照 (BLOB の URL) を指定して、パラメーターを指定します。
    * インライン python コードで、から`Zipackage` `sandbox_utils`インポートし、その`install()`メソッドを zip ファイルの名前で呼び出します。

### <a name="example"></a>例

偽のデータを生成する[Faker](https://pypi.org/project/Faker/)パッケージのインストール:

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
|   1| Gary Tapia   |
|   2| Emma Evans   |
|   3| Ashley Bowen |

---

::: zone-end

::: zone pivot="azuremonitor"

これは、ではサポートされていません Azure Monitor

::: zone-end
