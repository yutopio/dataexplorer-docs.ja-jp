---
title: Operations management-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの Operations management について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e4e373cd694de989b2bbef8058aaf1c8b3ca3025
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616475"
---
# <a name="operations-management"></a>運用管理

## <a name="show-operations"></a>。操作を表示します。

`.show``operations`コマンドは、実行中と完了したすべての管理操作を含むテーブルを返します。これは、過去2週間以内に実行されています (現在の保有期間の構成)。

**構文**

|||
|---|---| 
|`.show` `operations`              |クラスターが処理した、または処理中のすべての操作を返します 
|`.show``operations` *OperationId*|特定の ID の操作の状態を返します。 
|`.show``operations` `,` *OperationId1* OperationId1 OperationId2`,` ...) *OperationId2* `(`|特定の Id の操作の状態を返します。

**結果**
 
|出力パラメーター |Type |説明 
|---|---|---
|Id |String |操作の識別子。
|Operation |String |管理者コマンドのエイリアス 
|NodeId |String |コマンドにリモート実行 (例: DataIngestPull) がある場合、NodeId には実行中のリモートノードの id が含まれます。 
|StartedOn |DateTime |操作が開始された日付/時刻 (UTC) 
|LastUpdatedOn |DateTime |操作が最後に更新された日付/時刻 (UTC)。操作内のステップまたは完了ステップのいずれかを指定できます。 
|Duration |DateTime |LastUpdateOn と StartedOn の間の TimeSpan 
|State |String |コマンドの状態: "処理中"、"完了" または "失敗" の値を持つことができます。 
|Status |String |失敗した操作のエラーを保持する追加のヘルプ文字列 
 
**例**
 
|Id |Operation |ノード ID |開始日 |最終更新日 |Duration |State |Status 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |SchemaShow | |2015-01-06 08:47: 01.0000000 |2015-01-06 08:47: 01.0000000 |0001-01-01 00:00: 00.0000000 |完了 | 
|84 1fafa4076a47 cba93008-4836da9c75 |DataIngestPull |Kusto. Azure. Svc_IN_1 |2015-01-06 08:47: 02.0000000 |2015-01-06 08:48: 19.0000000 |0001-01-01 00:01: 17.0000000 |完了 | 
|e198c519-5263-4629-a158-8d68f7a1022f |表示の表示 | |2015-01-06 08:47: 18.0000000 |2015-01-06 08:47: 18.0000000 |0001-01-01 00:00: 00.0000000 |完了 | 
|a9f287a1-f3e6-4154-ad18-b86438da0929 |ExtentsDrop | |2015-01-11 08:41: 01.0000000 |0001-01-01 00:00: 00.0000000 |0001-01-01 00:00: 00.0000000 |InProgress | 
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DataIngestPull | |2015-01-10 14:57: 41.0000000 |2015-01-10 14:57: 41.0000000 |0001-01-01 00:00: 00.0000000 |失敗 |コレクションが変更されました。列挙操作は実行できません。 

## <a name="show-operation-details"></a>。操作の詳細を表示します

操作は (オプションで) 結果を永続化できます。また、操作が完了したら、 `.show` `operation`次を使用して操作を取得できます。`details` 

**注:**

* すべてのコントロールコマンドの結果が永続化されるわけではありません。また、通常は、非同期`async`実行でのみ (キーワードを使用して) 実行するコントロールコマンドもあります。 ドキュメントで特定のコマンドを検索し、実行されているかどうかを確認してください (例については、「[データのエクスポート](data-export/index.md)」を参照してください)。 
* `details`コマンドの`.show` `operations`出力スキーマは、コマンドの同期実行によって返されるスキーマと同じです。 
* コマンド`.show` `operation` `details`は、操作が正常に完了した後にのみ呼び出すことができます。 [[操作の表示] コマンド](#show-operations)を使用して、このコマンドを呼び出す前の操作の状態を確認します。 

**構文**

`.show``operation` *OperationId*`details`

**結果**

結果は操作の種類によって異なり、同期的に実行された場合、操作結果のスキーマと一致します。 

**使用例**

この例の*OperationId*は、いずれかの[データエクスポート](../management/data-export/index.md)コマンドの非同期実行から返されたものです。

```kusto 
.export 
  async 
  to csv ( 
    h@"https://storage1.blob.core.windows.net/containerName;secretKey", 
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey" 
  ) 
  <| myLogs 

```

非同期エクスポートコマンドによって次の操作 id が返されました:

|OperationId|
|---|
|56e51622-eb4947 d1a(63966-6a03178efcd)|

この操作 id は、コマンドが次のように、エクスポートされた blob をクエリするために完了した場合に使用できます。 

```kusto
.show operation 56e51622-eb49-4d1a-b896-06a03178efcd details 
```

**結果**

|Path|NumRecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/2_454c0f1359e24795b6529da8a0101330.csv|15|