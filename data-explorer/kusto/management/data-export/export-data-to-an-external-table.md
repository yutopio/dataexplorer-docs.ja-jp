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
ms.openlocfilehash: 8b549ca239dac0e88e0a8c0f0748eb86984f5cb4
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97389006"
---
# <a name="export-data-to-an-external-table"></a>外部テーブルへのデータのエクスポート

[外部テーブル](../external-table-commands.md)を定義し、データをエクスポートすることによって、データをエクスポートできます。
テーブルのプロパティは、 [外部テーブルを作成](../external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table)するときに指定します。
エクスポートコマンドは、外部テーブルを名前で参照します。

このコマンドを実行するに [は、テーブル管理者またはデータベース管理者の権限](../access-control/role-based-authorization.md)が必要です。

## <a name="syntax"></a>構文

`.export` [ `async` ] `to` `table` *Externaltablename* <br>
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <|*クエリ*

**引数**

* *Externaltablename*: エクスポート先の外部テーブルの名前。
* *クエリ*: クエリをエクスポートします。
* *プロパティ*: 次のプロパティは、[外部テーブルへのエクスポート] コマンドの一部としてサポートされています。 
    * `sizeLimit`、 `parquetRowGroupSize` 、 `distributed` -これらのプロパティの詳細については、「 [ストレージへのエクスポート](export-data-to-storage.md) 」セクションを参照してください。
    * `spread`、 `concurrency` -プロパティを変更して、書き込み操作の同時実行を減らしたり増やしたりします。 詳細については、「 [partition operator](../../query/partitionoperator.md) 」を参照してください。 これらのプロパティは、 _文字列_ パーティションによってパーティション分割された外部テーブルにエクスポートする場合にのみ関連します。 既定では、同時にエクスポートされるノードの数は、64とクラスターノードの数の最小値になります。


## <a name="output"></a>Output

|出力パラメーター |型 |説明
|---|---|---
|ExternalTableName  |String |外部テーブルの名前。
|Path|String|出力パス。
|NumRecords|String| Path にエクスポートされたレコードの数。

## <a name="notes"></a>ノート

* エクスポートクエリの出力スキーマは、パーティションで定義されているすべての列を含め、外部テーブルのスキーマと一致する必要があります。 たとえば、テーブルが *DateTime* でパーティション分割されている場合、クエリ出力スキーマには、タイムスタンプ *columnname* と一致する Timestamp 列が必要です。 この列名は、外部テーブルのパーティション分割定義で定義されています。

* Export コマンドを使用して外部テーブルのプロパティを上書きすることはできません。
 たとえば、Parquet 形式のデータを、データ形式が CSV である外部テーブルにエクスポートすることはできません。

* 外部テーブルがパーティション分割されている場合は、パーティション分割された [外部テーブルの例](#partitioned-external-table-example)に示すように、エクスポートされたアイテムがパーティション定義に従ってそれぞれのディレクトリに書き込まれます。
  * パーティション値が null または空であるか、または無効なディレクトリ値の場合、ターゲットストレージの定義に従って、パーティション値は既定値のに置き換えられ `__DEFAULT_PARTITION__` ます。

* エクスポートコマンドの実行中にストレージエラーを解決するための推奨事項については、「 [エクスポートコマンドのエラー](export-data-to-storage.md#failures-during-export-commands)」を参照してください。

### <a name="number-of-files"></a>ファイルの数

パーティションごとに書き込まれるファイルの数は、設定によって異なります。

 * 外部テーブルに datetime パーティションだけが含まれている場合、またはパーティションがまったく存在しない場合は、書き込まれたファイルの数 (パーティションが存在する場合) は、クラスター内のノードの数に似ている必要があります (または、に到達した場合) `sizeLimit` 。 エクスポート操作が分散されると、クラスター内のすべてのノードが同時にエクスポートされます。 1つのノードのみが書き込みを行うようにディストリビューションを無効にするには、を `distributed` false に設定します。 このプロセスによって作成されるファイルの量は少なくなりますが、エクスポートのパフォーマンスが低下します。

* 外部テーブルに文字列型の列によってパーティションが含まれている場合、エクスポートされるファイルの数は、パーティションごとに1つのファイルである必要があります (またはに到達した場合 `sizeLimit` )。 すべてのノードは引き続きエクスポートに参加します (操作は分散されます) が、各パーティションは特定のノードに割り当てられます。 を false に設定すると、 `distributed` 1 つのノードのみがエクスポートされますが、動作は同じままです (パーティションごとに1つのファイルが書き込まれます)。

## <a name="examples"></a>例

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
partition by (CustomerName:string=CustomerName, Date:datetime=startofday(Timestamp))   
pathformat = ("CustomerName=" CustomerName "/" datetime_pattern("yyyy/MM/dd", Date))   
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

コマンドが (キーワードを使用して) 非同期的に実行された場合、[ `async` [操作の詳細を表示](../operations.md#show-operation-details) ] コマンドを使用して出力を取得できます。
