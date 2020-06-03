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
ms.openlocfilehash: 9ed8ba6ac5e66326e6aa1d9e7a7f474506b57e26
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328978"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>クロスクラスター クエリとスキーマ変更

クラスター間クエリを実行する場合、最初のクエリ解釈を実行するクラスターには、リモートクラスターで参照されているエンティティのスキーマが必要です。
次のクエリが*Cluster1*に送信されたとき

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*Cluster1*では、 *Cluster2*の列*と列の*データ型を把握している必要があります。 この情報を取得するために、特殊なコマンドは*Cluster1*から*Cluster2*に送信されます。
コマンドを送信すると、コストが高くなる可能性があります。 取得されると、リモートエンティティのスキーマがキャッシュされ、同じエンティティを参照する次のクエリでは、実行されるネットワーク操作が減ります。

リモートエンティティのスキーマを変更すると、望ましくない影響が生じる可能性があります。 たとえば、追加された列が認識されないか、削除された列によってセマンティックエラーではなく ' Partial Query Error ' が発生します。

この問題を解決するために、キャッシュされたスキーマは取得後1時間で有効期限が切れます。これにより、変更後に1時間以上実行されたすべてのクエリが最新のスキーマで動作するようになります。
