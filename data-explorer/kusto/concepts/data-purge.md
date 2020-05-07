---
title: データの消去-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのデータの消去について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: e24edd1f47318d1ee12bfead83d2e09f67a6cc7a
ms.sourcegitcommit: 9fe6ee7db15a5cc92150d3eac0ee175f538953d2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2020
ms.locfileid: "82907166"
---
# <a name="data-purge"></a>データの消去

>[!Note]
> この記事では、デバイスまたはサービスから個人データを削除する手順について説明します。また、これは GDPR での義務をサポートするために使用できます。 GDPR に関する一般情報をお探しの場合は、 [Service Trust portal の GDPR セクション](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)を参照してください。



データプラットフォームとして、Azure データエクスプローラー (Kusto) では、 `.purge`および関連コマンドを使用して個々のレコードを削除する機能がサポートされています。 また、[テーブル全体を消去](#purging-an-entire-table)することもできます。  

> [!WARNING]
> コマンドによるデータ`.purge`の削除は、個人データの保護に使用するように設計されており、他のシナリオでは使用しないでください。 頻繁に削除要求をサポートしたり、大量のデータを削除したりするようには設計されていないため、サービスのパフォーマンスに大きな影響を与える可能性があります。

## <a name="purge-guidelines"></a>消去のガイドライン

個人データをデータエクスプローラー Azure に保存するチームは、関連するポリシーのデータスキーマと調査を慎重に設計した後にのみ、この作業を行うことを**強くお勧め**します。

1. ベストケースのシナリオでは、このデータの保有期間は十分に短く、データは自動的に削除されます。
2. 保有期間の使用が不可能な場合、推奨される方法は、プライバシー規則に従うすべてのデータを少数の Kusto テーブル (最適、1つのテーブルのみ) で分離し、他のすべてのテーブルからリンクすることです。 これにより、機密データを保持している少数のテーブルに対してデータ[消去処理](#purge-process)を実行し、他のすべてのテーブルを回避できます。
3. 呼び出し元は、コマンドの`.purge`実行を1日あたりのテーブルごとに1-2 コマンドにバッチ処理するように設定する必要があります。
   複数のコマンドを発行しないでください。それぞれに独自のユーザー id 述語があります。代わりに、削除が必要なすべてのユーザー id が含まれている述語を含む単一のコマンドを送信します。

## <a name="purge-process"></a>プロセスの消去

Azure データエクスプローラーからデータを選択的に消去するプロセスは、次の手順で行われます。

1. **フェーズ 1:** Kusto テーブル名とレコードごとの述語を使用して、どのレコードを削除するかを指定すると、Kusto は、データの消去に参加するデータシャードを特定するようにテーブルをスキャンします (述語が true を返したレコードが1つ以上あることを確認します)。
2. **フェーズ 2: (論理的な削除)**(手順 (1) で識別された) テーブル内の各データシャードを、述語が true を返すレコードがない再取り込まれたバージョンに置き換えます。
   テーブルに新しいデータが取り込まれたされていない限り、このフェーズを終了すると、述語が true を返すデータが返されなくなります。 
   ソフト削除の消去フェーズの期間は、消去する必要があるレコードの数、クラスター内のデータシャード間のディストリビューション、クラスター内のノードの数、削除操作に必要な予備容量、その他いくつかの要因によって異なります。 フェーズ2の期間は数秒から数時間の間で異なる場合があります。
3. **フェーズ 3: (ハード削除)**"有害な" データを含む可能性があるすべてのストレージアーティファクトを処理し、それらをストレージから削除します。 このフェーズは、データのプライバシー要件に準拠するために、前のフェーズの完了から*少なくとも5日後*に実行されますが、最初のコマンドの後30日以内に実行されます。

コマンドを`.purge`発行すると、このプロセスがトリガーされます。完了するまでに数日かかります。 述語が適用されるレコードの "密度" が十分に大きい場合、このプロセスによってテーブル内のすべてのデータが実質的に再取り込みされるため、パフォーマンスと COGS に大きな影響が及びます。


## <a name="purge-limitations-and-considerations"></a>パージの制限事項と考慮事項

* **消去プロセスは最終処理であり、元に戻すことが**できません。 このプロセスを "元に戻す" ことや、削除されたデータを回復することはできません。 そのため、 [undo table drop](../management/undo-drop-table-command.md)などのコマンドは、消去されたデータを回復することはできません。また、以前のバージョンへのデータのロールバックは、最新の purge コマンドの "前" に進むことはできません。

* 誤りを回避するには、削除する前にクエリを実行して、予期された結果と一致することを確認するか、または削除する予定のレコード数を返す2段階のプロセスを使用して、述語を検証することをお勧めします。 

* 予防措置として、既定ではすべてのクラスターで消去プロセスが無効になっています。
   消去プロセスを有効にするには、[サポートチケット](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)を開く必要がある1回限りの操作です。`EnabledForPurge`機能を有効にするように指定してください。

* データ管理`.purge`エンドポイント`https://ingest-[YourClusterName].kusto.windows.net`に対してコマンドが実行されます。
   コマンドには、関連するデータベースに対する[データベース管理者](../management/access-control/role-based-authorization.md)権限が必要です。 
* 消去プロセスのパフォーマンスに影響があり、[消去のガイドライン](#purge-guidelines)に従っていることを保証するために、呼び出し元はデータスキーマを変更することを想定しています。これにより、最小限のテーブルに関連するデータが含まれるようになり、テーブルごとのバッチコマンドを実行して、消去プロセスの大きな COGS の影響を軽減できます。
* Purge `predicate`コマンドのパラメーターは、消去するレコードを指定するために使用されます[。](#purge-table-tablename-records-command)
`Predicate`サイズは 63 KB に制限されています。 を`predicate`構築する場合:
    * たとえば、のよう[に ' in ' 演算子](../query/inoperator.md)を使用します`where [ColumnName] in ('Id1', 'Id2', .. , 'Id1000')`。 
        * [' In ' 演算子](../query/inoperator.md)の制限 (リストには最大値を`1,000,000`含めることができます) に注意してください。
    * クエリのサイズが大きい場合は、 [' externaldata ' 演算子](../query/externaldata-operator.md)を使用し`where UserId in (externaldata(UserId:string) ["https://...blob.core.windows.net/path/to/file?..."])`ます (例:)。 ファイルには、削除する Id の一覧が格納されます。
        * すべての externaldata blob を展開した後のクエリサイズの合計 (すべての blob の合計サイズ) が 64 MB を超えることはできません。 

## <a name="purge-performance"></a>パフォーマンスの消去

特定の時点で、クラスターで実行できる消去要求は1つだけです。 他のすべての要求は、"スケジュール済み" 状態でキューに登録されます。 消去要求キューのサイズは、データに適用される要件に合わせて十分な制限内に保持する必要があります。

消去の実行時間を短縮するには:
* [消去のガイドライン](#purge-guidelines)に従って、削除されたデータの量を減らします。
* コールドデータの消去にかかる時間が長くなるため、[キャッシュポリシー](../management/cachepolicy.md)を調整します。
* クラスターをスケールアウトする

* 「[エクステントを消去](../management/capacitypolicy.md#extents-purge-rebuild-capacity)する」で詳しく説明するように、クラスターの消去容量は慎重に検討してください。 このパラメーターを変更するには、[サポートチケット](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)を開く必要があります

## <a name="trigger-the-purge-process"></a>消去プロセスをトリガーします

> [!Note]
> 消去[を実行する*TableName* ](#purge-table-tablename-records-command)には、データ管理エンドポイント (**https://ingest-[YourClusterName]. [region]. kusto. windows. net) を入力**します。

### <a name="purge-table-tablename-records-command"></a>テーブル*TableName*レコードの消去コマンド

Purge コマンドは、さまざまな使用シナリオに対して2つの方法で呼び出すことができます。
1. プログラムによる呼び出し: アプリケーションによって呼び出されるようにするための単一ステップです。 このコマンドを直接呼び出すと、実行シーケンスの消去がトリガーします。

    **構文**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     .purge table [TableName] records in database [DatabaseName] with (noregrets='true') <| [Predicate]
     ```

    > [!NOTE]
    > このコマンドは、 [Kusto クライアントライブラリ](../api/netfx/about-kusto-data.md)NuGet パッケージの一部として利用可能な CslCommandGenerator API を使用して生成します。

1. 人間による呼び出し: 個別の手順として明示的な確認を必要とする2段階のプロセス。 コマンドの最初の呼び出しでは、検証トークンが返されます。これは実際の消去を実行するために提供される必要があります。 このシーケンスにより、誤ったデータが誤って削除されるリスクが軽減されます。 このオプションを使用すると、大幅なコールドキャッシュデータを含む大きなテーブルでの完了に時間がかかる場合があります。
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
    
    |パラメーター  |説明  |
    |---------|---------|
    | DatabaseName   |   データベースの名前      |
    | TableName     |     テーブルの名前    |
    | Predicate    |    消去するレコードを識別します。 「Purge 述語の制限事項」を参照してください。 | 
    | noregrets    |     設定すると、シングルステップのアクティブ化がトリガーします。    |
    | verificationtoken     |  2段階のアクティブ化シナリオ (**noregrets**は設定されていません) では、このトークンを使用して2番目のステップを実行し、アクションをコミットできます。 **Verificationtoken**が指定されていない場合は、コマンドの最初のステップがトリガーされます。この手順では、消去が返されます。トークンは、ステップ #2 を実行するためにコマンドに渡す必要があります。   |

    **削除の述語の制限事項**
    * 述語は、単純な選択である必要があります (例: where *[columnname] = = ' x '* / *where [columnname] in (' x ', ' Y ', ' Z ') and [othercolumn] = = ' a '*)。
    * 複数のフィルターは、個別`where`の句 (など`where [ColumnName] == 'X' and  OtherColumn] == 'Y'` `where [ColumnName] == 'X' | where [OtherColumn] == 'Y'`) ではなく、' and ' と組み合わせる必要があります。
    * 述語は、削除されるテーブル以外のテーブル (*TableName*) を参照することはできません。 述語には、選択ステートメント (`where`) のみを含めることができます。 テーブルから特定の列を射影することはできません ('* `table` | ' を実行するときは出力スキーマ)。述語*' はテーブルスキーマと一致しなければなりません)。
    * システム関数 (、 `ingestion_time()`、 `extent_id()`など) は、述語の一部としてはサポートされていません。

#### <a name="example-two-step-purge"></a>例: 2 段階の消去

1. 2段階のアクティブ化シナリオで消去を開始するには、コマンドの手順 #1 を実行します。

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase <| where CustomerId in ('X', 'Y')
    ```

    **出力**

    |NumRecordsToPurge 関するお願い |EstimatedPurgeExecutionTime| VerificationToken
    |--|--|--
    |1596 |00:00:02 |e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

    ステップ #2 を実行する前に、NumRecordsToPurge 検証してください。 
2. 2段階のアクティブ化シナリオで消去を完了するには、手順 #1 から返された検証トークンを使用して、手順 #2 を実行します。

    ```kusto
    .purge table MyTable records in database MyDatabase
    with (verificationtoken='e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b')
    <| where CustomerId in ('X', 'Y')
    ```

    **出力**

    |OperationId |DatabaseName |TableName |ScheduledTime |Duration |LastUpdatedOn |EngineOperationId |状態 |StateDetails |EngineStartTime |EngineDuration |[再試行の回数] |ClientRequestId |プリンシパル
    |--|--|--|--|--|--|--|--|--|--|--|--|--|--
    |c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41: 05.4391686 |00:00: 00.1406211 |2019-01-20 11:41: 05.4391686 | |スケジュール | | | |0 |KE.RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD アプリ id =...

#### <a name="example-single-step-purge"></a>例: 単一ステップの消去

シングルステップライセンス認証のシナリオで消去をトリガーするには、次のコマンドを実行します。

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase with (noregrets='true') <| where CustomerId in ('X', 'Y')
    ```

**出力**

|OperationId |DatabaseName |TableName |ScheduledTime |Duration |LastUpdatedOn |EngineOperationId |状態 |StateDetails |EngineStartTime |EngineDuration |[再試行の回数] |ClientRequestId |プリンシパル
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41: 05.4391686 |00:00: 00.1406211 |2019-01-20 11:41: 05.4391686 | |スケジュール | | | |0 |KE.RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD アプリ id =...

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

このコマンドの出力は、"削除操作の*表示" コマンド*の出力と同じであり、取り消された消去操作の更新状態が示されます。 試行が成功した場合、操作の状態は "破棄済み" に更新されます。それ以外の場合は、操作の状態は変更されません。 

|OperationId |DatabaseName |TableName |ScheduledTime |Duration |LastUpdatedOn |EngineOperationId |状態 |StateDetails |EngineStartTime |EngineDuration |[再試行の回数] |ClientRequestId |プリンシパル
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41: 05.4391686 |00:00: 00.1406211 |2019-01-20 11:41: 05.4391686 | |Abandoned | | | |0 |KE.RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD アプリ id =...

## <a name="track-purge-operation-status"></a>削除操作の状態の追跡 

> [!Note]
> 消去操作は、データ管理エンドポイントに対して実行される [削除の[表示](#show-purges-command)] コマンドを使用して追跡でき**https://ingest-ます ([YourClusterName]. [region]. kusto. windows. net) を入力**します。

Status = ' Completed ' は、消去操作の最初のフェーズが正常に完了したことを示します。つまり、レコードは論理的に削除され、クエリに使用できなくなります。 お客様は、2番目のフェーズ (ハード削除) の完了を追跡して確認することは期待されて**いません**。 このフェーズは、Kusto によって内部で監視されます。

### <a name="show-purges-command"></a>削除コマンドの表示

`Show purges`コマンドは、要求された期間内に操作 Id を指定することによって、消去操作の状態を示します。 

```kusto
.show purges <OperationId>
.show purges [in database <DatabaseName>]
.show purges from '<StartDate>' [in database <DatabaseName>]
.show purges from '<StartDate>' to '<EndDate>' [in database <DatabaseName>]
```

|Properties  |説明  |必須/省略可能
|---------|---------|
|OperationId    |      1フェーズまたは2番目のフェーズを実行した後のデータ管理操作 Id outputed。   |Mandatory
|StartDate    |   フィルター処理の制限時間を短くします。 省略した場合、既定値は現在の時刻よりも24時間前になります。      |省略可能
|EndDate    |  フィルター処理の上限時間。 省略した場合、既定値は現在の時刻になります。       |省略可能
|DatabaseName    |     結果をフィルター処理するデータベース名。    |省略可能

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

|OperationId |DatabaseName |TableName |ScheduledTime |Duration |LastUpdatedOn |EngineOperationId |状態 |StateDetails |EngineStartTime |EngineDuration |[再試行の回数] |ClientRequestId |プリンシパル
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41: 05.4391686 |00:00: 33.6782130 |2019-01-20 11:42: 34.6169153 |a0825d4d-6b0f-47f3-a499-54ac5681ab78 |完了 |消去が正常に完了しました (削除を保留中の記憶域アイテム) |2019-01-20 11:41: 34.6486506 |00:00: 04.4687310 |0 |KE.RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD アプリ id =...

* **OperationId** -purge の実行時に返される DM 操作 ID。 
* **DatabaseName** -データベース名 (大文字と小文字が区別されます)。 
* **TableName** -テーブル名 (大文字と小文字が区別されます)。 
* **Scheduledtime** -DM サービスに対して purge コマンドを実行した時間。 
* **Duration** -実行 DM キューの待機時間を含む、消去操作の合計時間。 
* **EngineOperationId** -エンジンで実行されている実際の消去の操作 ID。 
* **状態**-消去状態は、次のいずれかになります。 
    * スケジュール済みの消去操作は、実行のスケジュールが設定されています。 ジョブが**スケジュール**されたままの場合は、削除操作のバックログが存在する可能性があります。 このバックログを消去するには、「[消去のパフォーマンス](#purge-performance)」を参照してください。 一時的なエラーが発生したときに消去操作が失敗した場合は、DM によって再試行され、**スケジュール**が再設定されます (したがって、**スケジュール**から処理済みから**スケジュール****済みに戻る**操作の切り替えが発生する可能性があります)。
    * InProgress-エンジンで消去処理が進行中です。 
    * 完了-消去が正常に完了しました。
    * BadInput-無効な入力の消去に失敗し、再試行されません。 これは、述語で構文エラーが発生した、削除コマンドの無効な述語、制限を超えたクエリ (externaldata 演算子の場合は1M エンティティ、合計拡張クエリサイズが 100 MB を超える場合など)、externaldata blob に対して404または403エラーが発生したことが原因である可能性があります。
    * 失敗-消去に失敗し、再試行されません。 これは、他の消去操作のバックログ、または再試行の制限を超えるエラーが発生したために、操作がキューで長時間待機していた (14 日を超えた) 場合に発生する可能性があります。 後者の場合、内部監視アラートが生成され、Azure データエクスプローラーチームによって調査されます。 
* StateDetails-状態の説明。
* EngineStartTime-コマンドがエンジンに発行された時刻。 この時間と ScheduledTime の間に大きな違いがある場合は、通常、消去操作の大きなバックログがあり、クラスターはそのペースに対応していません。 
* EngineDuration-エンジンで実際に消去が実行された時刻。 Purge が数回再試行された場合は、すべての実行期間の合計になります。 
* 再試行回数-一時的なエラーが原因で、DM サービスによって操作が再試行された回数。
* ClientRequestId-DM purge 要求のクライアントアクティビティ ID。 
* プリンシパル-purge コマンドの発行者の id。

## <a name="purging-an-entire-table"></a>テーブル全体の削除
テーブルを削除するには、テーブルを削除し、削除済みとしてマークします。これにより、[パージプロセス](#purge-process)で記述されたハード削除処理が実行されます。 消去せずにテーブルを削除しても、すべてのストレージアイテムが削除されるわけではありません (テーブルに最初に設定されたハードリテンション期間ポリシーに従って削除されます)。 `purge table allrecords`コマンドは短時間で効率的であり、シナリオに該当する場合は、レコードの消去プロセスの方がはるかに適しています。 

> [!Note]
> このコマンドは、データ管理エンド[ *TableName* ](#purge-table-tablename-allrecords-command)ポイント (**https://ingest-[YourClusterName]. [) で purge table TableName allrecords コマンドを実行して呼び出されます。region]. kusto. windows. net) を入力**します。

### <a name="purge-table-tablename-allrecords-command"></a>purge table *TableName* allrecords コマンド

"[Purge table records ](#purge-table-tablename-records-command)" コマンドと同様に、このコマンドはプログラムによって (シングルステップ)、または手動 (2 段階) モードで呼び出すことができます。
1. プログラムによる起動 (シングルステップ):

     **構文**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     .purge table [TableName] in database [DatabaseName] allrecords with (noregrets='true')
     ```

2. 人間による呼び出し (2 段階):

     **構文**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (the table will not be purged until step #2 is executed)
     .purge table [TableName] in database [DatabaseName] allrecords

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] in database [DatabaseName] allrecords with (verificationtoken='<verification token from step #1>')
     ```

    |パラメーター  |説明  |
    |---------|---------|
    |**DatabaseName**   |   データベースの名前です。      |
    |**TableName**     |     テーブルの名前。    |
    |**noregrets**    |     設定すると、シングルステップのアクティブ化がトリガーします。    |
    |**verificationtoken**     |  2段階のアクティブ化シナリオ (**noregrets**は設定されていません) では、このトークンを使用して2番目のステップを実行し、アクションをコミットできます。 **Verificationtoken**が指定されていない場合は、コマンドの最初のステップ (トークンが返される) がトリガーされ、コマンドに戻り、コマンドの手順 #2 を実行します。|

#### <a name="example-two-step-purge"></a>例: 2 段階の消去

1. 2段階のアクティブ化シナリオで消去を開始するには、コマンドの手順 #1 を実行します。 

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable in database MyDatabase allrecords
    ```

    **出力**

    | VerificationToken
    |--
    |e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

1.  2段階のアクティブ化シナリオで消去を完了するには、手順 #1 から返された検証トークンを使用して、手順 #2 を実行します。

    ```kusto
    .purge table MyTable in database MyDatabase allrecords 
    with (verificationtoken='eyJTZXJ2aWNlTmFtZSI6IkVuZ2luZS1pdHNhZ3VpIiwiRGF0YWJhc2VOYW1lIjoiQXp1cmVTdG9yYWdlTG9ncyIsIlRhYmxlTmFtZSI6IkF6dXJlU3RvcmFnZUxvZ3MiLCJQcmVkaWNhdGUiOiIgd2hlcmUgU2VydmVyTGF0ZW5jeSA9PSAyNSJ9')
    ```
    
    出力は '. テーブルの表示 ' コマンドの出力と同じです (削除されたテーブルが返されません)。

    **出力**

    |TableName|DatabaseName|フォルダー|DocString
    |---|---|---|---
    |OtherTable|MyDatabase|---|---


#### <a name="example-single-step-purge"></a>例: 単一ステップの消去

シングルステップライセンス認証のシナリオで消去をトリガーするには、次のコマンドを実行します。

```kusto
// Connect to the Data Management service
#connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 

.purge table MyTable in database MyDatabase allrecords with (noregrets='true')
```

出力は '. テーブルの表示 ' コマンドの出力と同じです (削除されたテーブルが返されません)。

**出力**

|TableName|DatabaseName|フォルダー|DocString
|---|---|---|---
|OtherTable|MyDatabase|---|---


