---
title: 継続的なデータのエクスポート-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの継続的なデータエクスポートについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/27/2020
ms.openlocfilehash: 69d8f4e8e0ffa388893c55e447dd3e03ed058380
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108407"
---
# <a name="continuous-data-export"></a>継続的データ エクスポート

Kusto から[外部テーブル](../externaltables.md)にデータを継続的にエクスポートします。 外部テーブルでは、エクスポート先 (Azure Blob Storage など) とエクスポートされるデータのスキーマを定義します。 エクスポートされたデータは、定期的に実行されるクエリによって定義されます。 結果は、外部テーブルに格納されます。 このプロセスでは、すべてのレコードが "正確に1回" (ディメンションテーブルを除く) にエクスポートされ、すべてのレコードがすべての実行で評価されます。 

連続データのエクスポートでは、[外部テーブルを作成](../externaltables.md#create-or-alter-external-table)し、外部テーブルを指す[連続エクスポート定義を作成](#create-or-alter-continuous-export)する必要があります。 

> [!NOTE] 
> * Kusto では、連続エクスポートの作成前に (連続エクスポートの一部として) 取り込まれた履歴レコードのエクスポートをサポートしていません。 履歴レコードは、(連続していない)[エクスポートコマンド](export-data-to-an-external-table.md)を使用して個別にエクスポートできます。 詳細については、「[履歴データのエクスポート](#exporting-historical-data)」を参照してください。 
> * 連続エクスポートは、ストリーミングインジェストを使用するデータ取り込まれたでは機能しません。 
> * 現時点では、[行レベルセキュリティポリシー](../../management/rowlevelsecuritypolicy.md)が有効になっているテーブルで連続エクスポートを構成することはできません。
> * 連続エクスポートは、の`impersonate` [接続文字列](../../api/connection-strings/storage.md)に含まれる外部テーブルではサポートされていません。
 
## <a name="notes"></a>Notes

* "厳密に1回" のエクスポートの保証は、[エクスポートされた[アーティファクトの表示] コマンド](#show-continuous-export-exported-artifacts)で報告されたファイルに対して*のみ*発生します。 
連続エクスポートでは、各レコードが外部テーブルに1回だけ書き込まれることは保証*されません*。 エクスポートが開始され、一部のアイテムが既に外部テーブルに書き込まれた後でエラーが発生した場合、外部テーブルには重複した (または、完了前に書き込み操作が中止された場合には破損した) ファイルが含まれている_可能性があり_ます。 このような場合、アイテムは外部テーブルから削除されませんが、[エクスポートされた[アーティファクトの表示] コマンド](#show-continuous-export-exported-artifacts)では報告され*ませ*ん。 を使用してエクスポートさ`show exported artifacts command`れたファイルを使用すると、重複は保証されません (もちろん破損ではありません)。
* 連続エクスポートでは、"厳密に1回" のエクスポートを保証するために、[データベースカーソル](../databasecursor.md)を使用します。 
したがって、エクスポートで "厳密に1回" 処理する必要があるクエリで参照されるすべてのテーブルで、 [IngestionTime ポリシー](../ingestiontime-policy.md)を有効にする必要があります。 このポリシーは、新しく作成されたすべてのテーブルで既定で有効になっています。
* エクスポートクエリの出力スキーマは、エクスポート先の外部テーブルのスキーマと一致して*いる必要があり*ます。 
* 連続エクスポートでは、複数データベース/クラスター呼び出しをサポートしていません。
* 連続エクスポートは、構成されている期間に従って実行されます。 この間隔の推奨値は、許容される待ち時間に応じて、少なくとも数分です。 連続エクスポートは、Kusto から継続的にデータをストリーミングするように設計されてい*ません*。 すべてのノードが同時にエクスポートされる分散モードで実行されます。 各実行によってクエリされるデータの範囲が小さい場合、連続エクスポートの出力は多数の小さな成果物になります (数値はクラスター内のノードの数によって異なります)。 
* 同時に実行できるエクスポート操作の数は、クラスターのデータエクスポート容量によって制限されます (「[調整](../../management/capacitypolicy.md#throttling)」を参照してください)。 クラスターにすべての連続エクスポートを処理するのに十分な容量がない場合は、遅れているが開始されます。 
 
* 既定では、エクスポートクエリで参照されるすべてのテーブルは、[ファクトテーブル](../../concepts/fact-and-dimension-tables.md)と見なされます。 
そのため、データベースカーソルに*スコープ*が設定されています。 エクスポートクエリに含まれるレコードは、前回のエクスポート実行以降に結合されたレコードだけです。 
エクスポートクエリには、ディメンションテーブルの*すべて*のレコードが*すべて*のエクスポートクエリに含まれる[ディメンションテーブル](../../concepts/fact-and-dimension-tables.md)が含まれている場合があります。 
    * 連続エクスポートでファクトテーブルとディメンションテーブル間の結合を使用する場合は、ファクトテーブルのレコードは1回だけ処理されることに注意してください。ディメンションテーブル内のレコードが一部のキーに対して欠落している間にエクスポートが実行されると、それぞれのキーのレコードが失われるか、エクスポートされたファイルのディメンション列に null 値が含まれることに注意してください 連続エクスポート定義の forcedLatency プロパティは、ファクトテーブルとディメンションテーブルが同時に (一致するレコードに対して) 取り込まれたされる場合に便利です。
    * ディメンションテーブルのみの連続エクスポートはサポートされていません。 エクスポートクエリには、少なくとも1つのファクトテーブルが含まれている必要があります。
    * この構文では、スコープ (ファクト) であり、スコープが設定されていないテーブル (ディメンション) を明示的に宣言します。 詳細に`over`ついては、 [create コマンド](#create-or-alter-continuous-export)のパラメーターを参照してください。

* 連続エクスポートの各繰り返しでエクスポートされるファイルの数は、外部テーブルのパーティション分割方法によって異なります。 詳細については、「 [export to external table command](export-data-to-an-external-table.md) 」の「notes」セクションを参照してください。 連続エクスポートの反復は、常に*新しい*ファイルに書き込み、既存のファイルには追加されないことに注意してください。 その結果、エクスポートされたファイルの数も、連続エクスポートが実行される頻度 (`intervalBetweenRuns`パラメーター) によって決まります。

連続エクスポートのすべてのコマンドには、[データベース管理者のアクセス許可](../access-control/role-based-authorization.md)が必要です。

## <a name="create-or-alter-continuous-export"></a>連続エクスポートの作成または変更

**構文 :**

`.create-or-alter``continuous-export` *ContinuousExportName* <br>
[ `over` `(` *T1*, *T2* `)`] <br>
`to``table` *Externaltablename* <br> [ `with` `(` *PropertyName* PropertyName `=` *PropertyValue*PropertyValue`,`...`)`]<br>
\<| *照会*

**プロパティ**:

| プロパティ             | Type     | 説明   |
|----------------------|----------|---------------------------------------|
| ContinuousExportName | String   | 連続エクスポートの名前。 名前はデータベース内で一意である必要があり、連続エクスポートを定期的に実行するために使用されます。      |
| ExternalTableName    | String   | エクスポート先の[外部テーブル](../externaltables.md)の名前。  |
| クエリ                | String   | エクスポートするクエリ。  |
| over (T1, T2)        | String   | クエリ内のファクトテーブルのコンマ区切りのリスト (省略可能)。 指定しない場合、クエリで参照されるすべてのテーブルがファクトテーブルと見なされます。 指定した場合、この一覧に含まれて*いない*テーブルはディメンションテーブルとして扱われ、スコープは設定されません (すべてのレコードがすべてのエクスポートに参加します)。 詳細については、「[メモ」](#notes)を参照してください。 |
| intervalBetweenRuns  | Timespan | 連続エクスポート実行の間隔。 1分以上にする必要があります。   |
| forcedLatency        | Timespan | この期間の前にのみ取り込まれたされたレコードに対してクエリを制限するオプションの期間 (現在の時刻に対して)。 これは、たとえば、クエリがいくつかの集計/結合を実行し、エクスポートを実行する前に関連するすべてのレコードが既に取り込まれたされていることを確認する必要がある場合に便利です。

上記に加えて、[[外部テーブルへのエクスポート] コマンド](export-data-to-an-external-table.md)でサポートされているすべてのプロパティが、連続エクスポートの create コマンドでサポートされています。 

**例:**

```
.create-or-alter continuous-export MyExport
over (T)
to table ExternalBlob
with
(intervalBetweenRuns=1h, 
 forcedLatency=10m, 
 sizeLimit=104857600)
<| T
```

| 名前     | ExternalTableName | クエリ | ForcedLatency | IntervalBetweenRuns | CursorScopedTables         | ExportProperties                   |
|----------|-------------------|-------|---------------|---------------------|----------------------------|------------------------------------|
| MyExport | ExternalBlob      | S     | 00:10:00      | 01:00:00            | [<br>  "[' DB ']。[S '] "<br>] | {<br>  "SizeLimit": 104857600<br>} |

## <a name="show-continuous-export"></a>連続エクスポートの表示

**構文 :**

`.show``continuous-export` *ContinuousExportName*

*ContinuousExportName*の連続エクスポートプロパティを返します。 

**属性**

| プロパティ             | Type   | 説明                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連続エクスポートの名前。 |


`.show` `continuous-exports`

データベース内の連続するすべてのエクスポートを返します。 

**出力:**

| 出力パラメーター    | Type     | 説明                                                             |
|---------------------|----------|-------------------------------------------------------------------------|
| CursorScopedTables  | String   | 明示的にスコープされた (ファクト) テーブルの一覧 (JSON シリアル化)               |
| ExportProperties    | String   | プロパティのエクスポート (JSON シリアル化)                                     |
| ExportedTo          | DateTime | 正常にエクスポートされた最後の日時 (インジェスト時刻)       |
| ExternalTableName   | String   | 外部テーブルの名前                                              |
| ForcedLatency       | TimeSpan | 強制待機時間 (指定されていない場合は null)                                   |
| IntervalBetweenRuns | TimeSpan | 実行間隔                                                   |
| IsDisabled          | Boolean  | 連続エクスポートが無効になっている場合は True                               |
| IsRunning           | Boolean  | 連続エクスポートが現在実行されている場合は True                      |
| LastRunResult       | String   | 最後の連続エクスポート実行の結果 (`Completed`または) `Failed` |
| LastRunTime         | DateTime | 連続エクスポートが最後に実行された時刻 (開始時刻)           |
| 名前                | String   | 連続エクスポートの名前                                           |
| クエリ               | String   | クエリをエクスポートする                                                            |
| StartCursor         | String   | この連続エクスポートの最初の実行の開始点         |

## <a name="show-continuous-export-exported-artifacts"></a>エクスポートされた連続エクスポートの成果物の表示

**構文 :**

`.show``continuous-export` *ContinuousExportName*`exported-artifacts`

すべての実行で連続エクスポートによってエクスポートされたすべての成果物を返します。 対象のレコードのみを表示するには、コマンドの Timestamp 列によって結果をフィルター処理することをお勧めします。 エクスポートされたアーティファクトの履歴は14日間保持されます。 

**属性**

| プロパティ             | Type   | 説明                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連続エクスポートの名前。 |

**出力:**

| 出力パラメーター  | Type     | 説明                            |
|-------------------|----------|----------------------------------------|
| Timestamp         | Datetime | 連続エクスポート実行のタイムスタンプ |
| ExternalTableName | String   | 外部テーブルの名前             |
| Path              | String   | [出力パス]                            |
| NumRecords        | long     | パスにエクスポートされたレコードの数     |

**例:** 

```kusto
.show continuous-export MyExport exported-artifacts | where Timestamp > ago(1h)
```

| Timestamp                   | ExternalTableName | Path             | NumRecords | SizeInBytes |
|-----------------------------|-------------------|------------------|------------|-------------|
| 2018-12-20 07:31: 30.2634216 | ExternalBlob      | `http://storageaccount.blob.core.windows.net/container1/1_6ca073fd4c8740ec9a2f574eaa98f579.csv` | 10                          | 1024              |

## <a name="show-continuous-export-failures"></a>連続エクスポートエラーの表示

**構文 :**

`.show``continuous-export` *ContinuousExportName*`failures`

連続エクスポートの一部として記録されたすべてのエラーを返します。 目的の時間範囲だけを表示するには、コマンドの Timestamp 列によって結果をフィルター処理します。 

**属性**

| プロパティ             | Type   | 説明                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連続エクスポートの名前  |

**出力:**

| 出力パラメーター | Type      | 説明                                         |
|------------------|-----------|-----------------------------------------------------|
| Timestamp        | Datetime  | エラーのタイムスタンプ。                           |
| OperationId      | String    | 失敗の操作 ID。                    |
| 名前             | String    | 連続エクスポート名。                             |
| LastSuccessRun   | Timestamp | 連続エクスポートが最後に正常に実行された。   |
| FailureKind      | String    | 失敗/PartialFailure。 PartialFailure は、エラーが発生する前に一部のアーティファクトが正常にエクスポートされたことを示します。 |
| 詳細情報          | String    | エラーの詳細。                              |

**例:** 

```
.show continuous-export MyExport failures 
```

| Timestamp                   | OperationId                          | 名前     | LastSuccessRun              | FailureKind | 詳細情報    |
|-----------------------------|--------------------------------------|----------|-----------------------------|-------------|------------|
| 2019-01-01 11:07: 41.1887304 | ec641435-2505-4532-ba19-d6ab88c96a9d | MyExport | 2019-01-01 11:06: 35.6308140 | 障害     | 詳細... |

## <a name="drop-continuous-export"></a>連続エクスポートの削除

**構文 :**

`.drop``continuous-export` *ContinuousExportName*

**属性**

| プロパティ             | Type   | 説明                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連続エクスポートの名前 |

**出力:**

データベース内の残りの連続エクスポート (削除後)。 [[連続エクスポートの表示] コマンド](#show-continuous-export)のようにスキーマを出力します。

## <a name="disable-or-enable-continuous-export"></a>連続エクスポートを無効または有効にする

**構文 :**

`.enable``continuous-export` *ContinuousExportName* 

`.disable``continuous-export` *ContinuousExportName* 

連続エクスポートジョブを無効または有効にすることができます。 無効な連続エクスポートは実行されませんが、その現在の状態は永続化され、連続エクスポートが有効になっている場合は再開できます。 長期間無効になっている連続エクスポートを有効にすると、無効になっている場合、エクスポートは最後に停止した場所から続行されます。 これにより、すべてのプロセスを処理するのに十分なクラスター容量がない場合に、実行時間の長いエクスポートが発生し、他のエクスポートの実行がブロックされる可能性があります。 連続エクスポートは、最後の実行時に昇順で実行されます (キャッチアップが完了するまで、最も古いエクスポートが最初に実行されます)。 

**属性**

| プロパティ             | Type   | 説明                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連続エクスポートの名前 |

**出力:**

変更された連続エクスポートの[連続エクスポートの表示コマンド](#show-continuous-export)の結果。 




## <a name="exporting-historical-data"></a>履歴データのエクスポート

連続エクスポートでは、作成した時点からのみデータのエクスポートが開始されます。 この時刻より前のレコード取り込まれたは、(連続していない)[エクスポートコマンド](export-data-to-an-external-table.md)を使用して個別にエクスポートする必要があります。 連続エクスポートによってエクスポートされたデータと重複しないようにするには、[[連続エクスポートの表示] コマンド](#show-continuous-export)によって返された startcursor を使用し、カーソル値が cursor_before_or_at あるレコードのみをエクスポートします。 次の例を見てください。 履歴データが大きすぎて、1回のエクスポートコマンドでエクスポートできない可能性があります。 そのため、クエリをいくつかの小さなバッチに分割します。 

```
.show continuous-export MyExport | project StartCursor
```

| StartCursor        |
|--------------------|
| 636751928823156645 |

次の後に続きます。 

```
.export async to table ExternalBlob
<| T | where cursor_before_or_at("636751928823156645")
```