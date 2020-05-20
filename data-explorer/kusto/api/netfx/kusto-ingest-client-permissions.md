---
title: Kusto. インジェストアクセス許可-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Kusto インジェストのアクセス許可について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2e88ba9af0b9563274e15eff8d1c1f6e997fb45c
ms.sourcegitcommit: e66c5f4b833b4f6269bb7bfa5695519fcb11d9fa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83630007"
---
# <a name="kustoingest---ingestion-permissions"></a>Kusto. インジェスト-インジェストのアクセス許可 

この記事では、インジェストを機能させるためにサービスに設定するアクセス許可について説明し `Native` ます。

## <a name="prerequisites"></a>前提条件
 
* Kusto サービスとデータベースの承認設定を表示および変更するには、「 [kusto control コマンド](../../management/security-roles.md)」を参照してください。

* 次の例では、Azure Active Directory (Azure AD) アプリケーションをサンプルプリンシパルとして使用します。
    * テスト Azure AD アプリ (2a904276-1234-5678-9012-66fc53add60b; microsoft.com)
    * Kusto 内部インジェスト Azure AD アプリ (76263cdb-1234-5678-9012-545644e9c404; microsoft.com)
 
## <a name="ingestion-permission-mode-for-queued-ingestion"></a>キューに置かれたインジェストのインジェストアクセス許可モード

インジェストアクセス許可モードは、 [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)で定義されています。 このモードでは、Azure データエクスプローラーサービスに対するクライアントコードの依存関係が制限されます。 インジェストは、Azure キューに Kusto インジェストメッセージを投稿することによって行われます。 このキュー (インジェストサービスとも呼ばれます) は、Azure データエクスプローラーサービスから得られます。 中間ストレージアーティファクトは、Azure データエクスプローラーサービスによって割り当てられたリソースを使用して、インジェストクライアントによって作成されます。

この図は、キューに置かれたインジェストクライアントと Kusto のやり取りの概要を示しています。

:::image type="content" source="../images/kusto-ingest-client-permissions/queued-ingest.png" alt-text="キューによるインジェスト":::

### <a name="permissions-on-the-engine-service"></a>エンジンサービスに対する権限

データベースのテーブルへのデータの取り込みを修飾するには `T1` `DB1` 、取り込み操作を実行するプリンシパルが承認を持っている必要があります。
最低限必要な権限レベルはで `Database Ingestor` 、 `Table Ingestor` データベース内の既存のすべてのテーブルまたは特定の既存のテーブルにデータを取り込むことができます。
テーブルの作成が必要な場合、 `Database User` または高いアクセスロールも割り当てる必要があります。


|ロール                 |PrincipalType        |PrincipalDisplayName
|---------------------|---------------------|------------
|`Database Ingestor`  |Azure AD アプリケーション |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`
|`Table Ingestor`     |Azure AD アプリケーション |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`

>`Kusto Internal Ingestion Azure AD App (76263cdb-1234-5678-9012-545644e9c404)`Kusto 内部インジェストアプリであるプリンシパルは、ロールに immutably マップされてい `Cluster Admin` ます。 そのため、任意のテーブルにデータを取り込むことが許可されます。 これは、Kusto で管理されるインジェストパイプラインで行われています。

データベースまたはテーブルに対して必要な権限 `DB1` `T1` を Azure AD アプリに付与することは、次の `Test App (2a904276-1234-5678-9012-66fc53add60b in Azure AD tenant microsoft.com)` ようになります。

```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test Azure AD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test Azure AD App'
```
 