---
title: HTTPS 経由の認証 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで HTTPS 経由の認証について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 4b6fbf5bb34dc3ff52938c7042778a7e49fd5faf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503045"
---
# <a name="authentication-over-https"></a>HTTPS 経由の認証

HTTPS を使用する場合、サービスは`Authorization`認証を実行するための標準 HTTP ヘッダーをサポートします。

サポートされる HTTP 認証方法は次のとおりです。

* **メソッド**を使用して、Azure`bearer`アクティブ ディレクトリを使用します。

Azure アクティブ ディレクトリを使用して認証する`Authorization`場合、ヘッダーの形式は次のとおりです。

```txt
Authorization: bearer TOKEN
```

呼`TOKEN`び出し元が Azure Active Directory サービスと通信して取得するアクセス トークンは、次のプロパティを持つ場所です。

* リソースはサービス URI です (例: `https://help.kusto.windows.net`)。
* Azure アクティブ ディレクトリ サービス`https://login.microsoftonline.com/TENANT/`エンドポイントは です。

Azure`TENANT`アクティブ ディレクトリのテナント ID または名前は、どこに存在します。 たとえば、Microsoft テナントの下に作成されたサービス`https://login.microsoftonline.com/microsoft.com/`は、 を使用できます。 または、ユーザー認証の場合のみ、要求を代わりに行`https://login.microsoftonline.com/common/`うことができます。

> [!NOTE]
> Azure アクティブ ディレクトリ サービス エンドポイントは、ナショナル クラウドで実行すると変更されます。
> 使用するエンドポイントを変更するには、環境変数`AadAuthorityUri`を必要な URI に設定します。

詳細については、[認証の概要](../../management/access-control/index.md)と Azure Active [Directory 認証のガイドを](../../management/access-control/how-to-authenticate-with-aad.md)参照してください。