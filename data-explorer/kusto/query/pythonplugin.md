---
title: Python プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Python プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: e6439912d323b7677f6febc8b23068c880a735c2
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85902126"
---
# <a name="python-plugin"></a>Python プラグイン

::: zone pivot="azuredataexplorer"

Python プラグインは、Python スクリプトを使用してユーザー定義関数 (UDF) を実行します。 Python スクリプトは、入力として表形式のデータを取得し、表形式の出力を生成することが想定されています。
プラグインのランタイムは、クラスターのノード上で実行される[サンド](../concepts/sandboxes.md)ボックスでホストされます。

## <a name="syntax"></a>構文

*T* `|` `evaluate` [ `hint.distribution` `=` ( `single`  |  `per_node` )] `python(` *output_schema* `,` *スクリプト*[ `,` *script_parameters*] [ `,` *external_artifacts*]`)`

## <a name="arguments"></a>引数

* *output_schema*: `type` Python コードによって返される表形式データの出力スキーマを定義するリテラル。
    * 形式は、 `typeof(` *ColumnName* `:` *ColumnType*[,...] `)` です。たとえば、のように `typeof(col1:string, col2:long)` なります。
    * 入力スキーマを拡張するには、次の構文を使用します。`typeof(*, col1:string, col2:long)`
* *script*: `string` 実行する有効な Python スクリプトであるリテラル。
* *script_parameters*: 省略可能な `dynamic` リテラルです。 これは、予約済みの辞書として Python スクリプトに渡される名前と値のペアのプロパティバッグ `kargs` です。 詳細については、「[予約済みの Python 変数](#reserved-python-variables)」を参照してください。
* *ヒント: distribution*: プラグインの実行を複数のクラスターノードに分散するための省略可能なヒントです。
  * 既定値は `single` です。
  * `single`: スクリプトの1つのインスタンスがクエリデータ全体で実行されます。
  * `per_node`: Python ブロックの前にクエリが分散されている場合、スクリプトのインスタンスは、そのスクリプトに含まれているデータの各ノードで実行されます。
* *external_artifacts*: `dynamic` クラウドストレージからアクセスできるアーティファクトの名前と URL のペアのプロパティバッグである省略可能なリテラルです。 スクリプトを実行時に使用できるようにすることができます。
  * このプロパティバッグで参照される Url は、次のようにする必要があります。
    * クラスターの[コールアウトポリシー](../management/calloutpolicy.md)に含まれています。
    * パブリックに使用可能な場所に保存するか、「[ストレージ接続文字列](../api/connection-strings/storage.md)」で説明されているように、必要な資格情報を指定します。
  * アーティファクトは、スクリプトがローカル一時ディレクトリ () から使用できるようになり `.\Temp` ます。 プロパティバッグに指定された名前は、ローカルファイル名として使用されます。 [例](#examples)を参照してください。
  * 詳細については、「 [Python プラグインのパッケージのインストール](#install-packages-for-the-python-plugin)」を参照してください。 

## <a name="reserved-python-variables"></a>予約済み Python 変数

次の変数は、Kusto クエリ言語と Python コード間のやり取りのために予約されています。

* `df`: 入力表形式のデータ (上記の値) は、 `T` データフレームとして指定し `pandas` ます。
* `kargs`: Python ディクショナリとしての*script_parameters*引数の値。
* `result`: `pandas` Python スクリプトによって作成されたデータフレーム。その値は、プラグインに従う Kusto クエリ演算子に送信される表形式のデータになります。

## <a name="enable-the-plugin"></a>プラグインの有効化

* プラグインは既定で無効になっています。
* プラグインを有効にするには、[前提条件](../concepts/sandboxes.md#prerequisites)の一覧を参照してください。
* クラスターの [[構成] タブ](../../language-extensions.md)で、Azure portal のプラグインを有効または無効にします。

## <a name="python-sandbox-image"></a>Python sandbox イメージ

* Python sandbox イメージは、 *python 3.6*エンジンを使用した*Anaconda 5.2.0*ディストリビューションに基づいています。
  [Anaconda パッケージ](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/)の一覧を参照してください。
  
  > [!NOTE]
  > パッケージの小さな割合は、プラグインが実行されるサンドボックスによって適用される制限と互換性がない場合があります。
  
* Python イメージには、共通の ML パッケージ、、、、、 `tensorflow` `keras` `torch` `hdbscan` `xgboost` およびその他の便利なパッケージも含まれています。
* このプラグインは、既定で*numpy* (as) `np` &*パンダ*(as) をインポートし `pd` ます。  必要に応じて、他のモジュールをインポートできます。

## <a name="use-ingestion-from-query-and-update-policy"></a>クエリおよび更新ポリシーからインジェストを使用する

* 次のクエリでプラグインを使用します。
  * [更新ポリシー](../management/updatepolicy.md)の一部として定義され、ソーステーブルが*非ストリーミング*インジェストを使用するように取り込まれたます。
  * などの[クエリから取り込み](../management/data-ingestion/ingest-from-query.md)するコマンドの一部として実行し `.set-or-append` ます。
    どちらの場合も、インジェストのボリュームと頻度、および Python ロジックで使用される複雑さとリソースについて、[サンドボックスの制限](../concepts/sandboxes.md#limitations)とクラスターの使用可能なリソースについて確認してください。 この操作を行わないと、[調整エラー](../concepts/sandboxes.md#errors)が発生する可能性があります。
* 更新ポリシーの一部として定義されているクエリでプラグインを使用することはできません。このクエリでは、[ストリーミングインジェスト](../../ingest-data-streaming.md)を使用してソーステーブルを取り込まれたします。

## <a name="examples"></a>使用例

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

:::image type="content" source="images/plugin/sine-demo.png" alt-text="サインのデモ" border="false":::

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
* `hint.distribution = per_node`スクリプト内のロジックが再頒布可能な場合は常にを使用します。
    * 入力データセットをパーティション分割するために、 [partition 演算子](partitionoperator.md)を使用することもできます。
* Python スクリプトのロジックを実装するには、可能な限り Kusto のクエリ言語を使用します。

    **例**

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

* で Python スクリプトを含む複数行の文字列を生成するに `Kusto.Explorer` は、使い慣れた python エディター (*Jupyter*、 *Visual Studio Code*、 *PyCharm*など) から python スクリプトをコピーします。 
  次のいずれかの操作を行います。
    * **F2**キーを押して、[ *Python で編集*] ウィンドウを開きます。 スクリプトをこのウィンドウに貼り付けます。 **[OK]** を選択します。 スクリプトは引用符と改行で修飾されるので、Kusto で有効であり、自動的に [クエリ] タブに貼り付けられます。
    * Python コードを [クエリ] タブに直接貼り付けます。これらの行を選択し、 **ctrl + K**キー、 **ctrl + S**キーを押して、上記のように装飾します。 逆にするには、 **ctrl + K**キー、 **ctrl + M**キーの順に押します。 [クエリエディターのショートカット](../tools/kusto-explorer-shortcuts.md#query-editor)の完全な一覧を参照してください。
* Kusto 文字列の区切り記号と Python 文字列リテラルの間の競合を回避するには、次のように使用します。
     * Kusto `'` クエリの kusto 文字列リテラル用の単一引用符 ()
     * Python `"` スクリプトでの python 文字列リテラルの二重引用符文字 ()
* [ `externaldata` オペレーター](externaldata-operator.md)を使用して、Azure Blob storage などの外部の場所に格納したスクリプトの内容を取得します。
  
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

## <a name="install-packages-for-the-python-plugin"></a>Python プラグインのパッケージをインストールする

次の理由により、パッケージのインストールが必要になる場合があります。

* パッケージはプライベートであり、独自のものです。
* パッケージは公開されていますが、プラグインの基本イメージに含まれていません。

次のようにパッケージをインストールします。

### <a name="prerequisites"></a>必須コンポーネント

  1. パッケージをホストする blob コンテナーを作成します (可能であれば、クラスターと同じ場所に作成します)。 たとえば、 `https://artifcatswestus.blob.core.windows.net/python` クラスターが米国西部にあるとします。
  1. クラスターの[コールアウトポリシー](../management/calloutpolicy.md)を変更して、その場所にアクセスできるようにします。
        * この変更には、 [Alldatabasesadmin](../management/access-control/role-based-authorization.md)のアクセス許可が必要です。

        * たとえば、にある blob へのアクセスを有効にするには、 `https://artifcatswestus.blob.core.windows.net/python` 次のコマンドを実行します。

        ```kusto
        .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
        ```

### <a name="install-packages"></a>パッケージをインストールする

1. [PyPi](https://pypi.org/)または他のチャネルのパブリックパッケージの場合は、パッケージとその依存関係をダウンロードします。

   * `*.whl`必要に応じて、コンパイルホイール () ファイル。
   * ローカル Python 環境のコマンドウィンドウで、次のコマンドを実行します。
    
    ```python
    pip wheel [-w download-dir] package-name.
    ```

1. 必要なパッケージとその依存関係を含む zip ファイルを作成します。

    * プライベートパッケージの場合: パッケージのフォルダーとその依存関係のフォルダーを zip 圧縮します。
    * パブリックパッケージの場合は、前の手順でダウンロードしたファイルを zip 圧縮します。
    
    > [!NOTE]
    > * `.whl`親フォルダーではなく、ファイル自体を zip 形式にしてください。
    > * `.whl`基本サンドボックスイメージで同じバージョンのパッケージが既に存在する場合は、ファイルをスキップできます。

1. (手順1の) 成果物の場所にある blob に、zip 形式のファイルをアップロードします。

1. プラグインを呼び出し `python` ます。
    * `external_artifacts`名前のプロパティバッグと zip ファイルへの参照 (blob の URL) を指定して、パラメーターを指定します。
    * インライン python コードで、からを `Zipackage` インポート `sandbox_utils` し、 `install()` zip ファイルの名前を使用してそのメソッドを呼び出します。

### <a name="example"></a>例

偽のデータを生成する[Faker](https://pypi.org/project/Faker/)パッケージをインストールします。

```kusto
range ID from 1 to 3 step 1 
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

| id | 名前         |
|----|--------------|
|   1| Gary Tapia   |
|   2| Emma Evans   |
|   3| Ashley Bowen |

---

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
