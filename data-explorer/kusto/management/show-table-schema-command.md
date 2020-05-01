---
title: 。テーブルスキーマの表示-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでテーブルスキーマを表示する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: e2a550f0ea755181d39524876833cff4281608b4
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618345"
---
# <a name="show-table-schema"></a>.show テーブル スキーマ

Create/alter コマンドと追加のテーブルメタデータで使用するスキーマを取得します。

[データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

```kusto
.show table TableName cslschema 
```

| 出力パラメーター | Type   | 説明                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | テーブルの名前。                                    |
| スキーマ           | String | テーブルの作成/変更にはテーブルスキーマを使用する必要があります |
| DatabaseName     | String | テーブルが属しているデータベース                   |
| Folder           | String | テーブルのフォルダー                                            |
| DocString        | String | テーブルの docstring                                         |


## <a name="show-table-schema-as-json"></a>。テーブルスキーマを JSON として表示します

JSON 形式のスキーマと追加のテーブルメタデータを取得します。

[データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

```kusto
.show table TableName schema as JSON
```

| 出力パラメーター | Type   | 説明                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | テーブルの名前                   |
| スキーマ           | String | JSON 形式のテーブルスキーマ         |
| DatabaseName     | String | テーブルが属しているデータベース |
| Folder           | String | テーブルのフォルダー                          |
| DocString        | String | テーブルの docstring                       |
