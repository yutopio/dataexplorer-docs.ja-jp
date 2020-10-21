---
title: ナロープラグイン-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのナロープラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a27794647eed3e8b30533d73456a0b1fb8ccde6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243194"
---
# <a name="narrow-plugin"></a>narrow プラグイン

```kusto
T | evaluate narrow()
```

この `narrow` プラグインは、幅の広いテーブルを、行番号、列の型、列の値 (as) の3つの列だけを含むテーブルに "unpivots `string` します。

`narrow`このプラグインは主に表示を目的として設計されており、水平スクロールを必要とせずに幅の広いテーブルを簡単に表示できるようになっています。

## <a name="syntax"></a>構文

`T | evaluate narrow()`

## <a name="examples"></a>例

次の例では、Kusto 制御コマンドの出力を読み取る簡単な方法を示して `.show diagnostics` います。

```kusto
.show diagnostics
 | evaluate narrow()
```

それ自体の結果は、 `.show diagnostics` 1 行と33列を含むテーブルになります。 プラグインを使用して `narrow` 、出力を次のように "回転" します。

行  | 列                              | [値]
-----|-------------------------------------|-----------------------------
0    | IsHealthy                           | True
0    | IsRebalanceRequired                 | 誤り
0    | IsScaleOutRequired                  | 誤り
0    | MachinesTotal                       | 2
0    | オフラインのスケジュール                     | 0
0    | NodeLastRestartedOn                 | 2017-03-14 10:59: 18.9263023
0    | AdminLastElectedOn                  | 2017-03-14 10:58: 41.6741934
0    | Clusterウォー Mdatacapacityfactor       | 0.130552847673333
0    | ExtentsTotal                        | 136
0    | Diskcold割り当て率        | 5
0    | InstancesTargetBasedOnDataCapacity  | 2
0    | TotalOriginalDataSize               | 5167628070
0    | TotalExtentSize                     | 1779165230
0    | IngestionsLoadFactor                | 0
0    | IngestionsInProgress                | 0
0    | IngestionsSuccessRate               | 100
0    | MergesInProgress                    | 0
0    | BuildVersion                        | 1.0.6281.19882
0    | BuildTime                           | 2017-03-13 11:02: 44.0000000
0    | ClusterDataCapacityFactor           | 0.130552847673333
0    | IsDataWarmingRequired               | 誤り
0    | RebalanceLastRunOn                  | 2017-03-21 09:14: 53.8523455
0    | DataWarmingLastRunOn                | 2017-03-21 09:19: 54.1438800
0    | MergesSuccessRate                   | 100
0    | NotHealthyReason                    | 空白
0    | IsAttentionRequired                 | 誤り
0    | AttentionRequiredReason             | 空白
0    | ProductVersion                      | KustoRelease_2017。03.13.2
0    | FailedIngestOperations              | 0
0    | 失敗した Mergeoperation               | 0
0    | MaxExtentsInSingleTable             | 64
0    | TableWithMaxExtents                 | KustoMonitoringPersistentDatabase.KustoMonitoringTable
0    | WarmExtentSize                      | 1779165230