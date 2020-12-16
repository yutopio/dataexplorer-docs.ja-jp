---
title: Azure Data Explorer のインジェスト エラー コード
description: このトピックでは、Azure Data Explorer のインジェスト エラー コードの一覧を示します。
author: orspod
ms.author: orspodek
ms.reviewer: vladikbr
ms.service: data-explorer
ms.topic: reference
ms.date: 11/11/2020
ms.openlocfilehash: 18ac718edd50c804f71b9b82cbffb5b2b7bb2e24
ms.sourcegitcommit: 202357f866801aafd92e3e29a84bb312e17aebc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2020
ms.locfileid: "96933777"
---
# <a name="ingestion-error-codes-in-azure-data-explorer"></a>Azure Data Explorer のインジェスト エラー コード

次の一覧に、[インジェスト](ingest-data-overview.md)中に発生する可能性のあるエラー コードを示します。 クラスターで、失敗したインジェストの [診断ログ](using-diagnostic-logs.md#ingestion-logs-schema)を有効にすると、**失敗したインジェスト** の操作ログにエラー コードが表示されます。 また、**インジェストの結果** の [メトリック](using-metrics.md#ingestion-metrics)を監視して、インジェスト エラーの **カテゴリ** を確認することもできますが、特定のエラー コードは確認できません。 以下のエラーは、これらのカテゴリ別に分類されています。 

> [!NOTE]
> エラーが一時的なものである場合は、インジェストの再試行が成功する可能性があります。

## <a name="category-badformat"></a>カテゴリ:BadFormat

|エラー メッセージ                                 |説明                                           |永続的/一時的|
|---|---|---|
|Stream_WrongNumberOfFields                        |入力レコードのフィールドの数に不整合があります。 HRESULT:0x80DA0008      |永続的           |
|Stream_ClosingQuoteMissing                        |CSV 形式が無効です。 終わりの引用符がありません。 HRESULT:0x80DA000b            |永続的           |
|BadRequest_InvalidBlob                            |BLOB が無効です。                                                              |永続的           |
|BadRequest_EmptyArchive                           |アーカイブが空です。                                                             |永続的           |
|BadRequest_InvalidArchive                         |アーカイブが無効です。                                                           |永続的           |
|BadRequest_InvalidMapping                         |インジェスト マッピングを解析できませんでした。<br>インジェスト マッピングを作成する方法の詳細については、「[データ マッピング](./kusto/management/mappings.md)」を参照してください。   |永続的           |
|BadRequest_InvalidMappingReference                |マッピング参照が無効です。            |永続的           |
|BadRequest_FormatNotSupported                     |形式がサポートされていません。 これは、特定のデータ接続でサポートされていない形式を使用していることが原因である可能性があります。 <br>インジェストについて Azure Data Explorer でサポートされるデータ形式の詳細については、「[サポートされるデータ形式](ingestion-supported-formats.md)」を参照してください。 |永続的          |
|BadRequest_InconsistentMapping                    |サポートされているインジェスト マッピングが、既存のテーブル スキーマと一致しません。 |永続的           |
|BadRequest_UnexpectedCharacterInInputStream       |入力ストリームに予期しない文字があります。                                     |永続的           |

## <a name="category-badrequest"></a>カテゴリ:BadRequest

|エラー メッセージ                                 |説明                                           |永続的/一時的|
|---|---|---|
|BadRequest_EmptyBlob                              |BLOB が空です。                                                               |永続的           |
|BadRequest_EmptyBlobUri                           |BLOB URI が空です。                                                           |永続的           |
|BadRequest_DuplicateMapping                       |インジェスト プロパティに、ingestionMapping と ingestionMappingReference の両方が含まれていますが、これは無効です。              |永続的          |
|BadRequest_InvalidOrEmptyTableName                |テーブル名が空か無効です。<br>Azure Data Explorer の名前付け規則の詳細については、「[エンティティ名](./kusto/query/schema-entities/entity-names.md)」を参照してください。    |永続的          |
|BadRequest_EmptyDatabaseName                      |データベース名が空です。             |永続的          |
|BadRequest_EmptyMappingReference                  |一部の形式では、インジェスト マッピングが取り込まれる必要がありますが、マッピング参照が空です。<br>マッピングの詳細については、「[データ マッピング](./kusto/management/mappings.md)」を参照してください。        |永続的           |
|Download_BadRequest                               |要求が正しくないため、Azure Storage からソースをダウンロードできませんでした。    |永続的           |
|BadRequest_MissingMappingtFailure                 |Avro および Json 形式は、ingestionMapping または ingestionMappingReference パラメーターを使用して取り込む必要があります。         |永続的           |
|Stream_NoDataToIngest                             |取り込むデータが見つかりませんでした。<br>JSON 形式のデータの場合、このエラーは JSON 形式が無効であることを示している可能性があります。        |永続的          |
|Stream_DynamicPropertyBagTooLarge                 |データの動的列に含まれる値が大きすぎます。 HRESULT:0x80DA000E         |永続的          |
|General_BadRequest                                |無効な要求です。            |永続的           |
|BadRequest_CorruptedMessage                       |メッセージが破損しています。    |永続的           |
|BadRequest_SyntaxError                            |要求の構文エラーです。     |永続的           |
|BadRequest_ZeroRetentionPolicyWithNoUpdatePolicy  |テーブルにはゼロ保持ポリシーが設定されており、またどの更新ポリシーのソース テーブルでもありません。    |永続的           |
|BadRequest_CreationTimeEarlierThanSoftDeletePeriod|インジェスト用に指定された作成時刻が `SoftDeletePeriod` 内ではありません。<br>`SoftDeletePeriod` の詳細については、「[ポリシー オブジェクト](./kusto/management/retentionpolicy.md#the-policy-object)」を参照してください。  |永続的   |
|BadRequest_NotSupported                           |要求はサポートされていません。    |永続的           |
|Download_SourceNotFound                           |Azure Storage からソースをダウンロードできませんでした。 ソースが見つかりません。       |永続的       |
|BadRequest_EntityNameIsNotValid                   |エンティティ名が無効です。<br>Azure Data Explorer の名前付け規則の詳細については、「[エンティティ名](./kusto/query/schema-entities/entity-names.md)」を参照してください。    |永続的           |
|BadRequest_MalformedIngestionProperty              |インジェスト プロパティの形式に誤りがあります。    |永続的           |
| BadRequest_IngestionPropertyNotSupportedInThisContext | インジェスト プロパティはこのコンテキストではサポートされていません| 永続的

## <a name="category-dataaccessnotauthorized"></a>カテゴリ:DataAccessNotAuthorized

|エラー メッセージ                                 |説明                                           |永続的/一時的|
|---|---|---|
|Download_AccessConditionNotSatisfied              |Azure Storage からソースをダウンロードできませんでした。 アクセス条件が満たされていません。     |永続的           |
|Download_Forbidden                                |Azure Storage からソースをダウンロードできませんでした。 アクセスは禁止されています。    |永続的           |
|Download_AccountNotFound                          |Azure Storage からソースをダウンロードできませんでした。 アカウントが見つかりません。    |永続的           |
|BadRequest_TableAccessDenied                      |テーブルへのアクセスが拒否されました。<br>詳しくは、「[Kusto でのロールベースの認可](./kusto/management/access-control/role-based-authorization.md)」を参照してください。     |永続的           |
|BadRequest_DatabaseAccessDenied                   |データベースへのアクセスが拒否されました。<br>詳しくは、「[Kusto でのロールベースの認可](./kusto/management/access-control/role-based-authorization.md)」を参照してください。                        |永続的           |

## <a name="category-downloadfailed"></a>カテゴリ:DownloadFailed

|エラー メッセージ                                 |説明                                           |永続的/一時的|
|---|---|---|
|Download_NotTransient                             |Azure Storage からソースをダウンロードできませんでした。 一時的でないエラーが発生しました。                   |永続的           |
|Download_UnknownError                             |Azure Storage からソースをダウンロードできませんでした。 不明なエラーが発生しました              |一時的           |


## <a name="category-entitynotfound"></a>カテゴリ:EntityNotFound

|エラー メッセージ                                 |説明                                           |永続的/一時的|
|---|---|---|
|BadRequest_MappingReferenceWasNotFound            |マッピング参照が見つかりませんでした。   |永続的           |
|BadRequest_DatabaseNotExist                       |データベースが存在しません。          |永続的           |
|BadRequest_TableNotExist                          |テーブルが存在しません。          |永続的           |
|BadRequest_EntityNotFound                         |Azure Data Explorer エンティティ (マッピング、データベース、テーブルなど) が見つかりませんでした。           |永続的           |

## <a name="category-filetoolarge"></a>カテゴリ:FileTooLarge

|エラー メッセージ                                 |説明                                           |永続的/一時的|
|---|---|---|
|Stream_InputStreamTooLarge                        |入力データの合計サイズまたはデータ内の 1 つのフィールドが大きすぎます。 HRESULT:0x80DA0009                 |永続的          |
|BadRequest_FileTooLarge                           |BLOB のサイズがインジェストで許可されているサイズの上限を超えました。<br>インジェストのサイズ制限の詳細については、[Azure Data Explorer のデータ インジェストの概要](ingest-data-overview.md#comparing-ingestion-methods-and-tools)に関するページを参照してください。 |永続的           |

## <a name="category-internalserviceerror"></a>カテゴリ:InternalServiceError

|エラー メッセージ                                 |説明                                           |永続的/一時的|
|---|---|---|
|General_InternalServerError                       |内部サーバー エラーが発生しました。                     |一時的          |
|General_TransientSchemaMismatch                   |インジェストを開始するときのターゲット テーブルのスキーマが、インジェストのコミット時のスキーマと一致しません。         |一時的           |
|タイムアウト                                            |タイムアウトのため、操作は中止されました。     |一時的           |
|BadRequest_MessageExhausted                       |インジェストが最大再試行回数または最大再試行期間に達したため、データを取り込むことができませんでした。<br>インジェストの再試行は成功する可能性があります。   |一時的          |

## <a name="category-updatepolicyfailure"></a>カテゴリ:UpdatePolicyFailure

|エラー メッセージ                                 |説明                                           |永続的/一時的|
|---|---|---|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema   |更新ポリシーを呼び出せませんでした。 クエリ スキーマがテーブル スキーマと一致しません。     |永続的           |
|UpdatePolicy_FailedDescendantTransaction          |更新ポリシーを呼び出せませんでした。 子孫のトランザクション更新ポリシーが失敗しました。    |一時的           |
|UpdatePolicy_IngestionError                       |更新ポリシーを呼び出せませんでした。 インジェスト エラーが発生しました。<br>このエラーは、更新ポリシーのソース テーブルで報告されます。     |一時的          |
|UpdatePolicy_UnknownError                         |更新ポリシーを呼び出せませんでした。 不明なエラーが発生しました。<br>このエラーは、更新ポリシーのターゲット テーブルで報告されます。    |一時的           |
|UpdatePolicy_Cyclic_Update_Not_Allowed         |更新ポリシーを呼び出せませんでした。 循環更新は許可されていません。      |永続的           |

## <a name="category-useraccessnotauthorized"></a>カテゴリ:UserAccessNotAuthorized

|エラー メッセージ                                 |説明                                           |永続的/一時的|
|---|---|---|
|BadRequest_InvalidKustoIdentityToken              |Kusto ID トークンが無効です。                           |永続的           |
|Forbidden                                          |要求を実行するための十分なセキュリティ アクセス許可がありません。 | 永続的|

## <a name="category-unknown"></a>カテゴリ:Unknown

|エラー メッセージ                                 |説明                                           |永続的/一時的|
|---|---|---|
|不明                                            |不明なエラーが発生しました。                             |一時的          |
