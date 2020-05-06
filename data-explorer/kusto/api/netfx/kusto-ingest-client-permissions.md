---
title: Kusto. インジェスト参照-インジェストアクセス許可-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Kusto. インジェストの参照-インジェストアクセス許可について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e60eb6642a66fac81ce373f8f4d62de4f7217a91
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799698"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Kusto. インジェスト参照-インジェストアクセス許可

この記事では、インジェストを機能させるため`Native`にサービスに設定するアクセス許可について説明します。

## <a name="prerequisites"></a>必須コンポーネント

* Kusto サービスとデータベースの承認設定を表示および変更するには、「 [kusto control コマンド](../../management/security-roles.md)」を参照してください。

## <a name="references"></a>References

* 次の例では、サンプルのプリンシパルとして使用されるアプリケーションを Azure AD します。
    * テスト AAD アプリ (2a904276-1234-5678-9012-66fc53add60b; microsoft.com)
    * Kusto 内部インジェスト AAD アプリ (76263cdb-1234-5678-9012-545644e9c404; microsoft.com)

## <a name="ingestion-permission-mode-for-queued-ingestion"></a>キューに置かれたインジェストのインジェストアクセス許可モード

インジェストアクセス許可モードは、 [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)で定義されています。 このモードでは、Azure データエクスプローラーサービスに対するクライアントコードの依存関係が制限されます。 インジェストは、Azure キューに Kusto インジェストメッセージを投稿することによって行われます。 このキュー (インジェストサービスとも呼ばれます) は、Azure データエクスプローラーサービスから得られます。 中間ストレージアーティファクトは、Azure データエクスプローラーサービスによって割り当てられたリソースを使用して、インジェストクライアントによって作成されます。

この図は、キューに置かれたインジェストクライアントと Kusto のやり取りの概要を示しています。

:::image type="content" source="../images/queued-ingest.jpg" alt-text="キューに登録済み-取り込み":::

### <a name="permissions-on-the-engine-service"></a>エンジンサービスに対する権限

データベース`T1` `DB1`のテーブルへのデータの取り込みを修飾するには、取り込み操作を実行するプリンシパルが承認を持っている必要があります。
最低限必要な権限レベル`Database Ingestor`は`Table Ingestor`で、データベース内の既存のすべてのテーブルまたは特定の既存のテーブルにデータを取り込むことができます。
テーブルの作成が必要な`Database User`場合、または高いアクセスロールも割り当てる必要があります。


|Role                 |PrincipalType        |PrincipalDisplayName
|---------------------|---------------------|------------
|`Database Ingestor`  |Azure AD アプリケーション |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`
|`Table Ingestor`     |Azure AD アプリケーション |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`Kusto 内部インジェストアプリであるプリンシパルは、 `Cluster Admin`ロールに immutably マップされています。 そのため、任意のテーブルにデータを取り込むことが許可されます。 これは、Kusto で管理されるインジェストパイプラインで行われています。

データベース`DB1`またはテーブル`T1`に対して必要な`Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)`権限を Azure AD アプリに付与することは、次のようになります。

```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```
