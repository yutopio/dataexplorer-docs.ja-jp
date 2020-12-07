---
title: . データベースを表示-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーにデータベースを表示する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: eb3e1dd16d36af5d92019b710f3d5790a24b1296
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320487"
---
# <a name="show-database"></a>.show database

コンテキストデータベースのプロパティを示すテーブルを返します。

すべてのレコードが、ユーザーがアクセスできるクラスター内のデータベースに対応するテーブルを返すには、「」を参照してください [`.show databases`](show-databases.md) 。

**構文**

`.show` `database` [`details` | `identity` | `policies` | `datastats`]

オプションが指定されていない既定の呼び出しは、' identity ' オプションと同じです。

**' Identity ' オプションの出力**
 
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

**[詳細] オプションの出力**
 
|出力パラメーター |種類 |説明 
|---|---|---
|DatabaseName  |String |データベース名。 データベース名では大文字と小文字が区別されます。 
|PersistentStorage  |String |データベースが格納されている永続的なストレージ URI。 (短期データベースの場合、このフィールドは空です)。 
|バージョン  |String |データベースのバージョン番号。 この数は、データベースの変更操作 (データの追加やスキーマの変更など) ごとに更新されます。 
|IsCurrent  |Boolean |データベースが現在の接続が指しているデータベースの場合は True を指定します。 
|DatabaseAccessMode  |String |クラスターがデータベースにアタッチされる方法。 たとえば、データベースが読み取り専用モードでアタッチされている場合、クラスターはデータベースを変更するすべての要求に失敗します。 
|"この名前" |String |データベースの名前。
|AuthorizedPrincipals |String | データベースの承認されたプリンシパルのコレクション (JSON 形式でシリアル化されたもの)。
|RetentionPolicy |String | データベースの保持ポリシー (JSON 形式でシリアル化されます)。
|MergePolicy |String | データベースのエクステントマージポリシー (JSON 形式でシリアル化されます)。
|CachingPolicy |String | データベースのキャッシュポリシー (JSON 形式でシリアル化されます)。
|シャード Ingpolicy |String | データベースのシャーディングポリシー (JSON 形式でシリアル化されます)。
|StreamingIngestionPolicy |String | データベースのストリーミングインジェストポリシー (JSON 形式でシリアル化)。
|IngestionBatchingPolicy |String | データベースのインジェストバッチ処理ポリシー (JSON 形式でシリアル化されます)。
|TotalSize |Real | データベースのエクステントの合計サイズ。
|DatabaseId |Guid |データベースの一意の ID。

**' Policies ' オプションの出力**
 
|出力パラメーター |種類 |説明 
|---|---|---
|DatabaseName  |String |データベース名。 データベース名では大文字と小文字が区別されます。 
|PersistentStorage  |String |データベースが格納されている永続的なストレージ URI。 (短期データベースの場合、このフィールドは空です)。 
|バージョン  |String |データベースのバージョン番号。 この数は、データベースの変更操作 (データの追加やスキーマの変更など) ごとに更新されます。 
|IsCurrent  |Boolean |データベースが現在の接続が指しているデータベースの場合は True を指定します。 
|DatabaseAccessMode  |String |クラスターがデータベースにアタッチされる方法。 たとえば、データベースが読み取り専用モードでアタッチされている場合、クラスターはデータベースを変更するすべての要求に失敗します。 
|"この名前" |String |データベースの愛称。
|DatabaseId |Guid |データベースの一意の ID。
|AuthorizedPrincipals |String | データベースの承認されたプリンシパルのコレクション (JSON 形式でシリアル化されたもの)。
|RetentionPolicy |String | データベースの保持ポリシー (JSON 形式でシリアル化されます)。
|MergePolicy |String | データベースのエクステントマージポリシー (JSON 形式でシリアル化されます)。
|CachingPolicy |String | データベースのキャッシュポリシー (JSON 形式でシリアル化されます)。
|シャード Ingpolicy |String | データベースのシャーディングポリシー (JSON 形式でシリアル化されます)。
|StreamingIngestionPolicy |String | データベースのストリーミングインジェストポリシー (JSON 形式でシリアル化)。
|IngestionBatchingPolicy |String | データベースのインジェストバッチ処理ポリシー (JSON 形式でシリアル化されます)。

**' Datastats ' オプションの出力**

|出力パラメーター |種類 |説明 
|---|---|---
|DatabaseName  |String |データベース名。 データベース名では大文字と小文字が区別されます。
|PersistentStorage  |String |データベースが格納されている永続的なストレージ URI。 (短期データベースの場合、このフィールドは空です)。 
|バージョン  |String |データベースのバージョン番号。 この数は、データベースの変更操作 (データの追加やスキーマの変更など) ごとに更新されます。 
|IsCurrent  |Boolean |データベースが現在の接続が指しているデータベースの場合は True を指定します。 
|DatabaseAccessMode  |String |クラスターがデータベースにアタッチされる方法。 たとえば、データベースが読み取り専用モードでアタッチされている場合、クラスターはデータベースを変更するすべての要求に失敗します。 
|"この名前" |String |データベースの愛称。
|DatabaseId |Guid |データベースの一意の ID。
|OriginalSize |Real | データベースのエクステントの合計元のサイズ。
|ExtentSize |Real | データベースのエクステント合計サイズ (データ + インデックス)。
|CompressedSize |Real | データベースのエクステントの合計データ圧縮サイズ。
|IndexSize |Real | データベースのエクステントの合計インデックスサイズ。
|RowCount |Long | データベースのエクステント総数の行数。
|ホット Originalsize |Real | データベースのホットエクステントの合計が元のサイズになります。
|HotExtentSize |Real | データベースのホットエクステントの合計サイズ (データ + インデックス)。
|HotCompressedSize |Real | データベースのホットエクステントの合計データ圧縮サイズ。
|ホット Indexsize |Real | データベースのホットエクステントのインデックスサイズの合計。
|ホット行数 |Long | データベースのホットエクステントの合計行数。