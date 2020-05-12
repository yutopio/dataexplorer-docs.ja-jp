---
title: 外部テーブルへのデータのエクスポート-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの外部テーブルにデータをエクスポートする方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 7b4bade1ca874157ec843103a8bcf5236b49abfe
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227793"
---
# <a name="export-data-to-an-external-table"></a>外部テーブルへのデータのエクスポート

[外部テーブル](../externaltables.md)を定義し、データをエクスポートすることによって、データをエクスポートできます。
テーブルのプロパティは、[外部テーブルを作成](../external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table)するときに指定します。 Export コマンドにテーブルのプロパティを埋め込む必要はありません。 エクスポートコマンドは、外部テーブルを名前で参照します。 データのエクスポートには、[データベース管理者のアクセス許可](../access-control/role-based-authorization.md)が必要です。

**構文 :**

`.export`[ `async` ] `to` `table` *Externaltablename* <br>
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <|*クエリ*

**出力:**

|出力パラメーター |Type |説明
|---|---|---
|ExternalTableName  |String |外部テーブルの名前。
|Path|String|出力パス。
|NumRecords|String| Path にエクスポートされたレコードの数。

**注:**
* エクスポートクエリの出力スキーマは、パーティションで定義されているすべての列を含め、外部テーブルのスキーマと一致する必要があります。 たとえば、テーブルが*DateTime*でパーティション分割されている場合、クエリ出力スキーマには、タイムスタンプ*columnname*と一致する Timestamp 列が必要です。 この列名は、外部テーブルのパーティション分割定義で定義されています。

* Export コマンドを使用して外部テーブルのプロパティを上書きすることはできません。
 たとえば、Parquet 形式のデータを、データ形式が CSV である外部テーブルにエクスポートすることはできません。

* Export コマンドの一部として、次のプロパティがサポートされています。 詳細については、「[ストレージへのエクスポート](export-data-to-storage.md)」を参照してください。 
   * `sizeLimit`, `parquetRowGroupSize`, `distributed`.

   * 外部テーブルがパーティション分割されている場合、エクスポートされたアイテムは、[例](#partitioned-external-table-example)に示されているパーティション定義に従って、それぞれのディレクトリに書き込まれます。 

* パーティションごとに書き込まれるファイルの数は、設定によって異なります。
   * 外部テーブルに datetime パーティションだけが含まれている場合、またはパーティションがまったく存在しない場合は、書き込まれたファイルの数 (各パーティションの場合は、存在する場合) がクラスター内のノードの数と同じである必要があります (以上の場合) `sizeLimit` 。 エクスポート操作が分散されると、クラスター内のすべてのノードが同時にエクスポートされます。 ディストリビューションを無効にして、1つのノードだけが書き込みを実行するようにするには、を `distributed` false に設定します。 このプロセスによって作成されるファイルの量は少なくなりますが、エクスポートのパフォーマンスが低下します。

   * 外部テーブルに文字列型の列によってパーティションが含まれている場合、エクスポートされるファイルの数は、パーティションごとに1つのファイルである必要があります (またはに到達した場合 `sizeLimit` )。 すべてのノードは引き続きエクスポートに参加します (操作は分散されます) が、各パーティションは特定のノードに割り当てられます。 `distributed`この場合、を false に設定すると、1つのノードのみがエクスポートを実行しますが、動作は同じままです (パーティションごとに1つのファイルが書き込まれます)。

## <a name="examples"></a>使用例

### <a name="non-partitioned-external-table-example"></a>パーティション分割されていない外部テーブルの例

ExternalBlob は、パーティション分割されていない外部テーブルです。 
```kusto
.export to table ExternalBlob <| T
```

|ExternalTableName|Path|NumRecords|
|---|---|---|
|ExternalBlob|http://storage1.blob.core.windows.net/externaltable1cont1/1_58017c550b384c0db0fea61a8661333e.csv|10|

### <a name="partitioned-external-table-example"></a>パーティション分割された外部テーブルの例

PartitionedExternalBlob は、次のように定義された外部テーブルです。 

```kusto
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

```kusto
.export to table PartitionedExternalBlob <| T
```

|ExternalTableName|Path|NumRecords|
|---|---|---|
|ExternalBlob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer1/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_1_58017c550b384c0db0fea61a8661333e.csv|10|
|ExternalBlob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer2/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_2_b785beec2c004d93b7cd531208424dc9.csv|10|

コマンドが (キーワードを使用して) 非同期的に実行された場合、[ `async` [操作の詳細を表示](../operations.md#show-operation-details)] コマンドを使用して出力を取得できます。
