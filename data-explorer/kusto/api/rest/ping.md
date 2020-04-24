---
title: クスト ping REST API - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの KUsto Ping REST API について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 14a3d3dd641ff435784eac4e813d98bac8c4a552
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502586"
---
# <a name="kusto-ping-rest-api"></a>クストーピン REST API

ping REST API は、ロード バランサーなどのネットワーク デバイスが、クラスターのステートレス フロントエンド コンポーネントの単純なネットワーク応答性を確認できるようにするために提供されています。 GET 動詞がこのエンドポイントに適用されると、クラスターは 200 OK HTTP 応答を返して応答します。 REST API は認証されません (呼び出し元は HTTP`Authorization`ヘッダーを送信する必要はありません)。

- パス: `/v1/rest/ping`
- 動詞：`GET`
- アクション: メッセージで`200 OK`応答する
- 引数: なし