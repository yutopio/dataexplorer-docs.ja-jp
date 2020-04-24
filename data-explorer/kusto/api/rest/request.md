---
title: クエリ/管理 HTTP 要求 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクエリ/管理 HTTP 要求について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 71fac7122ca51755beaa09ce3867143806e4f2b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502722"
---
# <a name="querymanagement-http-request"></a>クエリ/管理の HTTP 要求

## <a name="request-verb-and-resource"></a>要求動詞とリソース

|アクション    |HTTP 動詞|HTTP リソース   |
|----------|---------|----------------|
|クエリ     |GET      |`/v1/rest/query`|
|クエリ     |POST     |`/v1/rest/query`|
|クエリ v2  |GET      |`/v2/rest/query`|
|クエリ v2  |POST     |`/v2/rest/query`|
|管理|POST     |`/v1/rest/mgmt` |

たとえば、コントロール コマンド (「管理」) をサービス エンドポイントに送信するには、次の要求行を使用します。

```
POST https://help.kusto.windows.net/v1/rest/mgmt HTTP/1.1
```

(含める要求ヘッダーと本文については、以下を参照してください。

## <a name="request-headers"></a>要求ヘッダー

次の表に、クエリ操作と管理操作を実行するために使用される共通ヘッダーを示します。

|標準ヘッダー  |説明                                                                                                                    |
|-----------------|-------------------------------------------------------------------------------------------------------------------------------|
|`Accept`         |**必須**。 `application/json`に設定します。                                                                                  |
|`Accept-Encoding`|**オプション**。 サポートされているエンコーディングは`gzip``deflate`と です。                                                                    |
|`Authorization`  |**必須**。 [認証](./authentication.md)を参照してください。                                                                       |
|`Connection`     |**オプション**。 有効にすることをお`Keep-Alive`勧めします。                                                                  |
|`Content-Length` |**オプション**。 要求本文の長さは、既知の場合に指定することをお勧めします。                                          |
|`Content-Type`   |**必須**。 に`application/json`設定`charset=utf-8`します。                                                             |
|`Expect`         |**オプション**。 に設定できます`100-Continue`。                                                                                    |
|`Host`           |**必須**。 この値を、要求の送信先の完全修飾ドメイン名 (など`help.kusto.windows.net`) に設定します。|

次の表に、クエリ操作および管理操作を実行するときに使用する一般的なカスタム ヘッダーを示します。 特に指定がない限り、これらのヘッダーはテレメトリの目的でのみ使用され、機能に影響はありません。

すべてのヘッダーは**オプションです**。 カスタム ヘッダーを指定**することを強くお勧めします。** `x-ms-client-request-id` 一部のシナリオでは (たとえば、実行中のクエリをキャンセルする)、このヘッダーは要求を識別するために使用されるため**必須**です。


|カスタム ヘッダー           |説明                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |要求を行うアプリケーションの (フレンドリ) 名。                                                |
|`x-ms-user`             |要求を行うユーザーの (フレンドリ) 名。                                                       |
|`x-ms-user-id`          |`x-ms-user` と同じ。                                                                                      |
|`x-ms-client-request-id`|要求の一意の識別子。                                                                      |
|`x-ms-client-version`   |要求を行うクライアントの (フレンドリな) バージョン識別子。                                      |
|`x-ms-readonly`         |指定した場合、要求は強制的に読み取り専用モードで実行されます (長時間にわたる変更を行えないようにします)。|

## <a name="request-parameters"></a>要求パラメーター

要求では、次のパラメーターを渡すことができます。 これらは、GET または POST のどちらが使用されているかに応じて、クエリ パラメーターまたは本文の一部として要求でエンコードされます。

* `csl`: これは**必須**パラメータです。 実行するクエリまたはコントロール コマンドのテキストを保持します。

* `db`: これは、一部の制御コマンドの**オプション**パラメータであり、他の制御コマンドおよびすべてのクエリの**必須**パラメータです。 クエリまたは制御コマンドのターゲットであるデータベースである"スコープ内のデータベース"の名前を提供します。

* `properties`: これは**オプション**のパラメータです。 このクラスには、要求の処理方法とその結果を変更するさまざまなクライアント要求プロパティが用意されています。 詳しくは、[クライアント要求のプロパティ](../netfx/request-properties.md)を参照してください。

## <a name="get-query-parameters"></a>GET クエリ パラメーター

GET を使用する場合、要求のクエリ パラメーターは、上記の要求パラメーターを指定します。

## <a name="body"></a>Body

POST を使用する場合、要求の本文は UTF-8 でエンコードされた単一の JSON ドキュメントで、上記のリクエストパラメーターの値を提供します。

## <a name="examples"></a>例

次の例は、クエリの HTTP POST 要求を示しています。

```txt
POST https://help.kusto.windows.net/v2/rest/query HTTP/1.1
```

要求ヘッダー:

```txt
Accept: application/json
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Content-Type: application/json; charset=utf-8
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1
x-ms-user-id: EARTH\davidbg
x-ms-app: MyApp
```

要求本文 (明確にするために導入された改行、必要ありません):

```json
{
  "db":"Samples",
  "csl":"print Test=\"Hello, World!\"",
  "properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"},\"Parameters\":{},\"ClientRequestId\":\"MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1\"}"
}
```

次の例は[、curl](https://curl.haxx.se/)を使用して上記のクエリを送信する要求を作成する方法を示しています。

1. 認証用のトークンを取得します。

* `AAD_TENANT_NAME_OR_ID` [AAD アプリケーション認証](../../management/access-control/how-to-provision-aad-app.md)を設定した後で、 を`AAD_APPLICATION_ID`関連する値`AAD_APPLICATION_KEY`で置き換えます。

```
curl "https://login.microsoftonline.com/AAD_TENANT_NAME_OR_ID/oauth2/token" \
  -F "grant_type=client_credentials" \
  -F "resource=https://help.kusto.windows.net" \
  -F "client_id=AAD_APPLICATION_ID" \
  -F "client_secret=AAD_APPLICATION_KEY"
```

これにより、ベアラー トークンが提供されます。

```
{
  "token_type": "Bearer",
  "expires_in": "3599",
  "ext_expires_in":"3599", 
  "expires_on":"1578439805",
  "not_before":"1578435905",
  "resource":"https://help.kusto.windows.net",
  "access_token":"eyJ0...uXOQ"
}
```

2. クエリ エンドポイントへの要求でベアラー トークンを使用します。

```
curl -d '{"db":"Samples","csl":"print Test=\"Hello, World!\"","properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"}}"}"' \
-H "Accept: application/json" \
-H "Authorization: Bearer eyJ0...uXOQ" \
-H "Content-Type: application/json; charset=utf-8" \
-H "Host: help.kusto.windows.net" \
-H "x-ms-client-request-id: MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1" \
-H "x-ms-user-id: EARTH\davidbg" \
-H "x-ms-app: MyApp" \
-X POST https://help.kusto.windows.net/v2/rest/query
```

3. [この仕様](response.md)に従って応答を読みます。