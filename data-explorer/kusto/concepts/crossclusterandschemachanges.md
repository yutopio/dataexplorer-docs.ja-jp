---
title: クラスター間のクエリとスキーマの変更 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクラスター間のクエリとスキーマの変更について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a9473add657fe8a888818f83c72f12d47138c00e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523292"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>クロスクラスター クエリとスキーマ変更 

クロスクラスタークエリを実行する場合、初期クエリ解釈を行うクラスタには、リモートクラスタで参照されるエンティティのスキーマが必要です。
したがって、次のクエリが*Cluster1*に送信されたとき

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*Cluster1*は *、Cluster2*の*Table2*の列と、それらの列のデータ型を知っている必要があります。 特別なコマンドが*Cluster1*から*Cluster2*に送信されることを実現します。
このコマンドの送信は非常に高価になる可能性があるため、取得後、リモート エンティティのスキーマがキャッシュされ、同じエンティティを参照する次のクエリで実行するネットワーク操作が少なくなります。

これにより、リモート エンティティのスキーマが変更された場合に望ましくない影響が発生する可能性があります (追加された列が認識されないか、列が削除され、セマンティック エラーではなく '部分クエリ エラー' が発生します)。

この問題を軽減するために、キャッシュされたスキーマは取得後 1 時間で期限切れになるため、変更後 1 時間以上実行されたクエリは最新のスキーマで動作します。