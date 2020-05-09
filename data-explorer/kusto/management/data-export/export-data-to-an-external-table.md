---
title: 外部テーブルへのデータのエクスポート-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの外部テーブルにデータをエクスポートする方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 5a7a1b37c8c50bdff3760ad9222065191a9eb884
ms.sourcegitcommit: 3393ad86dac455fd182296ffb410b2bd570dbfce
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2020
ms.locfileid: "82991890"
---
# <a name="export-data-to-an-external-table"></a>外部テーブルへのデータのエクスポート

[外部テーブル](../externaltables.md)を定義し、データをエクスポートすることによって、データをエクスポートできます。
テーブルのプロパティは、[外部テーブルを作成](../externaltables.md#create-or-alter-external-table)するときに指定します。そのため、export コマンドにテーブルのプロパティを埋め込む必要はありません。 エクスポートコマンドは、外部テーブルを名前で参照します。
データのエクスポートには、[データベース管理者のアクセス許可](../access-control/role-based-authorization.md)が必要です。

**構文 :**

`.export`[`async`] `to` `table` *Externaltablename* <br>
[`with` `(` *PropertyName* PropertyName `=` *PropertyValue*PropertyValue`,`...`)`] <|*クエリ*

**出力:**

|出力パラメーター |Type |説明
|---|---|---
|ExternalTableName  |String |外部テーブルの名前。
|パス|String|出力パス。
|NumRecords|String| Path にエクスポートされたレコードの数。

**注:**
* エクスポートクエリの出力スキーマは、パーティションで定義されているすべての列を含め、外部テーブルのスキーマと一致する必要があります。 たとえば、テーブルが*DateTime*でパーティション分割されている場合、クエリ出力スキーマには、外部テーブルパーティション分割定義で*定義されているタイム*スタンプ列と一致する Timestamp 列が含まれている必要があります。

* Export コマンドを使用して外部テーブルのプロパティを上書きすることはできません。
 たとえば、Parquet 形式のデータを、データ形式が CSV である外部テーブルにエクスポートすることはできません。

* Export コマンドの一部として、次のプロパティがサポートされています。 詳細については、「[ストレージへのエクスポート](export-data-to-storage.md)」を参照してください。 
   * `sizeLimit`, `parquetRowGroupSize`, `distributed`.

* 外部テーブルがパーティション分割されている場合、エクスポートされたアイテムは、[例](#partitioned-external-table-example)に示されているパーティション定義に従って、それぞれのディレクトリに書き込まれます。 

* パーティションごとに書き込まれるファイルの数は、次のことに依存します。
   * 外部テーブルに datetime パーティションだけが含まれている場合、またはパーティションがまったく存在しない場合は、書き込まれたファイルの数 (各パーティションの場合は、存在する場合) がクラスター `sizeLimit`内のノードの数と同じである必要があります (以上の場合)。 これは、エクスポート操作が分散され、クラスター内のすべてのノードが同時にエクスポートされるためです。 
   ディストリビューションを無効にして、1つのノードだけが書き込みを`distributed`実行するようにするには、を false に設定します。 これにより、作成されるファイルが少なくなりますが、エクスポートのパフォーマンスが低下します。

   * 外部テーブルに文字列型の列によってパーティションが含まれている場合、エクスポートされるファイルの数は、パーティションごとに`sizeLimit` 1 つのファイルである必要があります (またはに到達した場合)。 すべてのノードは引き続きエクスポートに参加します (操作は分散されます) が、各パーティションは特定のノードに割り当てられます。 この`distributed`場合、を false に設定すると、1つのノードのみがエクスポートを実行しますが、動作は同じままです (パーティションごとに1つのファイルが書き込まれます)。

## <a name="examples"></a>使用例

### <a name="non-partitioned-external-table-example"></a>パーティション分割されていない外部テーブルの例

ExternalBlob は、パーティション分割されていない外部テーブルです。 
```kusto
.export to table ExternalBlob <| T
```

|ExternalTableName|パス|NumRecords|
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

|ExternalTableName|パス|NumRecords|
|---|---|---|
|ExternalBlob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer1/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_1_58017c550b384c0db0fea61a8661333e.csv|10|
|ExternalBlob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer2/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_2_b785beec2c004d93b7cd531208424dc9.csv|10|

コマンドが ( `async`キーワードを使用して) 非同期的に実行された場合、[[操作の詳細を表示](../operations.md#show-operation-details)] コマンドを使用して出力を取得できます。
