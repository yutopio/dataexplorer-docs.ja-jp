---
title: オペレーション管理 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの操作の管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 01f30a8d391948d5466ef76b2951d55aa6084e15
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520691"
---
# <a name="operations-management"></a>運用管理

## <a name="show-operations"></a>.show 操作

`.show``operations`コマンドは、過去 2 週間 (現在は保持期間の構成) で実行されたすべての管理操作を含むテーブルを返します。

**構文**

|||
|---|---| 
|`.show` `operations`              |クラスターが処理した、または処理しているすべての操作を返します。 
|`.show``operations`*オペレーションId*|特定の ID の操作ステータスを返します。 
|`.show``operations`*OperationId1*`,`*OperationId2*`,`操作 Id1 操作 Id2 .) `(`|特定の ID の操作ステータスを返します。

**結果**
 
|出力パラメーター |Type |説明 
|---|---|---
|Id |String |操作識別子。
|Operation |String |管理コマンド エイリアス 
|NodeId |String |コマンドにリモート実行がある場合 (例: DataIngestPull) - NodeId には実行中のリモートノードの ID が含まれます。 
|スタートンオン |DateTime |操作が開始された日時 (UTC) 
|ラストアップデートオン |DateTime |操作が最後に更新された日時 (UTC) (操作内のステップ、または完了ステップのいずれか) 
|Duration |DateTime |ラストアップデートオンとスタートンの間のタイムスパン 
|State |String |コマンド状態: "処理中"、"完了"、または"失敗"の値を持つことができます。 
|Status |String |失敗した操作のエラーを保持する追加のヘルプ文字列 
 
**例**
 
|Id |Operation |ノード ID |開始日 |最終更新日 |Duration |State |Status 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |スキーマショー | |2015-01-06 08:47:01.0000000 |2015-01-06 08:47:01.0000000 |0001-01-01 00:00:00.0000000 |完了 | 
|841fafa4-076a-4cba-9300-4836da0d9c75 |データインジングエストプル |Svc_IN_1 |2015-01-06 08:47:02.0000000 |2015-01-06 08:48:19.0000000 |0001-01-01 00:01:17.0000000 |完了 | 
|e198c519-5263-4629-a158-8d68f7a1022f |オペレーションショー | |2015-01-06 08:47:18.0000000 |2015-01-06 08:47:18.0000000 |0001-01-01 00:00:00.0000000 |完了 | 
|a9f287a1-f3e6-4154-ad18-b86438da0929 |エクステントドロップ | |2015-01-11 08:41:01.0000000 |0001-01-01 00:00:00.0000000 |0001-01-01 00:00:00.0000000 |InProgress | 
|9edb3ecc-f4b4-4738-87e1-648eed2bd9988 |データインジングエストプル | |2015-01-10 14:57:41.0000000 |2015-01-10 14:57:41.0000000 |0001-01-01 00:00:00.0000000 |失敗 |コレクションが変更されました。列挙操作は実行できません。 

## <a name="show-operation-details"></a>.show 操作の詳細

操作は(オプションで)結果を保持することができ、これらは操作が完了したときに取得できます。 `.show` `operation``details` 

**注:**

* すべての制御コマンドが結果`async`を保持するわけではありません。 特定のコマンドのドキュメントを検索し、実行されるかどうかを確認してください (例えば、[データ・エクスポート](data-export/index.md)を参照)。 
* コマンドの`.show``operations``details`出力スキーマは、コマンドの同期実行から返されるスキーマと同じです。 
* この`.show``operation``details`コマンドは、操作が正常に完了した後にのみ呼び出すことができます。 show [operations コマンド](#show-operations)() を使用して、このコマンドを呼び出す前に操作の状態を確認します。 

**構文**

`.show``operation`*オペレーションId*`details`

**結果**

結果は操作の種類によって異なり、同期的に実行された場合に、操作結果のスキーマと一致します。 

**使用例**

この例の*OperationId*は、[いずれかのデータ エクスポート](../management/data-export/index.md)コマンドの非同期実行から返されるものです。

```kusto 
.export 
  async 
  to csv ( 
    h@"https://storage1.blob.core.windows.net/containerName;secretKey", 
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey" 
  ) 
  <| myLogs 

```
非同期エクスポート コマンドは、次の操作 ID を返しました。

|OperationId|
|---|
|56e51622-eb49-4d1a-b896-06a03178efcd|

この操作 ID は、エクスポートされた BLOB のクエリを実行するためにコマンドが完了したときに使用できます。 

```
.show operation 56e51622-eb49-4d1a-b896-06a03178efcd details 
```

**結果**

|Path|数字の記録|
|---|---|
|http://storage1.blob.core.windows.net/containerName/1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/2_454c0f1359e24795b6529da8a0101330.csv|15|