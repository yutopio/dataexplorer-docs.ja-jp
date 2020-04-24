---
title: .show テーブル - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの .show テーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a8faf307a241d1ba0f73436d9503a56c9078e471
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519637"
---
# <a name="show-tables"></a>.show テーブル

指定したテーブルまたはデータベース内のすべてのテーブルを含むセットを返します。

[データベース ビューアの権限が](../management/access-control/role-based-authorization.md)必要です。

```
.show tables
.show tables (T1, ..., Tn)
```

**出力**

|出力パラメーター |Type |説明
|---|---|---
|TableName  |String |テーブルの名前。
|DatabaseName  |String |テーブルが属するデータベース。
|Folder |String |テーブルのフォルダ。
|ドクスト文字列 |String |テーブルを表す文字列。

**出力例**

|テーブル名 |データベース名 |Folder | ドクスト文字列
|---|---|---|---
|Table1 |DB1 |ログ |サービス ログを含む
|Table2 |DB1 | レポーティング |
|Table3 |DB1 | | 拡張情報 |
|表4 |DB2 | メトリック| サービスパフォーマンス情報を格納します。