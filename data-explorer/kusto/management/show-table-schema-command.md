---
title: .show テーブル スキーマ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .show テーブル スキーマについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 9223baa0242cc936b25a7d3293d102cb3966ed53
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519773"
---
# <a name="show-table-schema"></a>.show テーブル スキーマ

作成/変更コマンドおよび追加のテーブル メタデータで使用するスキーマを取得します。

[データベース ユーザーのアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

```
.show table TableName cslschema 
```
| 出力パラメーター | Type   | 説明                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | テーブルの名前。                                    |
| スキーマ           | String | テーブルの作成/変更に使用するテーブル スキーマ |
| DatabaseName     | String | テーブルが属するデータベース                   |
| Folder           | String | テーブルのフォルダ                                            |
| ドクスト文字列        | String | テーブルのドキュメント文字列                                         |


## <a name="show-table-schema-as-json"></a>.show テーブルスキーマを JSON として表示

JSON 形式のスキーマと追加のテーブル メタデータを取得します。

[データベース ユーザーのアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

```
.show table TableName schema as JSON
```

| 出力パラメーター | Type   | 説明                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | テーブルの名前                   |
| スキーマ           | String | JSON 形式のテーブルスキーマ         |
| DatabaseName     | String | テーブルが属するデータベース |
| Folder           | String | テーブルのフォルダ                          |
| ドクスト文字列        | String | テーブルのドキュメント文字列                       |