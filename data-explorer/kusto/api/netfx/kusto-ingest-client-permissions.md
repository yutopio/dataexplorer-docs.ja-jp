---
title: Kusto.Ingest リファレンス - インジェストのアクセス許可 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの Kusto.Ingest リファレンス - インジェストのアクセス許可について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 198d562f880b74f6df4c6318f72213ee865f8f28
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502892"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Kusto.Ingest リファレンス - インジェスト権限
この記事では、`Native`インジェストを機能させるために、サービスに対してどのようなアクセス許可を設定する必要があるかを説明します。



## <a name="prerequisites"></a>前提条件
* この資料では[、Kusto コントロール コマンド](../../management/security-roles.md)を使用して Kusto サービスおよびデータベースの承認設定を表示および変更する方法を説明します。
* 次の AAD アプリケーションは、次の例ではサンプル プリンシパルとして使用されます。
    * テスト AAD アプリ (2a904276-1234-5678-9012-66fc53add60b;microsoft.com)
    * クストー内部インジェストAADアプリ(76263cdb-1234-5678-9012-545644e9c404;microsoft.com)

## <a name="ingestion-permission-model-for-queued-ingestion"></a>キューイング インジェスティの取り込みアクセス許可モデル
で定義[されている IKustoQueueingest クライアント](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)このモードは、Kusto サービスに対するクライアント コードの依存関係を制限します。 インジェストは、Kusto インジェスト メッセージを Azure キューにポストして実行され、そのキューは Kusto データ管理 (別名. インジェスティオン)サービス。 すべての中間ストレージ成果物は、Kusto データ管理サービスによって割り当てられたリソースを使用して、INGEST クライアントによって作成されます。<BR>

次の図は、キューイングインジェスト クライアントと Kusto との対話の概要を示しています。<BR>

![alt text](../images/queued-ingest.jpg "キューイングインジェスト")

### <a name="permissions-on-the-engine-service"></a>エンジン サービスのアクセス許可
データベース`T1``DB1`上のテーブルへのデータ取り込みの資格を得るには、インジェスト操作を実行するプリンシパルを承認する必要があります。
必要最小限のアクセス許可`Database Ingestor`レベル`Table Ingestor`は、データベース内のすべての既存のテーブルまたは特定の既存のテーブルにデータを取り込むことができます。
テーブルの作成が必要な`Database User`場合、またはより高いアクセスロールも割り当てる必要があります。


|Role |プリンシパルタイプ    |プリンシパル表示名
|--------|------------|------------
|データベース *** インジェスター |AAD アプリケーション |テストアプリ(アプリID:2a904276-1234-5678-9012-66fc53add60b)
|テーブル *** インジェスター |AAD アプリケーション |テストアプリ(アプリID:2a904276-1234-5678-9012-66fc53add60b)

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`プリンシパル (Kusto 内部インジェストアプリ) は、`Cluster Admin`ロールに不変にマップされているため、任意のテーブルにデータを取り込む権限があります (Kusto 管理のインジェストパイプラインで起こっていることです)。

AAD App`DB1``Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)`にデータベースまたは`T1`テーブルに必要な権限を付与すると、次のようになります。
```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```

