---
title: インジェストエラー-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのインジェストエラーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: a7a2dcaea2ef982edc8286b83a042d2f21460986
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610442"
---
# <a name="ingestion-failures"></a>インジェスト エラー

## <a name="show-ingestion-failures"></a>。インジェストエラーを表示します


このコマンドは、 [データインジェスト制御コマンド](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands) の実行時に発生したインジェストエラーを含む結果セットを返します。


> [!NOTE]
> インジェストフローの他の部分で発生したインジェストエラーは、このコマンドの結果セットには表示されません。 たとえば、データインジェスト制御コマンドが Kusto データエンジンサービスに送信される前に、このようなエラーが発生することがあります。

> [キューに置か](../api/netfx/about-kusto-ingest.md#queued-ingestion)れたインジェストに関連するフローで発生した障害の監視の詳細については、こちらの[ガイド](../api/netfx/kusto-ingest-client-status.md)を参照してください。

**構文**

|構文オプション|説明|
|---|---| 
|`.show` `ingestion` `failures`                                       |記録されたインジェストエラーをすべて返します  
|`.show` `ingestion` `failures` <code>&#124;</code> `where` ...       |フィルター処理されたインジェストエラーのセットを返します
|`.show``ingestion` `failures` `with(OperationId="`*OperationId*`")` |特定の操作 ID のインジェストエラーを返します

**結果**
 
|出力パラメーター           |Type     |説明                                                                              |
|---------------------------|---------|-----------------------------------------------------------------------------------------|
|OperationId                |String   |を介して追加の操作の詳細を表示するために使用できる操作識別子 <br> [. 操作の表示](operations.md) コマンド </br> 
|データベース                   |String   |障害が発生したデータベース
|テーブル                      |String   |エラーが発生したテーブル
|失敗した場合                   |DateTime |エラーが登録された日付/時刻 (UTC) 
|IngestionSourcePath        |String   |インジェストソース (通常は Azure Blob URI) を識別します 
|詳細                    |String   |エラーの詳細。 実際のインジェストエラーの根本原因についての洞察を提供します
|FailureKind                |String   |エラーの種類 (永続的/一時的)
|RootActivityId             |String   |ルートアクティビティ ID。
|OperationKind              |String   |エラーが登録されたときのインジェスト操作の種類 (フェーズ)
|発信ポリシー |ブール型 | [更新ポリシー](update-policy.md)の実行中にエラーが登録されたかどうかを示します
 
**例**
 
|OperationId |データベース |テーブル |失敗した場合 |IngestionSourcePath |詳細 |FailureKind |RootActivityId |OperationKind |発信ポリシー
|--|--|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |DB1 |Table1 |2017-02-14 22:25: 03.1147331 |...url... |ID ' * * * * * .csv ' のストリームの CSV 形式が正しくありません * |永続的 |3c883942-e446-4999-9b00-d4c664f06ef6 |DataIngestPull | 0
|84 1fafa4076a47 cba93008-4836da9c75 |DB1 |Table1 |2017-02-14 22:34: 11.2565943 |...url... |ID ' * * * * * .csv ' のストリームの CSV 形式が正しくありません * |永続的 |48571bdb-b714-4f32-8ddc-4001838a956c |DataIngestPull | 0
|e198c519-5263-4629-a158-8d68f7a1022f |DB1 |Table1 |2017-02-14 22:34: 44.5824741 |...url... |ID ' * * * * * .csv ' のストリームの CSV 形式が正しくありません * |永続的 |5e31ab3c-e2c7-489a-827e-e89d2d691ec4 |DataIngestPull | 0
|a9f287a1-f3e6-4154-ad18-b86438da0929 |DB1 |Table1 |2017-02-14 22:36: 26.5525250 |...url... |不明なエラーが発生しました: 型 ' system.exception ' の例外がスローされました |一時的 |9b7bb01747 71e48 f6-9c96、d16fcf938d2a |DataIngestPull | 0
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DB1 |Table1 |2017-02-14 23:52: 31.5460071 |...url... |Blob をダウンロードできませんでした: クライアントは指定されたタイムアウト時間内に操作を完了できませんでした |永続的 |21fa0dd6-cd7d-4493-b6f7-78916ce0d617 |DataIngestPull | 0
