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
ms.openlocfilehash: 899e54b46dd231db0bf1272c0eb1933dad474a47
ms.sourcegitcommit: 2ebd83369f247cf6dd91709f26e4ecd873489eaa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83555017"
---
# <a name="show-table-schema"></a>.show テーブル スキーマ

Create/alter コマンドと追加のテーブルメタデータで使用するスキーマを取得します。

[データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

```kusto
.show table TableName cslschema 
```

| 出力パラメーター | 種類   | 説明                                               |
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
.show table TableName schema as json
```

| 出力パラメーター | 種類   | 説明                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | テーブルの名前                   |
| スキーマ           | String | JSON 形式のテーブルスキーマ         |
| DatabaseName     | String | テーブルが属しているデータベース |
| Folder           | String | テーブルのフォルダー                          |
| DocString        | String | テーブルの docstring                       |
