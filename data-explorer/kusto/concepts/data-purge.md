---
title: データの消去-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのデータの消去について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: reference
ms.date: 05/12/2020
ms.openlocfilehash: ad659f9208bd057719a1adc31f8370c0cb11ffd3
ms.sourcegitcommit: fb54d71660391a63b0c107a9703adea09bfc7cb9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2020
ms.locfileid: "86946140"
---
# <a name="data-purge"></a>データの消去

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Azure データエクスプローラーは、データプラットフォームとして、Kusto および関連コマンドを使用して個々のレコードを削除する機能をサポートしてい `.purge` ます。 また、[テーブル全体を消去](#purging-an-entire-table)することもできます。  

> [!WARNING]
> コマンドによるデータの削除 `.purge` は、個人データの保護に使用するように設計されており、他のシナリオでは使用しないでください。 頻繁に削除要求をサポートしたり、大量のデータを削除したりするようには設計されていないため、サービスのパフォーマンスに大きな影響を与える可能性があります。

## <a name="purge-guidelines"></a>消去のガイドライン

Azure データエクスプローラーに個人データを格納する前に、データスキーマを慎重に設計し、関連するポリシーを調査してください。

1. ベストケースのシナリオでは、このデータの保有期間は十分に短く、データは自動的に削除されます。
1. 保有期間の使用が不可能な場合は、プライバシー規則の対象となるすべてのデータを、少数の Azure データエクスプローラーテーブルで分離します。 最適です。1つのテーブルだけを使用し、他のすべてのテーブルからリンクします。 この分離により、機密データを保持している少数のテーブルに対してデータ[消去処理](#purge-process)を実行し、他のすべてのテーブルを回避できます。
1. 呼び出し元は、コマンドの実行を 1 `.purge` 日あたりのテーブルごとに1-2 コマンドにバッチ処理するように設定する必要があります。 一意のユーザー id 述語を使用して複数のコマンドを実行しないでください。 代わりに、削除が必要なすべてのユーザー id が含まれる述語を含む単一のコマンドを送信します。

## <a name="purge-process"></a>プロセスの消去

Azure データエクスプローラーからデータを選択的に消去するプロセスは、次の手順で行われます。

1. フェーズ 1: Azure データエクスプローラーテーブル名とレコードごとの述語を使用して、削除するレコードを示す入力を指定します。 Kusto は、データの消去に参加するデータエクステントを識別するために、テーブルをスキャンします。 特定されたエクステントとは、述語が true を返す1つ以上のレコードを持つものです。
1. フェーズ 2: (論理的な削除) テーブル内の各データエクステント (手順 (1) で識別されます) を reingested バージョンに置き換えます。 Reingested のバージョンには、述語が true を返すレコードが含まれていてはなりません。 新しいデータがテーブルに取り込まれたされていない場合、このフェーズの最後までは、述語が true を返すデータは返されなくなります。 論理削除の消去フェーズの期間は、次のパラメーターによって異なります。 
     * 削除する必要があるレコードの数 
     * クラスター内のデータエクステント全体に分散を記録する 
     * クラスター内のノードの数  
     * パージ操作用の予備容量
     * 他にも、フェーズ2の期間は数秒から数時間に変化する要因がいくつかあります。
1. フェーズ 3: (ハード削除) "有害な" データを含む可能性があるすべてのストレージアーティファクトを処理し、それらをストレージから削除します。 このフェーズは、前のフェーズが完了してから少なくとも5日後に実行されますが、最初のコマンドから30日以内に終了します。 これらのタイムラインは、データのプライバシー要件に従うように設定されています。

コマンドを発行すると `.purge` 、このプロセスがトリガーされます。完了するまでに数日かかります。 述語が適用されるレコードの密度が十分に大きい場合、このプロセスでは、テーブル内のすべてのデータが実質的に再取り込みされます。 この再取り込みは、パフォーマンスと COGS に大きな影響を与えます。

## <a name="purge-limitations-and-considerations"></a>パージの制限事項と考慮事項

* 消去プロセスは最終処理であり、元に戻すことはできません。 このプロセスを元に戻したり、削除されたデータを回復したりすることはできません。 [テーブル削除の取り消し](../management/undo-drop-table-command.md)などのコマンドは、消去されたデータを回復できません。 以前のバージョンへのデータのロールバックは、最新の purge コマンドの前に進むことはできません。

* 消去を実行する前に、クエリを実行し、結果が予期される結果と一致することを確認して、述語を確認します。 また、2段階のプロセスを使用して、削除される予定のレコード数を返すこともできます。 

* `.purge`データ管理エンドポイントに対してコマンドが実行され `https://ingest-[YourClusterName].[region].kusto.windows.net` ます。
   このコマンドを実行するには、関連するデータベースに対する[データベース管理者](../management/access-control/role-based-authorization.md)権限が必要です。 
* 消去プロセスのパフォーマンスに影響があり、[消去のガイドライン](#purge-guidelines)に従っていることを保証するために、呼び出し元はデータスキーマを変更することを想定しています。これにより、最小限のテーブルに関連するデータが含まれるようになり、テーブルごとのバッチコマンドを実行して、消去プロセスの大きな COGS の影響を軽減できます。
* Purge `predicate` コマンドのパラメーターは、消去するレコードを指定するために使用されます[。](#purge-table-tablename-records-command)
`Predicate` のサイズは 63 KB に制限されています。 を構築する場合 `predicate` :
    * たとえば、 [' in ' 演算子](../query/inoperator.md)を使用し `where [ColumnName] in ('Id1', 'Id2', .. , 'Id1000')` ます。 
    * [' In ' 演算子](../query/inoperator.md)の制限 (リストには最大値を含めることができます) に注意して `1,000,000` ください。
    * クエリのサイズが大きい場合は、 [ `externaldata` 演算子](../query/externaldata-operator.md)を使用します。たとえば、のように `where UserId in (externaldata(UserId:string) ["https://...blob.core.windows.net/path/to/file?..."])` します。 ファイルには、削除する Id の一覧が格納されます。
    * すべての blob を展開した後のクエリサイズの合計 `externaldata` (すべての blob の合計サイズ) は、64 MB を超えることはできません。 

## <a name="purge-performance"></a>パフォーマンスの消去

特定の時点で、クラスターで実行できる消去要求は1つだけです。 他のすべての要求は、状態のキューに登録され `Scheduled` ます。 消去要求キューのサイズを監視し、データに適用される要件に合わせて十分な制限を維持します。

消去の実行時間を短縮するには:
* 消去の[ガイドライン](#purge-guidelines)に従って、削除されたデータの量を減らします。
* コールドデータの消去にかかる時間が長くなるため、[キャッシュポリシー](../management/cachepolicy.md)を調整します。
* クラスターをスケールアウトする

* 「[エクステントを消去](../management/capacitypolicy.md#extents-purge-rebuild-capacity)する」で詳しく説明するように、クラスターの消去容量は慎重に検討してください。 このパラメーターを変更するには、[サポートチケット](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)を開く必要があります

## <a name="trigger-the-purge-process"></a>消去プロセスをトリガーします

> [!Note]
> データ管理エンドポイントで[purge Table *TableName* records](#purge-table-tablename-records-command)コマンドを実行すると、消去実行が呼び出され https://ingest- ます [YourClusterName]. [Region]。 kusto. windows. net.

### <a name="purge-table-tablename-records-command"></a>テーブル TableName レコードの消去コマンド

Purge コマンドは、さまざまな使用シナリオに対して2つの方法で呼び出すことができます。

* プログラムによる呼び出し:アプリケーションによって呼び出されることを目的とした単一の手順。 このコマンドを直接呼び出すと、実行シーケンスの消去がトリガーします。

    **構文**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     .purge table [TableName] records in database [DatabaseName] with (noregrets='true') <| [Predicate]
     ```

    > [!NOTE]
    > このコマンドは、 [Kusto クライアントライブラリ](../api/netfx/about-kusto-data.md)NuGet パッケージの一部として利用可能な CslCommandGenerator API を使用して生成します。

* ユーザーによる呼び出し:独立した手順として明確な確認を必要とする 2 段階のプロセス。 コマンドの最初の呼び出しでは、検証トークンが返されます。これは実際の消去を実行するために提供される必要があります。 このシーケンスにより、誤ったデータが誤って削除されるリスクが軽減されます。 大きなテーブルでこのオプションを使用すると、完了に時間がかかり、大量のコールド キャッシュ データが使用される可能性があります。
    <!-- If query times-out on DM endpoint (default timeout is 10 minutes), it is recommended to use the [engine `whatif` command](#purge-whatif-command) directly againt the engine endpoint while increasing the [server timeout limit](../concepts/querylimits.md#limit-on-request-execution-time-timeout). Only after you have verified the expected results using the engine whatif command, issue the purge command via the DM endpoint using the 'noregrets' option. -->

     **構文**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (no records will be purged until step #2 is executed)
     .purge table [TableName] records in database [DatabaseName] <| [Predicate]

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] records in database [DatabaseName] with (verificationtoken='<verification token from step #1>') <| [Predicate]
     ```
    
    | パラメーター  | 説明  |
    |---------|---------|
    | `DatabaseName`   |   データベースの名前      |
    | `TableName`     |     テーブルの名前    |
    | `Predicate`    |    消去するレコードを識別します。 「Purge 述語の制限事項」を参照してください。 | 
    | `noregrets`    |     設定すると、シングルステップのアクティブ化がトリガーします。    |
    | `verificationtoken`     |  2段階のアクティブ化のシナリオ ( `noregrets` 設定されていません) では、このトークンを使用して2番目のステップを実行し、アクションをコミットできます。 が `verificationtoken` 指定されていない場合は、コマンドの最初のステップがトリガーされます。 消去に関する情報は、コマンドに渡す必要があるトークンと共に返されます。このトークンは、#2 手順を実行します。   |

    **削除の述語の制限事項**

    * 述語は、単純な選択である必要があります (たとえば、[columnname *] = = ' x '*、  /  *(' x '、' Y '、' Z ')、および [othercolumn] = = ' a '*)。
    * 複数のフィルターは、個別の句ではなく ' and ' と組み合わせて使用する必要があり `where` ます (たとえば、 `where [ColumnName] == 'X' and  OtherColumn] == 'Y'` と not `where [ColumnName] == 'X' | where [OtherColumn] == 'Y'` )。
    * 述語は、削除されるテーブル以外のテーブル (*TableName*) を参照することはできません。 述語には、選択ステートメント () のみを含めることができ `where` ます。 テーブルから特定の列を射影することはできません ('* | ' を実行するときは出力スキーマ)。 `table`述語*' はテーブルスキーマと一致しなければなりません)。
    * システム関数 (、、など `ingestion_time()` `extent_id()` ) はサポートされていません。

#### <a name="example-two-step-purge"></a>例: 2 段階の消去

2段階のアクティブ化シナリオで purge を開始するには、コマンドの手順 #1 を実行します。

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase <| where CustomerId in ('X', 'Y')
    ```

    **Output**

    | NumRecordsToPurge 関するお願い | EstimatedPurgeExecutionTime| VerificationToken
    |--|--|--
    | 1596 | 00:00:02 | e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

    Then, validate the NumRecordsToPurge before running step #2. 

2段階のアクティブ化シナリオで消去を完了するには、手順 #1 から返された検証トークンを使用して、手順 #2 を実行します。

    ```kusto
    .purge table MyTable records in database MyDatabase
    with (verificationtoken='e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b')
    <| where CustomerId in ('X', 'Y')
    ```

    **Output**

    | `OperationId` | `DatabaseName` | `TableName`|`ScheduledTime` | `Duration` | `LastUpdatedOn` |`EngineOperationId` | `State` | `StateDetails` |`EngineStartTime` | `EngineDuration` | `Retries` |`ClientRequestId` | `Principal`|
    |--|--|--|--|--|--|--|--|--|--|--|--|--|--|
    | c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41: 05.4391686 |00:00: 00.1406211 |2019-01-20 11:41: 05.4391686 | |スケジュール済み | | | |0 |KE.RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD アプリ id =...|

#### <a name="example-single-step-purge"></a>例: 単一ステップの消去

シングルステップライセンス認証のシナリオで消去をトリガーするには、次のコマンドを実行します。

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase with (noregrets='true') <| where CustomerId in ('X', 'Y')
    ```

**出力**

| `OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`|
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
| c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41: 05.4391686 |00:00: 00.1406211 |2019-01-20 11:41: 05.4391686 | |スケジュール済み | | | |0 |KE.RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD アプリ id =...|

### <a name="cancel-purge-operation-command"></a>消去操作の取り消しコマンド

必要に応じて、保留中の消去要求を取り消すことができます。

> [!NOTE]
> この操作は、エラー回復のシナリオを対象としています。 成功するとは限りません。通常の動作フローの一部になることはありません。 キュー内の要求にのみ適用できます (実行のためにエンジンノードにディスパッチされることはありません)。 コマンドはデータ管理エンドポイントで実行されます。

**構文**

```kusto
 .cancel purge <OperationId>
 ```

**例**

```kusto
 .cancel purge aa894210-1c60-4657-9d21-adb2887993e1
 ```

**出力**

このコマンドの出力は、"削除操作の*表示" コマンド*の出力と同じであり、取り消された消去操作の更新状態が示されます。 試行が成功した場合、操作の状態はに更新され `Abandoned` ます。 それ以外の場合、操作の状態は変更されません。 

|`OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41: 05.4391686 |00:00: 00.1406211 |2019-01-20 11:41: 05.4391686 | |Abandoned | | | |0 |KE.RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD アプリ id =...

## <a name="track-purge-operation-status"></a>削除操作の状態の追跡 

> [!Note]
> 消去操作は、データ管理エンドポイントに対して実行される [削除の[表示](#show-purges-command)] コマンドを使用して追跡でき https://ingest- ます [YourClusterName]. [region]。 kusto. windows. net.

Status = ' Completed ' は、消去操作の最初のフェーズが正常に完了したことを示します。つまり、レコードは論理的に削除され、クエリに使用できなくなります。 お客様は、2番目のフェーズ (ハード削除) の完了を追跡して確認することは期待されてい**ませ**ん。 このフェーズは、Azure データエクスプローラーによって内部的に監視されます。

### <a name="show-purges-command"></a>削除コマンドの表示

`Show purges`コマンドは、要求された期間内に操作 ID を指定することによって、消去操作の状態を示します。 

```kusto
.show purges <OperationId>
.show purges [in database <DatabaseName>]
.show purges from '<StartDate>' [in database <DatabaseName>]
.show purges from '<StartDate>' to '<EndDate>' [in database <DatabaseName>]
```

| Properties  |説明  |必須/省略可能|
|---------|------------|-------|
|`OperationId `   |      1フェーズまたは2番目のフェーズを実行した後に出力されるデータ管理操作 Id。   |Mandatory
|`StartDate`    |   フィルター処理の制限時間を短くします。 省略した場合、既定値は現在の時刻よりも24時間前になります。      |省略可能
|`EndDate`    |  フィルター処理の上限時間。 省略した場合、既定値は現在の時刻になります。       |省略可能
|`DatabaseName`    |     結果をフィルター処理するデータベース名。    |省略可能

> [!NOTE]
> 状態は、クライアントが[データベース管理者](../management/access-control/role-based-authorization.md)権限を持っているデータベースに対してのみ提供されます。

**使用例**

```kusto
.show purges
.show purges c9651d74-3b80-4183-90bb-bbe9e42eadc4
.show purges from '2018-01-30 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00' in database MyDatabase
```

**出力** 

|`OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41: 05.4391686 |00:00: 33.6782130 |2019-01-20 11:42: 34.6169153 |a0825d4d-6b0f-47f3-a499-54ac5681ab78 |完了 |消去が正常に完了しました (削除を保留中の記憶域アイテム) |2019-01-20 11:41: 34.6486506 |00:00: 04.4687310 |0 |KE.RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD アプリ id =...

* `OperationId`-purge の実行時に返される DM 操作 ID。 
* `DatabaseName`* *-データベース名 (大文字と小文字が区別されます)。 
* `TableName`-テーブル名 (大文字と小文字が区別されます)。 
* `ScheduledTime`-DM サービスに対して purge コマンドを実行した時刻。 
* `Duration`-実行 DM キューの待機時間を含む、消去操作の合計時間。 
* `EngineOperationId`-エンジンで実行されている実際の消去の操作 ID。 
* `State`-purge state、次のいずれかの値を指定できます。 
    * `Scheduled`-消去操作の実行がスケジュールされています。 ジョブがスケジュールされたままの場合は、削除操作のバックログが存在する可能性があります。 このバックログを消去するには、「[消去のパフォーマンス](#purge-performance)」を参照してください。 一時的なエラーが発生したときに消去操作が失敗した場合は、DM によって再試行され、スケジュールが再設定されます (したがって、スケジュールから処理済みからスケジュール済みに戻る操作の切り替えが発生する可能性があります)。
    * `InProgress`-エンジンで消去操作が進行中です。 
    * `Completed`-消去が正常に完了しました。
    * `BadInput`-無効な入力の消去に失敗し、再試行されません。 このエラーは、述語の構文エラー、purge コマンドの無効な述語、制限を超えたクエリ (たとえば、演算子内の 1 `externaldata` MB 以上のエンティティ、合計拡張クエリサイズが 64 MB を超えるなど)、および blob に対して404または403エラーが発生したことが原因で発生する可能性があり `externaldata` ます。
    * `Failed`-purge に失敗し、再試行されません。 このエラーは、他の消去操作のバックログ、または再試行の制限を超えるエラーが発生したため、操作がキューで長時間待機している (14 日を超える) 場合に発生する可能性があります。 後者の場合、内部監視アラートが生成され、Azure データエクスプローラーチームによって調査されます。 
* `StateDetails`-状態の説明。
* `EngineStartTime`-コマンドがエンジンに発行された時刻。 この時間と ScheduledTime の間に大きな違いがある場合は、通常、消去操作の大きなバックログがあり、クラスターがそのペースに追いついていません。 
* `EngineDuration`-エンジンで実際に実行された実行時間。 Purge が数回再試行された場合は、すべての実行期間の合計になります。 
* `Retries`-一時的なエラーが原因で、DM サービスによって操作が再試行された回数。
* `ClientRequestId`-DM 消去要求のクライアントアクティビティ ID。 
* `Principal`-purge コマンドの発行者の id。

## <a name="purging-an-entire-table"></a>テーブル全体の削除

テーブルを削除するには、テーブルを削除し、削除済みとしてマークします。これにより、[パージプロセス](#purge-process)で記述されたハード削除処理が実行されます。 削除せずにテーブルを削除しても、そのテーブルのすべてのストレージアーティファクトは削除されません。 これらのアーティファクトは、テーブルに最初に設定されたハードリテンション期間ポリシーに従って削除されます。 コマンドは短時間で `purge table allrecords` 効率的であり、シナリオに該当する場合は、レコードの消去プロセスに適しています。 

> [!Note]
> このコマンドは、データ管理エンドポイント [YourClusterName] で、[テーブルの削除*TableName* allrecords](#purge-table-tablename-allrecords-command)コマンドを実行することによって呼び出され https://ingest- ます。 [region]。 kusto. windows. net.

### <a name="purge-table-tablename-allrecords-command"></a>Purge table *TableName* allrecords コマンド

"[Purge table records ](#purge-table-tablename-records-command)" コマンドと同様に、このコマンドはプログラムによって (シングルステップ)、または手動 (2 段階) モードで呼び出すことができます。

1. プログラムによる起動 (シングルステップ):

     **構文**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
     .purge table [TableName] in database [DatabaseName] allrecords with (noregrets='true')
     ```

1. 人間による呼び出し (2 段階):

     **構文**

     ```kusto
   
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (the table will not be purged until step #2 is executed)

     .purge table [TableName] in database [DatabaseName] allrecords

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] in database [DatabaseName] allrecords with (verificationtoken='<verification token from step #1>')
     ```

    | パラメーター  |説明  |
    |---------|---------|
    | `DatabaseName`   |   データベースの名前です。      |
    | `TableName`    |     テーブルの名前。    |
    | `noregrets`    |     設定すると、シングルステップのアクティブ化がトリガーします。    |
    | `verificationtoken`     |  2段階のアクティブ化のシナリオ ( `noregrets` 設定されていません) では、このトークンを使用して2番目のステップを実行し、アクションをコミットできます。 が `verificationtoken` 指定されていない場合は、コマンドの最初のステップがトリガーされます。 このステップでは、コマンドに戻るためのトークンが返され、#2 ステップが実行されます。|

#### <a name="example-two-step-purge"></a>例: 2 段階の消去

1. 2段階のアクティブ化シナリオで purge を開始するには、コマンドの手順 #1 を実行します。 

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
    .purge table MyTable in database MyDatabase allrecords
    ```

    **出力**

    | `VerificationToken`|
    |--|
    | e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b|

1.  2段階のアクティブ化シナリオで消去を完了するには、手順 #1 から返された検証トークンを使用して、手順 #2 を実行します。

    ```kusto
    .purge table MyTable in database MyDatabase allrecords 
    with (verificationtoken='eyJTZXJ2aWNlTmFtZSI6IkVuZ2luZS1pdHNhZ3VpIiwiRGF0YWJhc2VOYW1lIjoiQXp1cmVTdG9yYWdlTG9ncyIsIlRhYmxlTmFtZSI6IkF6dXJlU3RvcmFnZUxvZ3MiLCJQcmVkaWNhdGUiOiIgd2hlcmUgU2VydmVyTGF0ZW5jeSA9PSAyNSJ9')
    ```
    
    出力は '. テーブルの表示 ' コマンドの出力と同じです (削除されたテーブルが返されません)。

    **出力**

    |  TableName|DatabaseName|Folder|DocString
    |---|---|---|---
    |  OtherTable|MyDatabase|---|---


#### <a name="example-single-step-purge"></a>例: 単一ステップの消去

シングルステップライセンス認証のシナリオで消去をトリガーするには、次のコマンドを実行します。

```kusto
// Connect to the Data Management service
#connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 

.purge table MyTable in database MyDatabase allrecords with (noregrets='true')
```

出力は '. テーブルの表示 ' コマンドの出力と同じです (削除されたテーブルが返されません)。

**出力**

|TableName|DatabaseName|Folder|DocString
|---|---|---|---
|OtherTable|MyDatabase|---|---

