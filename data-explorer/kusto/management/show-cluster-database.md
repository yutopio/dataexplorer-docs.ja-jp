---
title: 。クラスターデータベースを表示します-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでクラスターデータベースを表示する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c6d25380a44a2195f407c52a2224ee28fad9f8bb
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320504"
---
# <a name="show-cluster-databases"></a>.show cluster databases

クラスターにアタッチされているすべてのデータベースを示すテーブルを返します。このテーブルには、コマンドを呼び出したユーザーがアクセスできます。 特定のデータベース名が使用されている場合は、それらのデータベースだけが含まれます。

**構文**

`.show` `cluster` `databases` [`details` | `identity` | `policies` | `datastats`]

`.show``cluster` `databases` `(`database1 `,` database2 `,` ...databaseN`)`

**出力**
 
|出力パラメーター |種類 |説明 
|---|---|---
|DatabaseName  |String |データベース名。 データベース名では大文字と小文字が区別されます。 
|PersistentStorage  |String |データベースが格納されている永続的なストレージ URI。 (短期データベースの場合、このフィールドは空です)。 
|バージョン  |String |データベースのバージョン番号。 この数は、データベースの変更操作 (データの追加やスキーマの変更など) ごとに更新されます。 
|IsCurrent  |Boolean |データベースが現在の接続が指しているデータベースの場合は True を指定します。 
|DatabaseAccessMode  |String |クラスターがデータベースにアタッチされる方法。 たとえば、データベースが読み取り専用モードでアタッチされている場合、クラスターはデータベースを変更するすべての要求に失敗します。 
|"この名前" |String |データベースの名前。
|CurrentUserIsUnrestrictedViewer |Boolean | 現在のユーザーがデータベースの無制限のビューアーであるかどうかを指定します。
|DatabaseId |Guid |データベースの一意の ID。