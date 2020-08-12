---
title: 継続的なデータエクスポートエラーの表示-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの継続的なデータエクスポートの失敗を表示する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 54c95e3beecd25b504c52c2fe06ffa51160ac8ca
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149509"
---
# <a name="show-continuous-export-failures"></a>連続エクスポートエラーの表示

連続エクスポートの一部として記録されたすべてのエラーを返します。 目的の時間範囲だけを表示するには、コマンドの Timestamp 列によって結果をフィルター処理します。 

## <a name="syntax"></a>構文

`.show` `continuous-export` *ContinuousExportName* `failures`

## <a name="properties"></a>Properties

| プロパティ             | Type   | 説明                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連続エクスポートの名前  |

## <a name="output"></a>出力

| 出力パラメーター | Type      | 説明                                         |
|------------------|-----------|-----------------------------------------------------|
| Timestamp        | Datetime  | エラーのタイムスタンプ。                           |
| OperationId      | String    | 失敗の操作 ID。                    |
| 名前             | String    | 連続エクスポート名。                             |
| LastSuccessRun   | Timestamp | 連続エクスポートが最後に正常に実行された。   |
| FailureKind      | String    | 失敗/PartialFailure。 PartialFailure は、エラーが発生する前に一部のアーティファクトが正常にエクスポートされたことを示します。 |
| 詳細          | String    | エラーの詳細。                              |

## <a name="example"></a>例 

```kusto
.show continuous-export MyExport failures 
```

| Timestamp                   | OperationId                          | 名前     | LastSuccessRun              | FailureKind | 詳細    |
|-----------------------------|--------------------------------------|----------|-----------------------------|-------------|------------|
| 2019-01-01 11:07: 41.1887304 | ec641435-2505-4532-ba19-d6ab88c96a9d | MyExport | 2019-01-01 11:06: 35.6308140 | 障害     | 詳細... |
