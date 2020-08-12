---
title: 継続的なデータエクスポートアーティファクトの表示-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーで継続的なデータエクスポートアーティファクトを表示する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 68b33ffc46df1e3bc4f63f8f7e9c86786116308b
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149387"
---
# <a name="show-continuous-export-artifacts"></a>連続エクスポートの成果物を表示する

すべての実行で連続エクスポートによってエクスポートされたすべての成果物を返します。 対象のレコードのみを表示するには、コマンドの Timestamp 列で結果をフィルター処理します。 エクスポートされたアーティファクトの履歴は14日間保持されます。 

## <a name="syntax"></a>構文

`.show` `continuous-export` *ContinuousExportName* `exported-artifacts`

## <a name="properties"></a>Properties

| プロパティ             | Type   | 説明                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連続エクスポートの名前。 |

## <a name="output"></a>出力

| 出力パラメーター  | Type     | 説明                            |
|-------------------|----------|----------------------------------------|
| Timestamp         | Datetime | 連続エクスポート実行のタイムスタンプ |
| ExternalTableName | String   | 外部テーブルの名前             |
| Path              | String   | [出力パス]                            |
| NumRecords        | long     | パスにエクスポートされたレコードの数     |

## <a name="example"></a>例

```kusto
.show continuous-export MyExport exported-artifacts | where Timestamp > ago(1h)
```

| Timestamp                   | ExternalTableName | Path             | NumRecords | SizeInBytes |
|-----------------------------|-------------------|------------------|------------|-------------|
| 2018-12-20 07:31: 30.2634216 | ExternalBlob      | `http://storageaccount.blob.core.windows.net/container1/1_6ca073fd4c8740ec9a2f574eaa98f579.csv` | 10                          | 1024              |
