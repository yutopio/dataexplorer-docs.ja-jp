---
title: データをストレージにエクスポートする-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのストレージにデータをエクスポートする方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: fd0ac46aa0e2cf73cf0ee5a51359cd346bf1beda
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321558"
---
# <a name="export-data-to-storage"></a>データをストレージにエクスポートする

クエリを実行し、最初の結果セットを [ストレージ接続文字列](../../api/connection-strings/storage.md)で指定された外部ストレージに書き込みます。

**構文**

`.export`[ `async` ] [ `compressed` ] `to` *OutputDataFormat* 
 `(` *StorageConnectionString* [ `,` ...] `)`[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|`*クエリ*

**引数**

* `async`: 指定した場合、コマンドが非同期モードで実行されることを示します。
  このモードの動作の詳細については、以下を参照してください。

* `compressed`: 指定した場合、出力ストレージの成果物はファイルとして圧縮され `.gz` ます。 `compressionType`Parquet ファイルを snappy として圧縮する場合は、「」を参照してください。 

* *OutputDataFormat*: コマンドによって書き込まれたストレージアーティファクトのデータ形式を示します。 サポートされている値は、、、 `csv` `tsv` `json` 、および `parquet` です。

* *StorageConnectionString*: データの書き込み先のストレージを示す1つ以上の [ストレージ接続文字列](../../api/connection-strings/storage.md) を指定します。 (スケーラブルな書き込みでは、複数のストレージ接続文字列を指定できます)。このような接続文字列は、ストレージへの書き込み時に使用する資格情報を示す必要があります。
  たとえば、Azure Blob Storage に書き込む場合、資格情報はストレージアカウントキー、または blob の読み取り、書き込み、および一覧表示のアクセス許可を持つ共有アクセスキー (SAS) のいずれかになります。

> [!NOTE]
> Kusto クラスター自体と同じリージョンに併置されているストレージにデータをエクスポートすることを強くお勧めします。 これには、他のリージョンの別のクラウドサービスに転送できるようにエクスポートされたデータが含まれます。 書き込みはローカルで実行する必要がありますが、読み取りはリモートで行われます。

* *PropertyName* /*PropertyValue*: 0 個以上のオプションのエクスポートプロパティ:

|プロパティ        |種類    |説明                                                                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------|
|`sizeLimit`     |`long`  |1つのストレージアーティファクト (圧縮前) のサイズ制限 (バイト単位)。 許容範囲は 100 MB (既定値) ~ 1 GB です。|
|`includeHeaders`|`string`|出力の場合は `csv` / `tsv` 、列ヘッダーの生成を制御します。 のいずれか `none` (既定値、ヘッダー行は出力されません)、 `all` (ヘッダー行をすべてのストレージ成果物に出力する)、または `firstFile` (1 つ目のストレージ成果物にのみヘッダー行を出力する) を指定できます。|
|`fileExtension` |`string`|ストレージアーティファクトの "拡張機能" 部分 (やなど) を示し `.csv` `.tsv` ます。 圧縮が使用されている場合は、 `.gz` も追加されます。|
|`namePrefix`    |`string`|生成された各ストレージアーティファクト名に追加するプレフィックスを示します。 指定されていない場合は、ランダムなプレフィックスが使用されます。       |
|`encoding`      |`string`|テキストをエンコードする方法を示します。 `UTF8NoBOM` (既定値) または `UTF8BOM` 。 |
|`compressionType`|`string`|使用する圧縮の種類を示します。 指定できる値は `gzip` または `snappy` です。 既定値は `gzip`です。 `snappy` 形式には、(必要に応じて) 使用でき `parquet` ます。 |
|`distribution`   |`string`  |配布ヒント ( `single` 、 `per_node` 、 `per_shard` )。 値がと等しい場合 `single` 、1つのスレッドがストレージに書き込みます。 それ以外の場合、エクスポートでは、クエリを並列実行するすべてのノードから書き込みが行われます。 「 [Evaluate plugin operator](../../query/evaluateoperator.md)」を参照してください。 既定値は `per_shard` です。
|`distributed`   |`bool`  |分散エクスポートを無効/有効にします。 を false に設定することは、distribution ヒントと同じです `single` 。 既定値は true です。
|`persistDetails`|`bool`  |コマンドが結果を永続化する必要があることを示します (フラグを参照 `async` )。 `true`非同期実行では、は既定でになりますが、呼び出し元が結果を必要としない場合はオフにすることができます。 `false`同期実行では、が既定でに設定されますが、これらのモードでも有効にすることができます。 |
|`parquetRowGroupSize`|`int`  |データ形式が parquet の場合にのみ関連します。 エクスポートされたファイルの行グループのサイズを制御します。 既定の行グループのサイズは10万レコードです。|

**結果**

コマンドは、生成されたストレージアーティファクトを示すテーブルを返します。
各レコードには1つの成果物が記述され、アーティファクトへのストレージパスと、そのアイテムに保持されているデータレコードの数が含まれます。

|パス|NumRecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/export_1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/export_2_454c0f1359e24795b6529da8a0101330.csv|15|

**非同期モード**

フラグが指定されている場合、 `async` コマンドは非同期モードで実行されます。
このモードでは、コマンドは操作 ID を使用して直ちに戻り、データのエクスポートは完了するまでバックグラウンドで続行されます。 コマンドによって返される操作 ID は、次のコマンドを使用して、進行状況と最終的な結果を追跡するために使用できます。

* [`.show operations`](../operations.md#show-operations): 進行状況を追跡します。
* [`.show operation details`](../operations.md#show-operation-details): 入力候補の結果を取得します。

たとえば、正常に完了した後、次のようにを使用して結果を取得できます。

```kusto
.show operation f008dc1e-2710-47d8-8d34-0d562f5f8615 details
```

**例** 

この例では、Kusto はクエリを実行し、クエリによって作成された最初のレコードセットを1つ以上の圧縮された CSV blob にエクスポートします。
列名のラベルは、各 blob の最初の行として追加されます。

```kusto 
.export
  async compressed
  to csv (
    h@"https://storage1.blob.core.windows.net/containerName;secretKey",
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey"
  ) with (
    sizeLimit=100000,
    namePrefix=export,
    includeHeaders=all,
    encoding =UTF8NoBOM
  )
  <| myLogs | where id == "moshe" | limit 10000
```

## <a name="failures-during-export-commands"></a>エクスポートコマンドのエラー

エクスポートコマンドは、実行中に失敗する可能性があります。 [連続エクスポート](continuous-data-export.md) では、コマンドが自動的に再試行されます。 通常のエクスポートコマンド ([storage へのエクスポート](export-data-to-storage.md)、 [外部テーブルへのエクスポート](export-data-to-an-external-table.md)) では、再試行は実行されません。

*  エクスポートコマンドが失敗した場合、既にストレージに書き込まれていたアーティファクトは削除されません。 これらのアーティファクトはストレージに残ります。 コマンドが失敗した場合、一部のアーティファクトが書き込まれていても、エクスポートが不完全であると想定します。 
* コマンドの完了と正常に完了したときにエクスポートされた成果物の両方を追跡する最良の方法は、コマンドとコマンドを使用することです [`.show operations`](../operations.md#show-operations) [`.show operation details`](../operations.md#show-operation-details) 。

### <a name="storage-failures"></a>ストレージの障害

既定では、エクスポートコマンドは、ストレージへの同時書き込みが多数存在する可能性があるように配布されます。 ディストリビューションのレベルは、エクスポートコマンドの種類によって異なります。
* 標準コマンドの既定のディストリビューション `.export` はです `per_shard` 。これは、ストレージへの書き込みを同時にエクスポートするデータを含むすべての [エクステント](../extents-overview.md) を意味します。 
* [外部テーブルコマンドへのエクスポート](export-data-to-an-external-table.md)の既定のディストリビューションはです `per_node` 。これは、同時実行がクラスター内のノードの数であることを意味します。

エクステントまたはノードの数が多い場合は、ストレージの調整または一時的なストレージエラーが発生する記憶域の負荷が高くなる可能性があります。 次の推奨事項では、これらのエラーを優先順位に従って解決できます。

* エクスポートコマンドまたは [外部テーブル定義](../external-tables-azurestorage-azuredatalake.md) に対して提供されるストレージアカウントの数を増やします (負荷は、アカウント間で均等に分散されます)。
* ディストリビューションヒントをに設定して同時実行を減らし `per_node` ます (「コマンドのプロパティ」を参照してください)。
* [ [クライアント要求] プロパティ](../../api/netfx/request-properties.md)を `query_fanout_nodes_percent` 目的の同時実行 (ノードの割合) に設定して、エクスポートするノードの同時実行数を減らします。 プロパティは、エクスポートクエリの一部として設定できます。 たとえば、次のコマンドは、記憶域に書き込むノードの数を、クラスターノードの50% に同時に制限します。

    ```kusto
    .export async  to csv
        ( h@"https://storage1.blob.core.windows.net/containerName;secretKey" ) 
        with
        (
            distribuion="per_node"
        ) 
        <| 
        set query_fanout_nodes_percent = 50;
        ExportQuery
    ```

* パーティション分割された外部テーブルにエクスポートする場合、プロパティを設定すると `spread` / `concurrency` 同時実行性が低下する可能性があります (詳細については、[コマンドのプロパティ](export-data-to-an-external-table.md#syntax)を参照してください)
* 上記のいずれの操作も行わない場合、プロパティを false に設定してディストリビューションを完全に無効にすることもでき `distributed` ますが、コマンドのパフォーマンスに大きな影響を与える可能性があるため、この方法はお勧めしません。
