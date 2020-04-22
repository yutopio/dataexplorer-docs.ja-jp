---
title: .create テーブル - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのテーブルの作成について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: f76c4d4a2780b58d9596537aea183d026d086fc5
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744034"
---
# <a name="create-tables"></a>.create テーブル

一括操作として新しい空のテーブルを作成します。

コマンドは、特定のデータベースのコンテキストで実行する必要があります。

[データベース ユーザーのアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.create``tables`*テーブル名1* ([列名:列タイプ]、.)[`,` *テーブル名 2* ([列名:列の種類]、.)

テーブルが既に存在する場合、コマンドは成功を返します。
 
**例** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
