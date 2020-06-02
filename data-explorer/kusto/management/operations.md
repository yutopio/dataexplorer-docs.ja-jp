---
title: Operations management-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの Operations management について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ac3d44fadf614606bc63e6a9aa3b8318419d0c70
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294391"
---
# <a name="operations-management"></a>運用管理

## <a name="show-operations"></a>。操作を表示します。 

`.show``operations`コマンドは、実行中と完了したすべての管理操作を含むテーブルを返します。これは、過去2週間以内に実行されています (現在の保有期間の構成)。

**構文**

|||
|---|---| 
|`.show` `operations`              |クラスターが処理しているすべての操作、またはクラスターが処理した操作を返します。
|`.show``operations` *OperationId*|特定の ID の操作の状態を返します。 
|`.show``operations` `(` *OperationId1* `,` *OperationId2* `,` ...)|特定の Id の操作の状態を返します。

**結果**
 
|出力パラメーター |Type |説明
|---|---|---
|id |String |操作 Id
|Operation |String |管理者コマンドのエイリアス
|NodeId |String |コマンドにリモート実行 (たとえば、DataIngestPull) がある場合は、実行中のリモートノードの ID が NodeId に含まれます。
|StartedOn |DateTime |操作が開始された日付/時刻 (UTC)
|LastUpdatedOn |DateTime |操作が最後に更新された日付/時刻 (UTC)。操作内のステップまたは完了ステップのいずれかを指定できます。
|Duration |DateTime |LastUpdateOn と StartedOn の間の TimeSpan
|州 |String |コマンドの状態-"処理中"、"完了"、または "失敗" の値を持つことができます。
|Status |String |失敗した操作のエラーを含む追加のヘルプ文字列
 
**例**
 
|id |Operation |ノード ID |開始日 |最終更新日 |Duration |州 |Status 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |SchemaShow | |2015-01-06 08:47: 01.0000000 |2015-01-06 08:47: 01.0000000 |0001-01-01 00:00: 00.0000000 |完了 |
|84 1fafa4076a47 cba93008-4836da9c75 |DataIngestPull |Kusto. Azure. Svc_IN_1 |2015-01-06 08:47: 02.0000000 |2015-01-06 08:48: 19.0000000 |0001-01-01 00:01: 17.0000000 |完了 |
|e198c519-5263-4629-a158-8d68f7a1022f |表示の表示 | |2015-01-06 08:47: 18.0000000 |2015-01-06 08:47: 18.0000000 |0001-01-01 00:00: 00.0000000 |完了 |
|a9f287a1-f3e6-4154-ad18-b86438da0929 |ExtentsDrop | |2015-01-11 08:41: 01.0000000 |0001-01-01 00:00: 00.0000000 |0001-01-01 00:00: 00.0000000 |InProgress |
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DataIngestPull | |2015-01-10 14:57: 41.0000000 |2015-01-10 14:57: 41.0000000 |0001-01-01 00:00: 00.0000000 |Failed |コレクションが変更されました。 列挙操作は実行できません。

## <a name="show-operation-details"></a>。操作の詳細を表示します

操作では、結果を永続化できます。また、操作が完了したら、を使用して結果を取得でき `.show` `operation` `details` ます。

> [!NOTE]
> すべてのコントロールコマンドの結果が保持されるわけではありません。 これらのコマンドは、通常、キーワードを使用して、非同期実行でのみ既定で実行さ `async` れます。 特定のコマンドのドキュメントを参照し、実行されているかどうかを確認します。 たとえば、「[データのエクスポート](data-export/index.md)」を参照してください)。
> コマンドの出力スキーマ `.show` `operations` `details` は、コマンドの同期実行によって返されるスキーマと同じです。
> コマンドは、 `.show` `operation` `details` 操作が正常に完了した後にのみ呼び出すことができます。 このコマンドを実行する前に、[[操作の表示] コマンド](#show-operations)を使用して操作の状態を確認します。

**構文**

`.show``operation` *OperationId*`details`

**結果**

結果は操作の種類ごとに異なり、同期的に実行された場合、操作結果のスキーマと一致します。

**使用例**

この例の*OperationId*は、[データエクスポート](../management/data-export/index.md)コマンドの1つの非同期実行からを返します。

```kusto 
.export 
  async 
  to csv ( 
    h@"https://storage1.blob.core.windows.net/containerName;secretKey", 
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey" 
  ) 
  <| myLogs 
```

非同期エクスポートコマンドによって次の操作 ID が返されました:

|OperationId|
|---|
|56e51622-eb4947 d1a(63966-6a03178efcd)|

この操作 ID は、コマンドが完了してエクスポートされた blob を照会するときに使用できます。 

```kusto
.show operation 56e51622-eb49-4d1a-b896-06a03178efcd details 
```

|パス|NumRecords |
|---|---|
|http://storage1.blob.core.windows.net/containerName/1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/2_454c0f1359e24795b6529da8a0101330.csv|15|
