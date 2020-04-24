---
title: 取り込みエラー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの取り込みエラーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 699fd01e9a284179bf2749b58581392d2170f0ca
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520946"
---
# <a name="ingestion-failures"></a>取り込み失敗

## <a name="show-ingestion-failures"></a>.表示インジェズションの失敗

このコマンドは、データ取り込み制御コマンドの実行中に発生したすべての取[り込](data-ingestion/index.md)み失敗を含む結果セットを返します。

*注*: 
- インジェスト フローの他の部分 (たとえば、データ取り込み制御コマンドが Kusto の Data Engine サービスに送信される前) に発生したインジェスト エラーは、このコマンドの結果セットに表示されません。
- [キューに入れ](../api/netfx/about-kusto-ingest.md#queued-ingestion)込まれるフローで発生する障害を監視する場合は、[このガイド](../api/netfx/kusto-ingest-client-status.md)に従うことをお勧めします。

**構文**

|||
|---|---| 
|`.show` `ingestion` `failures`                                       |記録されたすべてのインジェスの失敗を返します。  
|`.show` `ingestion` `failures` <code>&#124;</code> `where` ...       |フィルタ処理された一連のインジェスの失敗を返します。
|`.show``ingestion``failures`*OperationId*オペレーション`with(OperationId="`Id`")` |特定の操作 ID の取り込み失敗を返します。

**結果**
 
|出力パラメーター |Type |説明 
|---|---|---
|OperationId |String |操作識別子。 このパラメーターを使用して[、.show operation](operations.md)コマンドを実行して、追加の操作の詳細を表示できます。 
|データベース |String |障害が発生したデータベース
|テーブル |String |障害が発生したテーブル
|失敗した |DateTime |失敗が登録された日時 (UTC) 
|インジェスティションソースパス |String |取り込みソース (通常は、Azure BLOB URI) を識別します。 
|詳細 |String |失敗の詳細。 実際のインジェスト失敗の根本原因に関する洞察を提供します。
|失敗キンド |String |障害の種類 (永続/一時的)
|RootActivityId |String |ルート アクティビティ ID。
|操作種類 |String |失敗が登録されたインジェス操作タイプ (フェーズ)
|ソースの更新ポリシー |Boolean | [更新ポリシー](update-policy.md)の実行中に失敗が登録されたかどうかを示します。
 
**例**
 
|OperationId |データベース |テーブル |失敗した |インジェスティションソースパス |詳細 |失敗キンド |RootActivityId |操作種類 |ソースの更新ポリシー
|--|--|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |DB1 |Table1 |2017-02-14 22:25:03.1147331 |...Url。。。 |ID '*****.csv' のストリームの形式が正しくありません。 |永続的 |3c883942-e446-4999-9b00-d4c664f06ef6 |データインジングエストプル | 0
|841fafa4-076a-4cba-9300-4836da0d9c75 |DB1 |Table1 |2017-02-14 22:34:11.2565943 |...Url。。。 |ID '*****.csv' のストリームの形式が正しくありません。 |永続的 |48571bdb-b714-4f32-8ddc-4001838a956c |データインジングエストプル | 0
|e198c519-5263-4629-a158-8d68f7a1022f |DB1 |Table1 |2017-02-14 22:34:44.5824741 |...Url。。。 |ID '*****.csv' のストリームの形式が正しくありません。 |永続的 |5e31ab3c-e2c7-489a-827e-e89d2d691ec4 |データインジングエストプル | 0
|a9f287a1-f3e6-4154-ad18-b86438da0929 |DB1 |Table1 |2017-02-14 22:36:26.5525250 |...Url。。。 |不明なエラーが発生しました: 'System.Exception' 型の例外がスローされました |一時的 |9b7bb017-471e-48f6-9c96-d16fcf938d2a |データインジングエストプル | 0
|9edb3ecc-f4b4-4738-87e1-648eed2bd9988 |DB1 |Table1 |2017-02-14 23:52:31.5460071 |...Url。。。 |BLOB のダウンロードに失敗しました: クライアントは指定されたタイムアウト時間内に操作を完了できませんでした |永続的 |21fa0dd6-cd7d-4493-b6f7-78916ce0d617 |データインジングエストプル | 0