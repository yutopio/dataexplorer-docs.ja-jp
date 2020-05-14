---
title: Kusto クロスクラスタークエリ & スキーマ変更-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのクロスクラスタークエリとスキーマ変更について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 58fd8cef3836d17eb327734338b1567d815d7349
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382031"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>クロスクラスター クエリとスキーマ変更 

クロスクラスタークエリを実行する場合、最初のクエリ解釈を実行するクラスターには、リモートクラスターで参照されているエンティティのスキーマが必要です。
次のクエリが*Cluster1*に送信されたとき

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*Cluster1*では、 *Cluster2*の列とその列のデータ型を把握*している*必要があります。 特別なコマンドが*Cluster1*から*Cluster2*に送信されるようにするためです。
このコマンドを送信すると非常に負荷が大きくなる可能性があるため、取得した後は、リモートエンティティのスキーマがキャッシュされ、同じエンティティを参照する次のクエリでは、より少ないネットワーク操作を実行する必要があります。

これにより、リモートエンティティのスキーマが変更された場合に望ましくない影響が生じる可能性があります (追加された列が認識されていないか、削除された列によって、セマンティックエラーではなく "部分的なクエリエラー

この問題を軽減するために、キャッシュされたスキーマは取得後1時間で有効期限が切れます。そのため、変更後に1時間以上実行されたクエリは、最新のスキーマで動作します。