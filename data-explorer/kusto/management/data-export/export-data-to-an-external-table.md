---
title: 外部テーブルへのデータのエクスポート - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで外部テーブルにデータをエクスポートする方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: e26d1ae163b69f3a04c52538c245ea446dc11199
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521643"
---
# <a name="export-data-to-an-external-table"></a>外部テーブルへのデータのエクスポート

[外部テーブル](../externaltables.md)を定義し、データをエクスポートすることで、データをエクスポートできます。
テーブルのプロパティは[外部テーブルを作成](../externaltables.md#create-or-alter-external-table)するときに指定されるため、エクスポート コマンドにテーブルのプロパティを埋め込む必要はありません。 エクスポート コマンドは、外部テーブルを名前で参照します。
データのエクスポートには[、データベースの管理者権限](../access-control/role-based-authorization.md)が必要です。

> [!NOTE] 
> * 接続文字列を使用`impersonate`して外部テーブルにエクスポートすることは、現在サポートされていません。

**構文 :**

`.export`[`async` `to` `table` ]*外部テーブル名* <br>
`with``(`*プロパティ*名`=`*プロパティ値*`,`..`)`] <|*クエリ*

**出力：**

|出力パラメーター |Type |説明
|---|---|---
|外部テーブル名  |String |外部テーブルの名前。
|Path|String|出力パス。
|数字の記録|String| パスにエクスポートされたレコードの数。

**注:**
* エクスポート クエリ出力スキーマは、パーティションで定義されたすべての列を含む外部テーブルのスキーマと一致する必要があります。 たとえば、テーブルが*DateTime*でパーティション分割されている場合、クエリ出力スキーマには、外部テーブルパーティション定義で定義されている*TimestampColumnName*と一致する Timestamp 列が含まれている必要があります。

* エクスポート コマンドを使用して外部テーブルのプロパティをオーバーライドすることはできません。
 たとえば、データ形式が CSV である外部テーブルに Parquet 形式でデータをエクスポートすることはできません。

* 次のプロパティは、エクスポート コマンドの一部としてサポートされています。 詳細については、「[ストレージへのエクスポート](export-data-to-storage.md)」セクションを参照してください。 
   * `sizeLimit`, `parquetRowGroupSize`, `distributed`.

* 外部テーブルがパーティション化されている場合、エクスポートされたアーティファクトは、[例](#partitioned-external-table-example)に示すようにパーティション定義に従ってそれぞれのディレクトリに書き込まれます。 

* パーティションごとに書き込まれるファイルの数は、次の内容によって異なります。
   * 外部テーブルに datetime パーティションのみが含まれている場合、またはパーティションがまったく含まれていなくても、(パーティションごとに) 書き込まれるファイルの数は、クラスター内のノード数 (到達した`sizeLimit`場合は複数) の前後にする必要があります。 これは、エクスポート操作が分散され、クラスター内のすべてのノードが同時にエクスポートされるためです。 
   ディストリビューションを無効にして、1 つのノードだけが書き込みを`distributed`実行するようにするには、false に設定します。 これにより、作成されるファイルは少なくなりますが、エクスポートのパフォーマンスは低下します。

   * 外部テーブルに文字列列によるパーティションが含まれている場合、エクスポートされるファイルの数は、パーティションごとに 1 つのファイル (または`sizeLimit`、到達した場合) にする必要があります。 すべてのノードは引き続きエクスポートに参加しますが (操作は分散されます)、各パーティションは特定のノードに割り当てられます。 この`distributed`場合 false に設定すると、エクスポートを実行するノードは 1 つのみになりますが、動作は同じままです (パーティションごとに 1 つのファイルが書き込まれます)。

## <a name="examples"></a>例

### <a name="non-partitioned-external-table-example"></a>非パーティション化外部表の例

外部 BLOB は、非パーティション化された外部テーブルです。 
```kusto
.export to table ExternalBlob <| T
```

|外部テーブル名|Path|数字の記録|
|---|---|---|
|外部 BLOB|http://storage1.blob.core.windows.net/externaltable1cont1/1_58017c550b384c0db0fea61a8661333e.csv|10|

### <a name="partitioned-external-table-example"></a>パーティション化された外部テーブルの例

パーティション外部Blobは、次のように定義された外部テーブルです。 

```
.create external table PartitionedExternalBlob (Timestamp:datetime, CustomerName:string) 
kind=blob
partition by 
   "CustomerName="CustomerName,
   bin(Timestamp, 1d)
dataformat=csv
( 
   h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

```
.export to table PartitionedExternalBlob <| T
```

|外部テーブル名|Path|数字の記録|
|---|---|---|
|外部 BLOB|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer1/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_1_58017c550b384c0db0fea61a8661333e.csv|10|
|外部 BLOB|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer2/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_2_b785beec2c004d93b7cd531208424dc9.csv|10|

コマンドが非同期に (キーワードを使用して)`async`実行される場合、出力は[show operation details](../operations.md#show-operation-details)コマンドを使用して使用できます。