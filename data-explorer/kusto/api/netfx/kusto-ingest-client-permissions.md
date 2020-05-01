---
title: Kusto. インジェスト参照-インジェストアクセス許可-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの Kusto. インジェストの参照-インジェストアクセス許可について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ec3b91056a4cfc584c0d7168549864b2b50ef5ae
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617920"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Kusto. インジェスト参照-インジェストアクセス許可
この記事では、インジェストを機能させるため`Native`にサービスに設定するアクセス許可について説明します。


## <a name="prerequisites"></a>前提条件
* Kusto サービスとデータベースの承認設定を表示および変更するには、「 [kusto control コマンド](../../management/security-roles.md)」を参照してください。 

## <a name="references"></a>References
* 以下の例では、次の AAD アプリケーションがサンプルプリンシパルとして使用されています。
    * テスト AAD アプリ (2a904276-1234-5678-9012-66fc53add60b; microsoft.com)
    * Kusto 内部インジェスト AAD アプリ (76263cdb-1234-5678-9012-545644e9c404; microsoft.com)

## <a name="ingestion-permission-model-for-queued-ingestion"></a>キューに置かれたインジェストのインジェストアクセス許可モデル
[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)で定義されているこのモードは、Azure データエクスプローラーサービスのクライアントコードの依存関係を制限します。 インジェストは、Azure キューに Kusto インジェストメッセージを投稿することによって行われます。 キューは、Azure データエクスプローラーサービスから取得されます。 インジェストサービスとも呼ばれます。  すべての中間ストレージアーティファクトは、Azure データエクスプローラーサービスによって割り当てられたリソースを使用して、インジェストクライアントによって作成されます。

次の図は、キューに格納されたインジェストクライアントと Kusto のやり取りの概要を示しています。<BR>

:::image type="content" source="../images/queued-ingest.jpg" alt-text="キューに登録済み-取り込み":::

### <a name="permissions-on-the-engine-service"></a>エンジンサービスに対する権限
データベース`T1` `DB1`のテーブルへのデータの取り込みを修飾するには、取り込み操作を実行するプリンシパルが承認を持っている必要があります。
最低限必要な権限レベル`Database Ingestor`は`Table Ingestor`で、データベース内の既存のすべてのテーブルまたは特定の既存のテーブルにデータを取り込むことができます。
テーブルの作成が必要な`Database User`場合、または高いアクセスロールも割り当てる必要があります。


|Role |PrincipalType    |PrincipalDisplayName
|--------|------------|------------
|`Database Ingestor` |AAD アプリケーション |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`
|`Table Ingestor` |AAD アプリケーション |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`Kusto 内部インジェストアプリであるプリンシパルは immutably にマップ`Cluster Admin`され、その結果、任意のテーブルにデータを取り込むことができます。 これは、Kusto で管理されるインジェストパイプラインで行われています。

AAD アプリ`Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)`に対するデータベース`DB1`または`T1`テーブルに対する必要なアクセス許可の付与は次のようになります。
```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```
