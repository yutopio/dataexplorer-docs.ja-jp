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
ms.openlocfilehash: b470d017937ed6f2687016ab8a7cf53fed7b51ab
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614475"
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

|プロパティ        |Type    |説明                                                                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------|
|`sizeLimit`     |`long`  |1つのストレージアーティファクト (圧縮前) のサイズ制限 (バイト単位)。 許容範囲は 100 MB (既定値) ~ 1 GB です。|
|`includeHeaders`|`string`|出力の場合は `csv` / `tsv` 、列ヘッダーの生成を制御します。 のいずれか `none` (既定値、ヘッダー行は出力されません)、 `all` (ヘッダー行をすべてのストレージ成果物に出力する)、または `firstFile` (1 つ目のストレージ成果物にのみヘッダー行を出力する) を指定できます。|
|`fileExtension` |`string`|ストレージアーティファクトの "拡張機能" 部分 (やなど) を示し `.csv` `.tsv` ます。 圧縮が使用されている場合は、 `.gz` も追加されます。|
|`namePrefix`    |`string`|生成された各ストレージアーティファクト名に追加するプレフィックスを示します。 指定されていない場合は、ランダムなプレフィックスが使用されます。       |
|`encoding`      |`string`|テキストをエンコードする方法を示します。 `UTF8NoBOM` (既定値) または `UTF8BOM` 。 |
|`compressionType`|`string`|使用する圧縮の種類を示します。 指定できる値は `gzip` または `snappy` です。 既定値は `gzip` です。 `snappy` 形式には、(必要に応じて) 使用でき `parquet` ます。 |
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

* [。操作を表示](../operations.md#show-operations)します。進行状況を追跡します。
* [。操作の詳細を表示](../operations.md#show-operation-details)します。完了の結果を取得します。

たとえば、正常に完了した後、次のようにを使用して結果を取得できます。

```kusto
.show operation f008dc1e-2710-47d8-8d34-0d562f5f8615 details
```

**使用例** 

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

#### <a name="known-issues"></a>既知の問題

**Export コマンドの実行中に発生したエラー**

* エクスポートコマンドは、実行中に失敗する可能性があります。 エクスポートコマンドが失敗した場合、既にストレージに書き込まれていたアーティファクトは削除されません。 これらのアーティファクトはストレージに残ります。 コマンドが失敗した場合、一部のアーティファクトが書き込まれていても、エクスポートが不完全であると想定します。 コマンドの完了と正常に完了したときにエクスポートされた成果物の両方を追跡する最良の方法は、 [. show 操作](../operations.md#show-operations) と [. 操作の詳細の表示](../operations.md#show-operation-details) コマンドを使用することです。

* 既定では、エクスポートコマンドは、データを含むすべての [エクステント](../extents-overview.md) がストレージへの書き込みを同時にエクスポートするように分散されます。 大規模なエクスポートでは、このようなエクステントの数が多い場合、ストレージの調整または一時的なストレージエラーが発生するストレージの負荷が高くなる可能性があります。 このような場合は、export コマンドに提供されるストレージアカウントの数を増やして (負荷がアカウント間で分散される)、または distribution ヒントをに設定して同時実行を減らすことをお勧めし `per_node` ます (「コマンドのプロパティ」を参照してください)。 ディストリビューションを完全に無効にすることもできますが、これはコマンドのパフォーマンスに大きな影響を与える可能性があります。
