---
title: データパージ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのデータ消去について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 49d024d1deecd8e0c7bf16eda9917cd237fe6319
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523275"
---
# <a name="data-purge"></a>データの消去

>[!Note]
> この記事は、デバイスまたはサービスから個人用データを削除する手順について説明しており、GDPR での義務を果たすために使用できます。 GDPR に関する一般情報を探している場合は、[サービス信頼ポータル の GDPR セクションを](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)参照してください。



データ プラットフォームとして、Azure データ エクスプローラー (Kusto) は、各レコードを削除する機能`.purge`をサポートしています。 [テーブル全体を削除](#purging-an-entire-table)することもできます。  

> [!WARNING]
> コマンドによる`.purge`データの削除は、個人データを保護するために使用するように設計されており、他のシナリオでは使用しないでください。 頻繁な削除要求や大量のデータの削除をサポートするようには設計されておらず、サービスのパフォーマンスに大きな影響を与える可能性があります。

## <a name="purge-guidelines"></a>パージのガイドライン

Azure Data Explorer に個人データを格納するチームは、データ スキーマの設計と関連するポリシーの調査を慎重に行った後にのみ、この操作を**行うことを強くお勧めします**。

1. 最良のシナリオでは、このデータの保有期間は十分に短く、データは自動的に削除されます。
2. 保有期間の使用が不可能な場合は、プライバシールールに従うすべてのデータを少数の Kusto テーブル (最適には 1 つのテーブル) に分離し、他のすべてのテーブルからリンクすることをお勧めします。 これにより、機密データを保持する少数のテーブルでデータ[の削除処理](#purge-process)を実行し、他のすべてのテーブルを回避できます。
3. 呼び出し元は、1 日あたり`.purge`1 ~ 2 個のコマンドに対してコマンドの実行をバッチ処理する必要があります。
   複数のコマンドを発行しないでください。むしろ、削除を必要とするすべてのユーザー ID を述語に含む単一のコマンドを送信します。

## <a name="purge-process"></a>パージプロセス

Azure データ エクスプローラーから選択的にデータを削除するプロセスは、次の手順で行われます。

1. **フェーズ 1:** Kusto テーブル名と、削除するレコードを示すレコードごとの述語を指定すると、Kusto はテーブルをスキャンして、データパージに関与するデータシャードを識別します (述語が true を返すレコードが 1 つ以上ある)。
2. **フェーズ 2: (ソフト削除)** 表内の各データシャード (ステップ (1) で識別) を、述語が true を返すレコードを持たない再取り込みバージョンに置き換えます。
   新しいデータがテーブルに取り込まれる場合、このフェーズのクエリの終了までに、述語が true を返すデータが返されなくなります。 
   パージソフト削除フェーズの期間は、パージする必要があるレコードの数、クラスター内のデータシャードへのそれらの分散、クラスター内のノード数、パージ操作に備えた予備容量、およびその他のいくつかの要因によって異なります。 フェーズ 2 の期間は、数秒から数時間の間で変化します。
3. **フェーズ 3: (ハード削除)**「有害」データを持つ可能性のあるすべてのストレージ成果物を取り戻し、ストレージから削除します。 このフェーズは、前のフェーズが完了*してから*少なくとも 5 日後に実行されますが、最初のコマンドの 30 日後はデータ プライバシー要件に準拠していません。

コマンドを`.purge`発行すると、このプロセスがトリガーされ、完了までに数日かかります。 述語が適用されるレコードの「密度」が十分に大きい場合、プロセスはテーブル内のすべてのデータを効果的に再取り込みし、したがってパフォーマンスと COGS に大きな影響を与えます。


## <a name="purge-limitations-and-considerations"></a>パージの制限と考慮事項

* **パージプロセスは最終的かつ元に戻せません**。 このプロセスを "元に戻す" または削除されたデータを回復することはできません。 したがって[、undo テーブル・ドロップ](../management/undo-drop-table-command.md)などのコマンドは、パージされたデータを回復できず、データを以前のバージョンにロールバックしても、最新のパージ・コマンドの「前」に戻ることはできません。

* 間違いを避けるには、結果が予想される結果と一致することを確認するために、パージの前にクエリを実行して述部を検証するか、またはパージされるレコードの予想数を返す 2 ステップ プロセスを使用することをお勧めします。 

* 予防措置として、デフォルトではすべてのクラスターでパージ処理が無効になっています。
   パージプロセスを有効にすることは、[サポートチケット](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)を開く必要があるワンタイム操作です。`EnabledForPurge`機能を有効にするように指定してください。

* コマンド`.purge`は、データ管理エンドポイントに対して実行`https://ingest-[YourClusterName].kusto.windows.net`されます。
   このコマンドには、関連するデータベースに対する[データベース管理者権限](../management/access-control/role-based-authorization.md)が必要です。 
* パージ プロセスのパフォーマンスに影響を及ぼすこと、および[パージ のガイドライン](#purge-guidelines)に従っていることを保証するために、呼び出し元は、データ スキーマを変更して、テーブルごとに関連するデータを最小限に抑え、テーブルごとのバッチ コマンドを使用して、パージ プロセスの重大な COGS 影響を軽減することが期待されます。
* purge`predicate`コマンドの[.purge](#purge-table-tablename-records-command)パラメーターは、パージするレコードを指定するために使用されます。
`Predicate`サイズは 63 KB に制限されています。 を構築する場合`predicate`:
    * ['in' 演算子](../query/inoperator.md)を使用します。 `where [ColumnName] in ('Id1', 'Id2', .. , 'Id1000')` 
        * ['in' 演算子](../query/inoperator.md)の制限に注意してください (リストには最大`1,000,000`値を含めることができます)。
    * クエリのサイズが大きい場合は、"[外部データ" 演算子](../query/externaldata-operator.md)を使用します`where UserId in (externaldata(UserId:string) ["https://...blob.core.windows.net/path/to/file?..."])`。 ファイルには、パージする ID のリストが格納されます。
        * すべての外部データ BLOB (すべての BLOB の合計サイズ) を拡張した後のクエリの合計サイズは、64 MB を超えることはできません。 

## <a name="purge-performance"></a>パージのパフォーマンス

クラスタ上で同時に実行できるパージ要求は 1 つだけです。 その他のすべての要求は"スケジュール済み"状態でキューに入れられます。 パージ要求キューのサイズは監視し、データに適用される要件に合わせて十分な制限内に収まるようにする必要があります。

パージ実行時間を短縮するには::
* パージのガイドラインに従ってパージされたデータの量[を減](#purge-guidelines)らす
* コールド データの削除に時間がかかるため[、キャッシュ ポリシー](../management/cachepolicy.md)を調整します。
* クラスターのスケールアウト

* [「エクステントパージ再構築](../management/capacitypolicy.md#extents-purge-rebuild-capacity)容量」の詳細に従って、慎重に検討した後でクラスタパージ容量を増やす。 このパラメーターを変更するには[、サポート チケット](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)を開く必要があります

## <a name="trigger-the-purge-process"></a>パージ プロセスをトリガーする

> [!Note]
> パージの実行は、データ管理エンドポイント (**https://ingest-[YourClusterName].kusto.windows.net)** で[*テーブル名*レコードのパージ](#purge-table-tablename-records-command)コマンドを実行することによって呼び出されます。

### <a name="purge-table-tablename-records-command"></a>テーブル*名レコードの*パージ コマンド

Purge コマンドは、さまざまな使用シナリオで次の 2 つの方法で呼び出すことができます。
1. プログラムによる呼び出し: アプリケーションによって呼び出されることを意図した単一ステップ。 このコマンドを直接呼び出すと、実行シーケンスの削除がトリガーされます。

    **構文**
     ```kusto
     .purge table [TableName] records in database [DatabaseName] with (noregrets='true') <| [Predicate]
     ```

    > [!NOTE]
    > このコマンドを生成するには、ClCommandGenerator API を使用して[、Kusto クライアント ライブラリ](../api/netfx/about-kusto-data.md)NuGet パッケージの一部として使用できます。

1. ヒューマン呼び出し: 明示的な確認を別のステップとして必要とする 2 段階のプロセス。 コマンドの最初の呼び出しは、実際のパージを実行するために提供される検証トークンを返します。 このシーケンスにより、誤ったデータが誤って削除されるリスクが軽減されます。 このオプションを使用すると、重要なコールド キャッシュ データを持つ大きなテーブルで完了するのに時間がかかる場合があります。
    <!-- If query times-out on DM endpoint (default timeout is 10 minutes), it is recommended to use the [engine `whatif` command](#purge-whatif-command) directly againt the engine endpoint while increasing the [server timeout limit](../concepts/querylimits.md#limit-on-request-execution-time-timeout). Only after you have verified the expected results using the engine whatif command, issue the purge command via the DM endpoint using the 'noregrets' option. -->

     **構文**
     ```kusto
     // Step #1 - retrieve a verification token (no records will be purged until step #2 is executed)
     .purge table [TableName] records in database [DatabaseName] <| [Predicate]

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] records in database [DatabaseName] with (verificationtoken='<verification token from step #1>') <| [Predicate]
     ```
    
    |パラメーター  |説明  |
    |---------|---------|
    | DatabaseName   |   データベースの名前      |
    | TableName     |     テーブルの名前    |
    | Predicate    |    パージするレコードを識別します。 以下の「パージ述語の制限事項」を参照してください。 | 
    | 後悔なし    |     設定されている場合、シングルステップのアクティベーションがトリガーされます。    |
    | 検証トークン     |  2 段階のアクティブ化シナリオ **(noregrets**が設定されていません) では、このトークンを使用して 2 番目のステップを実行し、アクションをコミットできます。 **検証トークン**が指定されていない場合、コマンドの最初のステップがトリガーされ、そのコマンドの最初のステップでは、パージに関する情報が返され、トークンが返され、ステップ#2を実行するためにコマンドに戻されます。   |

    **パージ述語の制限**
    * 述語は単純な選択である必要があります (たとえば *、[列名] =='X'* / で [*列名] が [X' ('X'、'Y'、'Z') および [その他の列] == 'A'*)。
    * 複数のフィルタは、個別`where`の句ではなく'and' と組み合わせる必要があります (`where [ColumnName] == 'X' and  OtherColumn] == 'Y'`たとえば、`where [ColumnName] == 'X' | where [OtherColumn] == 'Y'`除外する)。
    * 述語は、パージされるテーブル以外のテーブルを参照できません (*TableName*)。 述語には選択文 ( )`where`のみを含めることができます。 テーブルから特定の列を投影することはできません ('* `table` |述語*' はテーブル スキーマと一致する必要があります。
    * システム関数 ( など`ingestion_time()` `extent_id()`) は述語の一部としてサポートされていません。

#### <a name="example-two-step-purge"></a>例: 2 ステップのパージ

1. 2 段階のアクティブ化シナリオでパージを開始するには、コマンドのステップ #1を実行します。

    ```kusto
    .purge table MyTable records in database MyDatabase <| where CustomerId in ('X', 'Y')
    ```

    **出力**

    |レコードをパージします。 |推定パージ実行時間| 検証トークン
    |--|--|--
    |1,596 |00:00:02 |e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5adf65057fa65736b

    ステップ #2を実行する前に、NumRecordsToPurge を検証します。 
2. 2 段階のアクティブ化シナリオでパージを完了するには、ステップ #1から返された検証トークンを使用して、ステップ #2を実行します。

    ```kusto
    .purge table MyTable records in database MyDatabase
    with (verificationtoken='e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b')
    <| where CustomerId in ('X', 'Y')
    ```

    **出力**

    |OperationId |DatabaseName |TableName |スケジュールされた時刻 |Duration |ラストアップデートオン |エンジンオペレーションId |State |ステートの詳細 |エンジン開始時間 |エンジン期間 |[再試行の回数] |ClientRequestId |プリンシパル
    |--|--|--|--|--|--|--|--|--|--|--|--|--|--
    |c9651d74-3b80-4183-90bb-bbe9e42eadc4 |データベース |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |スケジュール | | | |0 |柯。実行コマンド;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD アプリ id=..

#### <a name="example-single-step-purge"></a>例: シングルステップ・パージ

シングルステップのアクティブ化シナリオでパージをトリガーするには、次のコマンドを実行します。

```kusto
.purge table MyTable records in database MyDatabase with (noregrets='true') <| where CustomerId in ('X', 'Y')
```

**出力**

|OperationId |DatabaseName |TableName |スケジュールされた時刻 |Duration |ラストアップデートオン |エンジンオペレーションId |State |ステートの詳細 |エンジン開始時間 |エンジン期間 |[再試行の回数] |ClientRequestId |プリンシパル
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |データベース |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |スケジュール | | | |0 |柯。実行コマンド;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD アプリ id=..

### <a name="cancel-purge-operation-command"></a>パージ操作のキャンセル コマンド

必要に応じて、保留中のパージ要求をキャンセルできます。

> [!NOTE]
> この操作は、エラー回復シナリオを想定しています。 成功が保証されるものではなく、通常の運用フローの一部であってはならない。 キュー内の要求にのみ適用できます (まだ実行のためにエンジン ノードにディスパッチされていません)。 コマンドは、データ管理エンドポイントで実行されます。

**構文**

```kusto
 .cancel purge <OperationId>
 ```

**例**

```kusto
 .cancel purge aa894210-1c60-4657-9d21-adb2887993e1
 ```

**出力**

このコマンドの出力は'show purges *OperationId'* コマンド出力と同じで、削除操作の更新済みステータスが取り消されます。 試行が成功すると、操作状態は 「破棄済み」に更新され、それ以外の場合は操作状態は変更されません。 

|OperationId |DatabaseName |TableName |スケジュールされた時刻 |Duration |ラストアップデートオン |エンジンオペレーションId |State |ステートの詳細 |エンジン開始時間 |エンジン期間 |[再試行の回数] |ClientRequestId |プリンシパル
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |データベース |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Abandoned | | | |0 |柯。実行コマンド;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD アプリ id=..

## <a name="track-purge-operation-status"></a>パージ操作の状態を追跡する 

> [!Note]
> パージ操作は、データ管理エンドポイント (**https://ingest-[YourClusterName].kusto.windows.net)** に対して実行される[show purges](#show-purges-command)コマンドを使用して追跡できます。

Status = 'Completed' は、削除操作の最初のフェーズが正常に完了したことを示します。 お客様は、第 2 フェーズ (ハード削除) の完了を追跡および検証**することは想定されていません**。 このフェーズは Kusto によって内部で監視されます。

### <a name="show-purges-command"></a>[パージ] コマンドを表示する

`Show purges`コマンドは、要求された期間内に操作 ID を指定することによって、パージ操作の状態を示します。 

```kusto
.show purges <OperationId>
.show purges [in database <DatabaseName>]
.show purges from '<StartDate>' [in database <DatabaseName>]
.show purges from '<StartDate>' to '<EndDate>' [in database <DatabaseName>]
```

|Properties  |説明  |必須/オプション
|---------|---------|
|OperationId    |      単一フェーズまたは第 2 フェーズの実行後に出力されるデータ管理操作 ID。   |Mandatory
|StartDate    |   フィルタリング操作の時間制限を下げる。 省略すると、既定では現在時刻の 24 時間前になります。      |省略可能
|EndDate    |  フィルタリング操作の制限時間の上限。 省略すると、デフォルトは現在時刻になります。       |省略可能
|DatabaseName    |     結果をフィルター処理するデータベース名。    |省略可能

> [!NOTE]
> ステータスは、クライアントがデータベース[管理者](../management/access-control/role-based-authorization.md)のアクセス許可を持つデータベースでのみ提供されます。

**使用例**

```kusto
.show purges
.show purges c9651d74-3b80-4183-90bb-bbe9e42eadc4
.show purges from '2018-01-30 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00' in database MyDatabase
```

**出力** 

|OperationId |DatabaseName |TableName |スケジュールされた時刻 |Duration |ラストアップデートオン |エンジンオペレーションId |State |ステートの詳細 |エンジン開始時間 |エンジン期間 |[再試行の回数] |ClientRequestId |プリンシパル
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |データベース |MyTable |2019-01-20 11:41:05.4391686 |00:00:33.6782130 |2019-01-20 11:42:34.6169153 |a0825d4d-6b0f-47f3-a499-54ac5681ab78 |完了 |正常に完了しました (ストレージ成果物は削除待ち) |2019-01-20 11:41:34.6486506 |00:00:04.4687310 |0 |柯。実行コマンド;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD アプリ id=..

* **オペレーション ID** : パージの実行時に返される DM 操作 ID。 
* **データベース名**- データベース名 (大文字と小文字を区別します)。 
* **テーブル名**- テーブル名 (大文字と小文字を区別します)。 
* **スケジュールされた時刻**- DM サービスに対してパージ コマンドを実行する時刻。 
* **期間**- 実行 DM キューの待機時間を含む、パージ操作の合計時間。 
* **エンジン操作 ID** - エンジンで実行されている実際のパージの操作 ID。 
* **状態**- パージ状態、次のいずれかになります。 
    * スケジュール済み - パージ操作が実行にスケジュールされます。 ジョブが**スケジュール済み**である場合、おそらくパージ操作のバックログがあります。 このバックログを[クリアするには、パージ・パフォーマンス](#purge-performance)を参照してください。 一時的なエラーでパージ操作が失敗した場合、DM によって再試行され、**再度スケジュール済**みに設定されます (そのため、操作が**スケジュール済**みから**InProgress**に移行し、**スケジュール済**みに戻る場合があります)。
    * [処理中]: パージの操作がエンジンで進行中です。 
    * 完了 - パージは正常に完了しました。
    * BadInput - 不正な入力でパージが失敗し、再試行されません。 これは、述語の構文エラー、パージ コマンドの無効な述語、制限を超えるクエリ (外部データ演算子の 1M を超えるエンティティ、または合計展開クエリ サイズの 64 MB を超えるクエリなど) 、外部データ BLOB の 404 または 403 エラーなど、さまざまな問題が原因である可能性があります。
    * 失敗 - パージに失敗し、再試行されません。 これは、他のパージ操作のバックログまたは再試行制限を超えるエラーが原因で、操作がキューで待ち時間が長すぎる (14 日間) 場合に発生することがあります。 後者は内部監視アラートを発生させ、Azure データ エクスプローラー チームによって調査されます。 
* ステートの詳細 - 状態の説明。
* エンジンの開始時間- コマンドがエンジンに対して発行された時刻。 この時間とScheduledTimeの間に大きな違いがある場合、通常はパージ操作の大きなバックログがあり、クラスターはペースに追いついていなくてもいいです。 
* EngineDuration - エンジン内の実際のパージ実行時間。 パージが数回再試行された場合は、すべての実行期間の合計になります。 
* 再試行回数 - 一時的なエラーが原因で DM サービスによって操作が再試行された回数。
* クライアント要求 ID - DM パージ要求のクライアント アクティビティ ID。 
* プリンシパル - パージ コマンド発行者の ID。



## <a name="purging-an-entire-table"></a>テーブル全体の削除
テーブルのパージには、テーブルの削除と、パージ プロセスで説明されているハード削除プロセスが実行されるように[パージ](#purge-process)済みとしてマークすることが含まれます。 削除せずにテーブルを削除しても、そのテーブルのストレージ アーティファクトがすべて削除されるわけではありません (最初にテーブルに設定されたハード保持ポリシーに従って削除されます)。 コマンド`purge table allrecords`は迅速かつ効率的であり、シナリオに該当する場合は、レコードのパージ プロセスよりも優れています。 

> [!Note]
> このコマンドは、データ管理エンドポイント (**https://ingest-[YourClusterName].kusto.windows.net)** で[*テーブル名*allrecords](#purge-table-tablename-allrecords-command)コマンドを実行して呼び出されます。

### <a name="purge-table-tablename-allrecords-command"></a>パージ テーブル*名*すべてのレコード コマンド

'[.purge table records ](#purge-table-tablename-records-command)' コマンドと同様に、このコマンドはプログラム (シングル ステップ) または手動 (2 ステップ) モードで呼び出すことができます。
1. プログラムによる呼び出し (シングルステップ):

     **構文**
     ```kusto
     .purge table [TableName] in database [DatabaseName] allrecords with (noregrets='true')
     ```

2. 人間の呼び出し(2段階):

     **構文**
     ```kusto
     // Step #1 - retrieve a verification token (the table will not be purged until step #2 is executed)
     .purge table [TableName] in database [DatabaseName] allrecords

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] in database [DatabaseName] allrecords with (verificationtoken='<verification token from step #1>')
     ```

    |パラメーター  |説明  |
    |---------|---------|
    |**DatabaseName**   |   データベースの名前です。      |
    |**TableName**     |     テーブルの名前。    |
    |**後悔なし**    |     設定されている場合、シングルステップのアクティベーションがトリガーされます。    |
    |**検証トークン**     |  2 段階のアクティブ化シナリオ **(noregrets**が設定されていません) では、このトークンを使用して 2 番目のステップを実行し、アクションをコミットできます。 **検証トークン**が指定されていない場合は、トークンが返されるコマンドの最初のステップがトリガーされ、コマンドに戻り、コマンドのステップ#2が実行されます。|

#### <a name="example-two-step-purge"></a>例: 2 ステップのパージ

1. 2 段階のアクティブ化シナリオでパージを開始するには、コマンドのステップ #1を実行します。 

    ```kusto
    .purge table MyTable in database MyDatabase allrecords
    ```

    **出力**

    | 検証トークン
    |--
    |e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5adf65057fa65736b

1.  2 段階のアクティブ化シナリオでパージを完了するには、ステップ #1から返された検証トークンを使用して、ステップ #2を実行します。

    ```kusto
    .purge table MyTable in database MyDatabase allrecords 
    with (verificationtoken='eyJTZXJ2aWNlTmFtZSI6IkVuZ2luZS1pdHNhZ3VpIiwiRGF0YWJhc2VOYW1lIjoiQXp1cmVTdG9yYWdlTG9ncyIsIlRhYmxlTmFtZSI6IkF6dXJlU3RvcmFnZUxvZ3MiLCJQcmVkaWNhdGUiOiIgd2hlcmUgU2VydmVyTGF0ZW5jeSA9PSAyNSJ9')
    ```
    出力は '.show tables' コマンド出力と同じです (パージされたテーブルなしで返されます)。

    **出力**

    |TableName|DatabaseName|Folder|ドクスト文字列
    |---|---|---|---
    |その他のテーブル|データベース|---|---


#### <a name="example-single-step-purge"></a>例: シングルステップ・パージ

シングルステップのアクティブ化シナリオでパージをトリガーするには、次のコマンドを実行します。

```kusto
.purge table MyTable in database MyDatabase allrecords with (noregrets='true')
```

出力は '.show tables' コマンド出力と同じです (パージされたテーブルなしで返されます)。

**出力**

|TableName|DatabaseName|Folder|ドクスト文字列
|---|---|---|---
|その他のテーブル|データベース|---|---


