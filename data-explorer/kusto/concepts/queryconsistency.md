---
title: クエリの整合性-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのクエリの一貫性について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 8b4d1df4dc9a035764f9d50a4f9c4dcf452da67e
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512267"
---
# <a name="query-consistency"></a>クエリの一貫性

Kusto では、2つのクエリ整合性モデル ( **strong**と**weak**) がサポートされています。

厳密に一貫性のあるクエリ (既定値) には、"変更の読み取り" の保証があります。 制御コマンドを送信し、コマンドが正常に完了したことを示す受信確認を受信した場合は、すぐに次のクエリを実行すると、コマンドの結果が確認されます。

クライアントによって明示的に有効にする必要がある、一貫性のないクエリは保証されません。 クエリを実行するクライアントは、変更とその変更を反映したクエリの間に、待機時間 (通常は1-2 分) が発生する可能性があります。

整合性の弱いクエリの利点は、データベースの変更を処理するクラスターノードの負荷が軽減されることです。 一般に、最初に厳密に一貫性のあるモデルを試すことをお勧めします。 必要に応じて、弱い一貫したクエリを使用するように切り替えます。

弱い一貫したクエリに切り替えるには、 `queryconsistency` [REST API 呼び出し](../api/rest/request.md)を行うときにプロパティを設定します。 また、.NET クライアントのユーザーは、 [Kusto 接続文字列](../api/connection-strings/kusto.md)で、または[クライアント要求のプロパティ](../api/netfx/request-properties.md)でフラグとして設定することもできます。
