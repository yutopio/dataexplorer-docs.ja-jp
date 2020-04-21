---
title: クエリの一貫性 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクエリの一貫性について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: b66540af2d2d4bef4571249474cd618e69eb2261
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523105"
---
# <a name="query-consistency"></a>クエリの一貫性

Kusto は、**強**いクエリと弱いの 2 つのクエリの一貫性モデルをサポート**しています**。

強い整合性を持つクエリ (既定) には"読み取りマイ変更" が保証されます。 制御コマンドを送信し、コマンドが正常に完了したことを肯定応答を受け取るクライアントは、次の直後のクエリがコマンドの結果を観察することを保証します。

弱い整合性のクエリ (クライアントが明示的に有効にする必要があります) は保証されません。 クエリを実行するクライアントは、変更とそれらの変更を反映したクエリの間に、ある程度の待機時間 (通常は 1 ~ 2 分) を観察する場合があります。

弱い整合性クエリの利点は、データベースの変更を処理するクラスター ノードの負荷を軽減することです。 一般に、お客様はまず強い一貫性のあるモデルを試し、必要に応じて弱い一貫性を使用するように切り替えることをお勧めします。

[REST API 呼び出し](../api/rest/request.md)を行う際`queryconsistency`にプロパティを設定することで、弱い整合性のクエリに切り替えます。 Kusto .NET クライアントのユーザーは[、Kusto 接続文字列](../api/connection-strings/kusto.md)または[クライアント要求のプロパティ](../api/netfx/request-properties.md)のフラグとして設定することもできます。