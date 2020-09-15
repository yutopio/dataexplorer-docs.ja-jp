---
title: cosmosdb_sql_request プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの cosmosdb_sql_request プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 09/11/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 035705e0a15ab686af16b9e8b2a00268db21d6a2
ms.sourcegitcommit: c140bc3bc984f861df0b85e672d2e685e6659a54
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2020
ms.locfileid: "90109989"
---
# <a name="cosmosdb_sql_request-plugin"></a>cosmosdb_sql_request プラグイン

::: zone pivot="azuredataexplorer"

この `cosmosdb_sql_request` プラグインは COSMOS DB sql ネットワークエンドポイントに sql クエリを送信し、クエリの結果を返します。 このプラグインは、主に小さなデータセットのクエリを実行するために設計されています。たとえば、 [Azure Cosmos DB](/azure/cosmos-db/)に格納されている参照データを使用してデータを強化できます

## <a name="syntax"></a>構文

`evaluate``cosmosdb_sql_request` `(` *ConnectionString* `,` *sqlquery* [ `,` *sqlquery* [ `,` *オプション*]]`)`

## <a name="arguments"></a>引数

|引数名 | [説明] | 必須/省略可能 | 
|---|---|---|
| *ConnectionString* | `string`照会する Cosmos DB コレクションを指す接続文字列を示すリテラル。 *Accountendpoint*、*データベース*、および*コレクション*が含まれている必要があります。 認証にマスターキーが使用されている場合は、 *AccountKey* が含まれることがあります。 <br> **例:** `'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/ ;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;'`| 必須 |
| *SqlQuery*| `string`実行するクエリを示すリテラル。 | 必須 |
| *SqlParameters* | `dynamic`クエリと共にパラメーターとして渡すキーと値のペアを保持する型の定数値。 パラメーター名はで始まる必要があり `@` ます。 | Optional |
| *[オプション]* | `dynamic`キーと値のペアとしてより高度な設定を保持する型の定数値。 | Optional |
|| ----*サポートされているオプションの設定は次のとおりです。*-----
|      `armResourceId` | Azure Resource Manager から API キーを取得する <br> **例:** `/subscriptions/a0cd6542-7eaf-43d2-bbdd-b678a869aad1/resourceGroups/ cosmoddbresourcegrouput/providers/Microsoft.DocumentDb/databaseAccounts/cosmosdbacc`| 
|  `token` | Azure Resource Manager での認証に使用される Azure AD アクセストークンを指定します。
| `preferredLocations` | データを照会するリージョンを制御します。 <br> **例:** `['East US']` | |  

## <a name="set-callout-policy"></a>コールアウトポリシーの設定

プラグインは、Cosmos DB にコールアウトを行います。 クラスターの [コールアウトポリシー](../management/calloutpolicy.md) で `cosmosdb` 、ターゲット *CosmosDbUri*への型の呼び出しが有効になっていることを確認します。

次の例は、Cosmos DB のコールアウトポリシーを定義する方法を示しています。 特定のエンドポイント (、) に制限することをお勧め `my_endpoint1` `my_endpoint2` します。

```kusto
[
  {
    "CalloutType": "CosmosDB",
    "CalloutUriRegex": "my_endpoint1.documents.azure.com",
    "CanCall": true
  },
  {
    "CalloutType": "CosmosDB",
    "CalloutUriRegex": "my_endpoint2.documents.azure.com",
    "CanCall": true
  }
]
```

次の例は、CalloutType に対する alter 吹き出し policy コマンドを示しています。 `cosmosdb` *CalloutType*

```kusto
.alter cluster policy callout @'[{"CalloutType": "cosmosdb", "CalloutUriRegex": "\\.documents\\.azure\\.com", "CanCall": true}]'
```

## <a name="examples"></a>例

### <a name="query-cosmos-db"></a>クエリ Cosmos DB

次の例では、 *cosmosdb_sql_request* プラグインを使用して sql クエリを送信し、sql API を使用して Cosmos DB からデータをフェッチします。

```kusto
evaluate cosmosdb_sql_request(
  'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;',
  'SELECT * from c')
```

### <a name="query-cosmos-db-with-parameters"></a>パラメーターを使用した Cosmos DB のクエリ

次の例では、SQL クエリパラメーターを使用して、代替リージョンからデータを照会します。 詳細については、「[`preferredLocations`](/azure/cosmos-db/tutorial-global-distribution-sql-api?tabs=dotnetv2%2Capi-async#preferred-locations)」を参照してください。

```kusto
evaluate cosmosdb_sql_request(
    'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;',
    "SELECT c.id, c.lastName, @param0 as Column0 FROM c WHERE c.dob >= '1970-01-01T00:00:00Z'",
    dynamic({'@param0': datetime(2019-04-16 16:47:26.7423305)}),
    dynamic({'preferredLocations': ['East US']}))
| where lastName == 'Smith'
```

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
