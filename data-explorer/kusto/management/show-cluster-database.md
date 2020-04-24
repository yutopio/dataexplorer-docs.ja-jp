---
title: .show クラスター データベース - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクラスター データベースの .show について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f354862df1bc9bef352819832125cf6f82ba0ae4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519994"
---
# <a name="show-cluster-databases"></a>.show クラスタ データベース

クラスターにアタッチされ、コマンドを呼び出すユーザーがアクセスできるすべてのデータベースを示す表を返します。 特定のデータベース名を使用する場合は、それらのデータベースのみが含まれます。

**構文**

`.show` `cluster` `databases` [`details` | `identity` | `policies` | `datastats`]

`.show``cluster``,`データベース1データベース2..`,` `databases` `(`データベースN`)`

**出力**
 
|出力パラメーター |Type |説明 
|---|---|---
|DatabaseName  |String |データベース名。 データベース名では大文字と小文字が区別されます。 
|永続ストレージ  |String |データベースが格納されている永続ストレージ URI。 (このフィールドは、一時データベースでは空です)。 
|Version  |String |データベースのバージョン番号。 この番号は、データベースの変更操作ごとに更新されます (データの追加やスキーマの変更など)。 
|IsCurrent  |Boolean |データベースが現在の接続が指すデータベースである場合は True。 
|データベース アクセス モード  |String |クラスターがデータベースにアタッチされる方法。 たとえば、データベースが ReadOnly モードでアタッチされている場合、クラスタはデータベースを変更する要求をすべて失敗します。 
|プリティネーム |String |データベースの名前がかなり。
|現在のユーザーは制限されていないビューア |Boolean | 現在のユーザーがデータベース上の無制限のビューアーかどうかを指定します。
|DatabaseId |Guid |データベースの一意の ID。