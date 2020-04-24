---
title: ストリーミング インジェスト HTTP 要求 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのストリーミング インジェスト HTTP 要求について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: d14987806bbc62dbc79112700bd5b88aaa6676c3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503011"
---
# <a name="streaming-ingestion-http-request"></a>ストリーミングインジェスト HTTP 要求

## <a name="request-verb-and-resource"></a>要求動詞とリソース

|アクション    |HTTP 動詞|HTTP リソース                                               |
|----------|---------|------------------------------------------------------------|
|取り込み    |POST     |`/v1/rest/ingest/{database}/{table}?{additional parameters}`|

## <a name="request-parameters"></a>要求パラメーター

| パラメーター    |  説明                                                                                                |
|--------------|-------------------------------------------------------------------------------------------------------------|
| `{database}` | **必須**取り込み要求のターゲット データベースの名前                                          |
| `{table}`    | **必須**取り込み要求のターゲット表の名前                                             |

## <a name="additional-parameters"></a>追加パラメーター
追加のパラメータは URL クエリとして書式設定されます`{name}`=`{value}`: &文字で区切られたペア


| パラメーター    |  説明                                                                                                |
|--------------|-------------------------------------------------------------------------------------------------------------|
|`streamFormat`| **必須**要求本文のデータの形式を指定します。 値は、次のいずれかである必要があります`Csv`。`Tsv``Scsv``SOHsv``Psv``Json``SingleJson``MultiJson``Avro` 詳細については、「[サポートされているデータ形式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)」を参照してください。|
|`mappingName` | 、、`streamFormat``SingleJson`または`MultiJson``Json``Avro`、、省略**可能**な場合は**必須**です。 値は、テーブルに定義された事前に作成されたインジェスション マッピングの名前にする必要があります。 データ マッピングの詳細については、「[データ マッピング](../../management/mappings.md)」を参照してください。 テーブルで事前に作成されたマッピングを管理する方法については、以下のページで説明[します。](../../management/create-ingestion-mapping-command.md) |
              

たとえば、データベースのテーブル`Logs`に CSV 形式のデータを取`Test`り込むには、次の要求行を使用します。

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Csv HTTP/1.1
```

事前に作成されたマッピングを使用して JSON 形式のデータを取り込む`mylogmapping`

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```


(含める要求ヘッダーと本文については、以下を参照してください。

## <a name="request-headers"></a>要求ヘッダー

次の表に、クエリ操作と管理操作を実行するために使用される共通ヘッダーを示します。

|標準ヘッダー  |説明                                                                                                              |
|------------------|------------------------------------------------------------------------------------------------------------------------|
|`Accept`          |**オプション**。 `application/json`に設定します。                                                                           |
|`Accept-Encoding` |**オプション**。 サポートされているエンコーディングは`gzip``deflate`と です。                                                             |
|`Authorization`   |**必須**。 [認証](./authentication.md)を参照してください。                                                                |
|`Connection`      |**オプション**。 有効にすることをお`Keep-Alive`勧めします。                                                           |
|`Content-Length`  |**オプション**。 要求本文の長さは、既知の場合に指定することをお勧めします。                                   |
|`Content-Encoding`|**オプション**。 その場合`gzip`、本文はgzip圧縮が必要とされるように設定できます                                 |
|`Expect`          |**オプション**。 に設定できます`100-Continue`。                                                                             |
|`Host`            |**必須**。 この値は、要求の送信先の完全修飾ドメイン名 (例:`help.kusto.windows.net`など) に設定します。|

次の表に、クエリ操作および管理操作を実行するときに使用する一般的なカスタム ヘッダーを示します。 特に指定がない限り、これらのヘッダーはテレメトリの目的でのみ使用され、機能に影響はありません。

すべてのヘッダーは**オプションです**。 ただし、カスタム ヘッダーを`x-ms-client-request-id`指定**することを強くお勧めします**。 一部のシナリオでは (たとえば、実行中のクエリをキャンセルする)、このヘッダーは要求を識別するために使用される**ので必須**です。


|カスタム ヘッダー           |説明                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |要求を行うアプリケーションの (フレンドリ) 名。                                                |
|`x-ms-user`             |要求を行うユーザーの (フレンドリ) 名。                                                       |
|`x-ms-user-id`          |`x-ms-user` と同じ。                                                                                      |
|`x-ms-client-request-id`|要求の一意の識別子。                                                                      |
|`x-ms-client-version`   |要求を行うクライアントの (フレンドリな) バージョン識別子。                                      |

## <a name="body"></a>Body

本文は、取り込まれる実際のデータです。 テキスト形式は UTF-8 エンコーディングを使用します。

## <a name="examples"></a>例

次の例は、JSON コンテンツを取り込む HTTP POST 要求を示しています。

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

要求ヘッダー:

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 161
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

要求本文:

```txt
{"Timestamp":"2018-11-14 11:34","Level":"Info","EventText":"Nothing Happened"}
{"Timestamp":"2018-11-14 11:35","Level":"Error","EventText":"Something Happened"}
```

次の例は、圧縮された同じデータを取り込む HTTP POST 要求を示しています。

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

要求ヘッダー:

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 116
Content-Encoding: gzip
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

要求本文:

```
... binary data ...
```