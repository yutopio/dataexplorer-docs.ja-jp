---
title: Azure Data Explorer でクロステナント クエリとコマンドを許可する
description: Azure Data Explorer で複数のテナントからのクエリまたはコマンドを許可する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: reference
ms.date: 01/06/2021
ms.openlocfilehash: e675fd0a3a50fa1959e4fb3a6a660546adcb4a36
ms.sourcegitcommit: ec290d02974b4bb28e44c8c4a981fb32b1f945a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98124406"
---
# <a name="allow-cross-tenant-queries-and-commands"></a>クロステナント クエリとコマンドを許可する 

複数のテナントから、1 つの Azure Data Explorer クラスターでクエリとコマンドを実行できます。 この記事では、別のテナントのプリンシパルにクラスターのアクセス権を付与する方法について説明します。

クラスター所有者は、他のテナントからのクエリおよびコマンドから自分のクラスターを保護できます。 これは、`TrustedExternalTenants` クラスター プロパティを管理することで行います。 `trustedExternalTenants` プロパティは、クラスターでクエリとコマンドを実行できるテナントを明示的に定義します。 詳細については、[Azure Data Explorer クラスターの要求本文](/rest/api/azurerekusto/clusters/createorupdate#request-body)に関するページを参照してください。

> [!NOTE]
> クエリまたはコマンドを実行するプリンシパルには、関連するデータベース ロールも必要です。 詳細については、[ロール ベースの認可](./kusto/management/access-control/role-based-authorization.md)に関する記事を参照してください。 正しいロールの検証は、信頼された外部テナントの検証後に行われます。

## <a name="syntax"></a>構文

**特定のテナントを定義する**

`trustedExternalTenants``: [ {"`*value*`": "`*tenantId1*`" }, { "`*value*`": "`*tenantId2*`" }, ... ]`

**すべてのテナントを許可する**

trustedExternalTenants 配列は、すべてのテナントを示すスター ('*') 表記もサポートしています。これにより、すべてのテナントからのクエリとコマンドが許可されます。 

`trustedExternalTenants``: [ { "`*value*`": "`*`" }]`

> [!NOTE]
> `trustedExternalTenants` の既定値は、すべてのテナント (`[ { "value": "*" }]`) です。 クラスターの作成時に外部テナント配列が定義されなかった場合、クラスターの更新操作でこれを上書きできます。 空の配列は受け入れられません。

## <a name="example"></a>例

次の操作を使用してクラスターを更新します。

```http
PATCH https://management.azure.com/subscriptions/12345678-1234-1234-1234-123456789098/resourceGroups/kustorgtest/providers/Microsoft.Kusto/clusters/kustoclustertest?api-version=2020-09-18
```

### <a name="request-body-specific-tenants"></a>要求本文の特定テナント

```json
{
              "properties": { 
                           "trustedExternalTenants": [
                                         { "value": "tenantId1" }, 
                                         { "value": "tenantId2" }, 
                                         ...
                           ]
              }
}
```

### <a name="request-body-all-tenants"></a>要求本文のすべてのテナント

```json
{
              "properties": { 
                           "trustedExternalTenants": [  { "value": "*" }  ]
              }
}

```
