---
title: Azure Data Explorer REST API - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の Azure Data Explorer REST API について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 000561f96bd4174b94cca8e9db4c932a3cf3f0c6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490686"
---
# <a name="azure-data-explorer-rest-api"></a>Azure Data Explorer REST API

この記事では、HTTPS 経由で Kusto を操作する方法について説明します。

## <a name="what-actions-are-supported"></a>サポートされているアクション

エンドポイントでサポートされるアクションの一覧は、エンドポイントがエンジン エンドポイントか、データ管理エンドポイントかによって変わります。

|アクション         |HTTP 動詞  |URI テンプレート             |エンジン|データ管理|認証?|
|---------------|-----------|-------------------------|------|---------------|---------------|
|クエリ          |GET または POST|`/v1/rest/query`         |はい   |いいえ             |はい            |
|クエリ          |GET または POST|`/v2/rest/query`         |はい   |いいえ             |はい            |
|管理     |POST       |`/v1/rest/mgmt`          |はい   |はい            |はい            |
|UI             |GET        |`/`                      |はい   |いいえ             |いいえ             |
|UI             |GET        |`/{dbname}`              |はい   |いいえ             |いいえ             |

**アクション**は、関連するアクティビティのグループを表します。

* **クエリ** アクションでは、サービスにクエリが送信され、クエリの結果を取得されます。
* **管理**アクションでは、管理コマンドがサービスに送信され、管理コマンドの結果が取得されます。
* **UI** アクションを使用すると、(HTTP リダイレクト応答を介して) デスクトップ クライアントまたは Web クライアントを起動してサービスと対話できます。

クエリと管理アクションの HTTP 要求/応答の詳細については、「[クエリ/管理の HTTP 要求](./request.md)」、「[クエリ/管理の HTTP 応答](./response.md)」、「[クエリ v2 の HTTP 応答](./response2.md)」を参照してください。 UI アクションの詳細については、「[UI (ディープ リンク)](./deeplink.md)」を参照してください。