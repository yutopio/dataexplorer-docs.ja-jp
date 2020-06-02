---
title: ジャーナル管理-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのジャーナル管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: 64b0f8179a328ce811747b05a90516fd8b6029be
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294408"
---
# <a name="journal-management"></a>ジャーナル管理

 `Journal`Azure データエクスプローラーデータベースで実行されるメタデータ操作について説明します。

メタデータ操作は、ユーザーが実行したコントロールコマンド、またはシステムによって実行された内部制御コマンド (保有期間によるエクステントの削除など) によって発生する可能性があります。

> [!NOTE]
> * 、などの新しいエクステントの*追加*を含むメタデータ操作では、 `.ingest` `.append` `.move` に一致するイベントが表示されません `Journal` 。
> * 結果セットの列のデータと、表示される形式は契約していません。 
  依存関係を取得することは推奨されません。

|Event        |EventTimestamp     |データベース  |EntityName|UpdatedEntityName|EntityVersion|Entitycontainername.entitysetname|
|-------------|-------------------|----------|----------|-----------------|-------------|-------------------|
|テーブルの作成 |2017-01-05 14:25:07|InternalDb|MyTable1  |MyTable1         |v1.0         |InternalDb         |
|テーブル名の変更 |2017-01-13 10:30:01|InternalDb|MyTable1  |MyTable2         |8.0         |InternalDb         |  

|OriginalEntityState|UpdatedEntityState                                              |ChangeCommand                                                                                                          |プリンシパル            |
|-------------------|----------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------|
|.                  |名前: MyTable1、Attributes: Name = ' [MyTable1]。[col1] ', Type = ' I32 '|. create table MyTable1 (col1: int)                                                                                      |imike@fabrikam.com
|.                  |データベースのプロパティ (長すぎてここに表示できません)         |. create database TestDB persist (@ " https://imfbkm.blob.core.windows.net/md ", @ " https://imfbkm.blob.core.windows.net/data ")|Azure AD app id = 76263cdb-545644e9c404
|名前: MyTable1、Attributes: Name = ' [MyTable1]。[col1] ', Type = ' I32 '|名前: MyTable2、Attributes: Name = ' [MyTable1]。[col1] ', Type = ' I32 '|。テーブル MyTable1 を MyTable2 に変更します。|rdmik@fabrikam.com

|Item                 |説明                                                              |                                
|---------------------|-------------------------------------------------------------------------|
|Event                |メタデータイベント名                                                  |
|EventTimestamp       |イベントのタイムスタンプ                                                      |                        
|データベース             |このデータベースのメタデータは、イベントの後で変更されました                |
|EntityName           |変更前に操作が実行されたエンティティ名。    |
|UpdatedEntityName    |変更後の新しいエンティティ名                                     |
|EntityVersion        |変更後の新しいメタデータバージョン (db/クラスター)               |
|Entitycontainername.entitysetname  |エンティティコンテナー名 (entity = column, container = table)               |
|OriginalEntityState  |変更前のエンティティ (エンティティプロパティ) の状態            |
|UpdatedEntityState   |変更後の新しい状態                                           |
|ChangeCommand        |メタデータの変更をトリガーした、実行されたコントロールコマンド          |
|プリンシパル            |Control コマンドを実行したプリンシパル (ユーザー/アプリ)               |
    
## <a name="show-journal"></a>。ジャーナルを表示します

この `.show journal` コマンドは、データベースまたはユーザーが管理者アクセス権を持っているクラスターのメタデータ変更の一覧を返します。

**アクセス許可**

すべてのユーザー (クラスターアクセス) でコマンドを実行できます。 

返される結果は次のとおりです。 
- コマンドを実行しているユーザーのすべてのジャーナルエントリ。 
- コマンドを実行しているユーザーが管理者アクセス権を持っているデータベースのすべてのジャーナルエントリ。 
- コマンドを実行しているユーザーがクラスター管理者である場合は、すべてのクラスタージャーナルエントリ。 

## <a name="show-database-databasename-journal"></a>。データベース*DatabaseName*ジャーナルを表示します 

`.show` `database` *DatabaseName* `journal` コマンドは、特定のデータベースメタデータの変更に対するジャーナルを返します。

**アクセス許可**

すべてのユーザー (クラスターアクセス) でコマンドを実行できます。 返される結果は次のとおりです。 
- コマンドを実行しているユーザーが*databasename*内のデータベース管理者である場合、データベース*DatabaseName*のすべてのジャーナルエントリ。 
- それ以外の場合は、コマンドを実行しているデータベースとユーザーのすべてのジャーナルエントリ `DatabaseName` 。 
