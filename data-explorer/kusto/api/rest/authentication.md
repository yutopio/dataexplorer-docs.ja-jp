---
title: HTTPS 経由の認証-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの HTTPS 経由の認証について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: e0910089d87d6bce6124cb7e4560c2fa7b92b847
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617988"
---
# <a name="authentication-over-https"></a>HTTPS 経由の認証

HTTPS を使用する場合、サービスは認証を`Authorization`実行するための標準 HTTP ヘッダーをサポートします。

サポートされている HTTP 認証方法は次のとおりです。

* **Azure Active Directory**メソッドを使用し`bearer`て Azure Active Directory します。

Azure AD を使用して認証`Authorization`する場合、ヘッダーの形式は次のようになります。

```txt
Authorization: bearer TOKEN
```

ここ`TOKEN`で、は、呼び出し元が Azure AD サービスと通信することによって取得するアクセストークンです。 このトークンには、次のプロパティがあります。

* リソースはサービス URI です (例: `https://help.kusto.windows.net`)。
* Azure AD サービスエンドポイントは、`https://login.microsoftonline.com/TENANT/`

ここ`TENANT`で、は Azure AD のテナント ID または名前です。 たとえば、Microsoft テナントで作成されたサービスでは、 `https://login.microsoftonline.com/microsoft.com/`を使用できます。 または、ユーザー認証のみの場合は、に`https://login.microsoftonline.com/common/`対して要求を行うことができます。

> [!NOTE]
> Azure AD サービスエンドポイントは、national クラウドで実行されるときに変更されます。
> エンドポイントを変更するには、必要`AadAuthorityUri`な URI に環境変数を設定します。

詳細にについては、[認証の概要](../../management/access-control/index.md)と[Azure AD 認証のガイドを](../../management/access-control/how-to-authenticate-with-aad.md)参照してください。
