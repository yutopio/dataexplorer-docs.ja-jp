---
title: クエリ/管理 HTTP 要求-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのクエリ/管理 HTTP 要求について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: cf9f9e5f6a9c5afca58e2637ed4e639882e3749d
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337416"
---
# <a name="querymanagement-http-request"></a>クエリ/管理の HTTP 要求

## <a name="request-verb-and-resource"></a>動詞とリソースの要求

|アクション    |HTTP 動詞|HTTP リソース   |
|----------|---------|----------------|
|クエリ     |GET      |`/v1/rest/query`|
|クエリ     |POST     |`/v1/rest/query`|
|クエリ v2  |GET      |`/v2/rest/query`|
|クエリ v2  |POST     |`/v2/rest/query`|
|管理|POST     |`/v1/rest/mgmt` |

たとえば、制御コマンド ("管理") をサービスエンドポイントに送信するには、次の要求行を使用します。

```
POST https://help.kusto.windows.net/v1/rest/mgmt HTTP/1.1
```

含める要求ヘッダーと本文については、以下を参照してください。

## <a name="request-headers"></a>要求ヘッダー

次の表に、クエリおよび管理操作に使用される共通のヘッダーを示します。

|標準ヘッダー  |説明                                                                                 |必須/省略可能 |
|-----------------|--------------------------------------------------------------------------------------------|------------------|
|`Accept`         |`application/json`                                                                   |必須          |
|`Accept-Encoding`|サポートされているエンコーディングはとです。 `gzip``deflate`                                                |省略可能          |
|`Authorization`  |[認証](./authentication.md)を参照                                                   |必須          |
|`Connection`     |を有効にすることをお勧めします。 `Keep-Alive`                                                   |省略可能          |
|`Content-Length` |既知の場合は、要求本文の長さを指定することをお勧めします。                            |省略可能          |
|`Content-Type`   |`application/json`に設定`charset=utf-8`                                              |必須          |
|`Expect`         |をに設定できます。 `100-Continue`                                                                |省略可能          |
|`Host`           |要求が送信された修飾ドメイン名 (など) に設定します `help.kusto.windows.net` 。 |必須|

次の表に、クエリおよび管理操作に使用される共通のカスタムヘッダーを示します。 特に指定がない限り、これらのヘッダーはテレメトリの目的でのみ使用され、機能には影響しません。

すべてのヘッダーは省略可能です。 カスタムヘッダーを指定することをお勧めし `x-ms-client-request-id` ます。 実行中のクエリのキャンセルなど、一部のシナリオでは、要求の識別に使用されるため、このヘッダーが必要です。

|カスタムヘッダー           |[説明]                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |要求を行っているアプリケーションの (フレンドリ名) 名前                                                 |
|`x-ms-user`             |要求を行っているユーザーのフレンドリ名                                                        |
|`x-ms-user-id`          |`x-ms-user` と同じ                                                                                       |
|`x-ms-client-request-id`|要求の一意の識別子                                                                       |
|`x-ms-client-version`   |要求を行っているクライアントの (フレンドリな) バージョン識別子                                       |
|`x-ms-readonly`         |指定されている場合は、要求を読み取り専用モードで強制的に実行して、長時間にわたる変更を防ぐことができます。 |

## <a name="request-parameters"></a>要求パラメーター

要求では、次のパラメーターを渡すことができます。 これらは、GET または POST のどちらを使用するかに応じて、要求でクエリパラメーターまたは本文の一部としてエンコードされます。

|パラメーター   |説明                                                                                 |必須/省略可能 |
|------------|--------------------------------------------------------------------------------------------|------------------|
|`csl`       |実行するクエリまたはコントロールコマンドのテキスト                                             |必須          |
|`db`        |クエリまたはコントロールコマンドの対象であるスコープ内のデータベースの名前            |一部のコントロールコマンドでは省略可能。 <br>他のコマンドおよびすべてのクエリに必要です。 </br>                                                                   |
|`properties`|要求の処理方法と結果を変更するクライアント要求のプロパティを提供します。 詳細については、「[クライアント要求のプロパティ](../netfx/request-properties.md)」を参照してください。                                               | 省略可能         |

## <a name="get-query-parameters"></a>クエリパラメーターの取得

GET を使用する場合、要求のクエリパラメーターは要求パラメーターを指定します。

## <a name="body"></a>Body

POST を使用する場合、要求の本文は、要求パラメーターの値を使用して、UTF-8 でエンコードされた単一の JSON ドキュメントになります。

## <a name="examples"></a>例

次の例では、クエリの HTTP POST 要求を示します。

```txt
POST https://help.kusto.windows.net/v2/rest/query HTTP/1.1
```

要求ヘッダー

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

要求本文

```json
{
  "db":"Samples",
  "csl":"print Test=\"Hello, World!\"",
  "properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"},\"Parameters\":{},\"ClientRequestId\":\"MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1\"}"
}
```

この例では、 [curl](https://curl.haxx.se/)を使用して、上記のクエリを送信する要求を作成する方法を示します。

1. 認証用のトークンを取得します。

    `AAD_TENANT_NAME_OR_ID`、、 `AAD_APPLICATION_ID` およびを `AAD_APPLICATION_KEY` 関連する値に置き換えます ( [AAD アプリケーション認証](../../../provision-azure-ad-app.md)を設定した後)

    ```
    curl "https://login.microsoftonline.com/AAD_TENANT_NAME_OR_ID/oauth2/token" \
      -F "grant_type=client_credentials" \
      -F "resource=https://help.kusto.windows.net" \
      -F "client_id=AAD_APPLICATION_ID" \
      -F "client_secret=AAD_APPLICATION_KEY"
    ```

    このコードスニペットは、ベアラートークンを提供します。

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

1. クエリエンドポイントへの要求でベアラートークンを使用します。

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

1. [この仕様](response.md)に従って応答を読み取ります。