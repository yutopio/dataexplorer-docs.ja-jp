---
title: .create-merge テーブル - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .create-merge テーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 408046e198710c4b825a399fcb90960411de1041
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744450"
---
# <a name="create-merge-tables"></a>作成/マージ テーブル

特定のデータベースのコンテキストで、1 回の一括操作で既存のテーブルのスキーマを作成または拡張できます。

既存のテーブルを拡張するための[テーブル管理者権限](../management/access-control/role-based-authorization.md)とデータベース[ユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.create-merge``tables`*テーブル名1* ([列名:列タイプ]、.)[`,` *テーブル名 2* ([列名:列の種類]、.)

* 存在しない指定されたテーブルが作成されます。
* 既に存在する指定されたテーブルには、そのスキーマが拡張されます。
    * 既存でない列は、既存のテーブルのスキーマの_末尾_に追加されます。
    * コマンドで指定されていない既存の列は、既存のテーブルのスキーマから削除されません。
    * コマンドで別のデータ型で指定された既存の列は、既存のテーブルのスキーマのデータ型に対してエラーが発生します (テーブルの作成や拡張は行いません)。

**例** 

```kusto
.create-merge tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```

**出力を返す**

| TableName | DatabaseName  | Folder | ドクスト文字列 |
|-----------|---------------|--------|-----------|
| マイログス    | トップ比較 |        |           |
| マイユーザー   | トップ比較 |        |           |
