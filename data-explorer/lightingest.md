---
title: LightIngest を使用して Azure Data Explorer にデータを取り込みます。
description: Azure Data Explorer へのアドホック データ インジェストのためのコマンドライン ユーティリティである LightIngest について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/28/2020
ms.openlocfilehash: 1825ef642e5427df58800c8d6a71f75ff484bcf5
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872643"
---
# <a name="use-lightingest-to-ingest-data-to-azure-data-explorer"></a>LightIngest を使用して Azure Data Explorer にデータを取り込む
 
LightIngest は、Azure Data Explorer へのアドホック データ インジェストのためのコマンドライン ユーティリティです。 このユーティリティは、ローカル フォルダーまたは Azure Blob Storage コンテナーからソース データをプルできます。
LightIngest は、インジェスト期間に時間の制約がないため、大量のデータを取り込む必要がある場合に最も役立ちます。 また、クエリの (取り込み時間ではなく) 作成時間に従って後でレコードのクエリを実行する場合にも役立ちます。

## <a name="prerequisites"></a>前提条件

* LightIngest - [Microsoft.Azure.Kusto.Tools NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)の一部としてダウンロードします

    :::image type="content" source="media/lightingest/lightingest-download-area.png" alt-text="Lightingest のダウンロード":::

* WinRAR - [www.win-rar.com/download.html](http://www.win-rar.com/download.html) からダウンロードします

## <a name="install-lightingest"></a>LightIngest をインストールする

1. LightIngest をダウンロードしたコンピューター上の場所に移動します。
1. WinRAR を使用して、*tools* ディレクトリをコンピューターに抽出します。

## <a name="run-lightingest"></a>LightIngest を実行する

1. コンピューター上の抽出した *tools* ディレクトリに移動します。
1. 場所バーから既存の場所情報を削除します。
    
    :::image type="content" source="kusto/tools/images/KustoTools-Lightingest/lightingest-locationbar.png" alt-text="LightIngest の既存の場所情報を削除する":::

1. 「`cmd`」と入力して、**Enter** キーを押します。
1. コマンド プロンプトで、「`LightIngest.exe`」に続けて関連するコマンドライン引数を入力します。

    > [!Tip]
    > サポートされているコマンド ライン引数を確認するには、「`LightIngest.exe /help`」と入力します。
    >
    > :::image type="content" source="media/lightingest/lightingest-cmd-line-help.png" alt-text="LightIngest のコマンド ライン ヘルプ":::

1. 「`ingest-`」に続けて、インジェストを管理する Azure Data Explorer クラスターへの接続文字列を入力します。
    接続文字列を二重引用符で囲み、その後に [Kusto 接続文字列の指定](kusto/api/connection-strings/kusto.md)を入力します。

    次に例を示します。

    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

### <a name="recommendations"></a>推奨事項

* 推奨される方法は、`https://ingest-{yourClusterNameAndRegion}.kusto.windows.net` のインジェスト エンドポイントで LightIngest を動作させることです。 これにより、Azure Data Explorer サービスはインジェストの負荷を管理でき、一時的なエラーから簡単に回復できます。 ただし、エンジン エンドポイント (`https://{yourClusterNameAndRegion}.kusto.windows.net`) で直接動作するように LightIngest を構成することもできます。

   > [!NOTE]
   > エンジン エンドポイントで直接取り込む場合、`ingest-` を含める必要はありません。 ただし、エンジンを保護し、インジェストの成功率を向上させる DM 機能はありません。

* インジェストのパフォーマンスを最適にするには、圧縮されていないローカル ファイルのサイズを LightIngest が推定できるように、生データのサイズが必要です。 ただし、LightIngest では、先にダウンロードしない限り、圧縮された BLOB の生のサイズを正しく見積もることができないことがあります。 したがって、圧縮された BLOB を取り込むときは、BLOB メタデータの `rawSizeBytes` プロパティを圧縮されていないデータ サイズ (バイト単位) に設定します。

## <a name="command-line-arguments"></a>コマンド ライン引数

|引数名            |Type     |説明       |必須/省略可能
|------------------------------|--------|----------|-----------------------------|
|                               |string   |インジェストを処理する Kusto エンドポイントを指定する [Azure Data Explorer 接続文字列](kusto/api/connection-strings/kusto.md)。 二重引用符で囲む必要があります | Mandatory
|-database、-db          |string  |対象の Azure Data Explorer データベースの名前 | 省略可能  |
|-table                  |string  |対象の Azure Data Explorer テーブルの名前 | Mandatory |
|-sourcePath、-source      |string  |ソース ファイルまたは BLOB コンテナーのルート URI へのパス。 データが BLOB 内にある場合は、ストレージ アカウント キーまたは SAS が含まれている必要があります。 二重引用符で囲むことをお勧めします |Mandatory |
|-prefix                  |string  |取り込むソース データが Blob Storage に存在する場合、この URL プレフィックスが、コンテナー名を除くすべての BLOB で共有されます。 <br>たとえば、データが `MyContainer/Dir1/Dir2` にある場合は、プレフィックスを `Dir1/Dir2` にする必要があります。 二重引用符で囲むことをお勧めします | 省略可能  |
|-pattern        |string  |ソース ファイルまたは BLOB を選択するパターン。 ワイルドカードがサポートされます。 たとえば、「 `"*.csv"` 」のように入力します。 二重引用符で囲むことをお勧めします | 省略可能  |
|-zipPattern     |string  |取り込む ZIP アーカイブ内のファイルを選択するときに使用する正規表現。<br>アーカイブ内の他のファイルはすべて無視されます。 たとえば、「 `"*.csv"` 」のように入力します。 二重引用符で囲むことをお勧めします | 省略可能  |
|-format、-f           |string  | ソース データの形式。 [サポートされている形式](ingestion-supported-formats.md)のいずれかである必要があります | 省略可能  |
|-ingestionMappingPath、-mappingPath |string  |インジェスト列マッピング用のローカル ファイルのパス。 Json および Avro 形式の場合は必須です。 「[データ マッピング](kusto/management/mappings.md)」を参照してください | 省略可能  |
|-ingestionMappingRef、-mappingRef  |string  |テーブルで既に作成されているインジェスト列マッピングの名前。 Json および Avro 形式の場合は必須です。 「[データ マッピング](kusto/management/mappings.md)」を参照してください | 省略可能  |
|-creationTimePattern      |string  |設定した場合、ファイルまたは BLOB のパスから CreationTime プロパティを抽出するために使用されます。 「[`CreationTime` を使用してデータを取り込む方法](#how-to-ingest-data-using-creationtime)」をご覧ください |省略可能  |
|-ignoreFirstRow、-ignoreFirst |[bool]    |設定した場合、各ファイルまたは BLOB の最初のレコードが無視されます (たとえば、ソース データにヘッダーが含まれている場合) | 省略可能  |
|-tag            |string   |取り込まれたデータと関連付けるための[タグ](kusto/management/extents-overview.md#extent-tagging)。 複数回の出現が許可されています | 省略可能  |
|-dontWait           |[bool]     |"true" に設定した場合、インジェストの完了を待機しません。 大量のファイルまたは BLOB を取り込むときに便利です |省略可能  |
|-compression、-cr          |double |圧縮率のヒント。 圧縮されたファイルまたは BLOB を取り込むときに、Azure Data Explorer が生データのサイズを評価できるようにするのに便利です。 元のサイズを圧縮サイズで割った値として計算されます |省略可能  |
|-limit、-l           |整数 (integer)   |設定した場合、インジェストを最初の N 個のファイルに限定します |省略可能  |
|-listOnly、-list        |[bool]    |設定した場合、インジェストに選択された項目のみが表示されます| 省略可能  |
|-ingestTimeout   |整数 (integer)  |すべての取り込み操作が完了するまでのタイムアウト (分)。 既定値は `60` です| 省略可能  |
|-forceSync        |[bool]  |設定した場合、同期インジェストを強制的に実行します。 既定値は `false` です |省略可能  |
|-dataBatchSize        |整数 (integer)  |各取り込み操作の合計サイズ制限 (MB、非圧縮) を設定します |省略可能  |
|-filesInBatch            |整数 (integer) |各取り込み操作のファイルまたは BLOB の数の制限を設定します |省略可能  |
|-devTracing、-trace       |string    |設定した場合、診断ログがローカル ディレクトリに書き込まれます (既定では現在のディレクトリの `RollingLogs` ですが、スイッチ値を設定して変更できます) | 省略可能  |

## <a name="azure-blob-specific-capabilities"></a>Azure BLOB に固有の機能

Azure BLOB で使用する場合、LightIngest では特定の BLOB メタデータ プロパティを使用してインジェスト プロセスが拡張されます。

|メタデータのプロパティ                            | 使用法                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | 設定すると、非圧縮データ サイズとして解釈されます                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | UTC タイムスタンプとして解釈されます。 設定すると、Kusto の作成時刻を上書きするために使用されます。 バックフィル シナリオに役立ちます |

## <a name="usage-examples"></a>使用例

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Ingestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="how-to-ingest-data-using-creationtime"></a>CreationTime を使用してデータを取り込む方法

既存のシステムの履歴データを Azure Data Explorer に取り込むと、すべてのレコードに同じインジェスト日付が付与されます。 `-creationTimePattern` 引数を使用すると、データをインジェスト時間ではなく作成時間でパーティション分割できます。 `-creationTimePattern` 引数を指定すると、ファイルまたは BLOB のパスから `CreationTime` プロパティが抽出されます。 パターンに項目のパス全体を反映する必要はなく、使用するタイムスタンプを囲むセクションだけでかまいません。

引数の値には次の値を含める必要があります。
* 単一引用符で囲まれたタイムスタンプ形式の直前の定数テキスト (プレフィックス)
* タイムスタンプ形式 (標準 [.NET DateTime 表記](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings))
* タイムスタンプの直後の定数テキスト (サフィックス)。

**使用例** 

* 次の形式の datetime を含む BLOB 名: `historicalvalues19840101.parquet` (タイムスタンプは 4 桁の年、2 桁の月、2 桁の日の数字)、 
    
    `-creationTimePattern` 引数の値は次のファイル名の一部です: *"' historicalvalues'yyyyMMdd ' parquet'"*

    ```kusto
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
     -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* 次の形式で階層フォルダー構造を参照する BLOB URI: `https://storageaccount/container/folder/2002/12/01/blobname.extension`、 

    `-creationTimePattern` 引数の値は次のフォルダー構造の一部です: *"'folder/'yyyy/MM/dd'/blob'"*

   ```kusto
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'folder/'yyyy/MM/dd'/blob'"
     -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>ストレージ アカウント キーまたは SAS トークンを使用して BLOB を取り込む

* ストレージ アカウント `ACCOUNT`、フォルダー `DIR`、コンテナー `CONT`、一致パターン `*.csv.gz` を指定して、10 個の BLOB を取り込みます
* 取り込み先はデータベース `DB`のテーブル `TABLE` であり、取り込み先にインジェスト マッピング `MAPPING` が事前に作成されています
* ツールは、取り込み操作が完了するまで待機します
* 以下を指定するための異なるオプションに注意してください: ターゲット データベース、ストレージ アカウント キーと SAS トークン

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>ヘッダー行を含めずに、コンテナー内のすべての BLOB を取り込む

* ストレージ アカウント `ACCOUNT`、フォルダー `DIR1/DIR2`、コンテナー `CONT`、一致パターン `*.csv.gz` を指定して、すべての BLOB を取り込みます
* 取り込み先はデータベース `DB`のテーブル `TABLE` であり、取り込み先にインジェスト マッピング `MAPPING` が事前に作成されています
* ソース BLOB にはヘッダー行が含まれるため、各 BLOB の最初のレコードを削除するようツールに指示します
* ツールは、インジェストのためのデータを送信し、取り込み操作が完了するのを待機しません

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>パスからすべての JSON ファイルを取り込む

* パス `PATH` の下にある、パターン `*.json` に一致するすべてのファイルを取り込みます
* 取り込み先はデータベース `DB`のテーブル `TABLE` であり、インジェスト マッピングはローカル ファイル `MAPPING_FILE_PATH` で定義されています
* ツールは、インジェストのためのデータを送信し、取り込み操作が完了するのを待機しません

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>ファイルを取り込んで、診断トレース ファイルを書き込む

* パス `PATH` の下にある、パターン `*.json` に一致するすべてのファイルを取り込みます
* 取り込み先はデータベース `DB`のテーブル `TABLE` であり、インジェスト マッピングはローカル ファイル `MAPPING_FILE_PATH` で定義されています
* ツールは、インジェストのためのデータを送信し、取り込み操作が完了するのを待機しません
* 診断トレース ファイルは、ローカル環境の `LOGS_PATH` フォルダーの下に書き込まれます

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
