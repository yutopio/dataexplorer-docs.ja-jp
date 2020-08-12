---
title: 継続的なデータのエクスポートを有効または無効にする-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーで継続的なデータエクスポートを無効または有効にする方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: b69db144474557ab8d5a8e19a7bce9bbbd5df183
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149339"
---
# <a name="disable-or-enable-continuous-export"></a>連続エクスポートを無効または有効にする

連続エクスポートジョブを無効または有効にします。 無効な連続エクスポートは実行されませんが、その現在の状態は永続化され、連続エクスポートが有効になっている場合は再開できます。 

長期間無効になっている連続エクスポートを有効にすると、エクスポートが無効になっているときに、エクスポートは最後に停止した場所から続行されます。 この継続は、すべてのプロセスを処理するのに十分なクラスター容量がない場合に、実行時間の長いエクスポートが発生し、他のエクスポートの実行がブロックされる可能性があります。 連続エクスポートは、最後の実行時に昇順で実行されます (キャッチアップが完了するまで、最も古いエクスポートが最初に実行されます)。 

## <a name="syntax"></a>構文

`.enable` `continuous-export` *ContinuousExportName* 

`.disable` `continuous-export` *ContinuousExportName* 

## <a name="properties"></a>Properties

| プロパティ             | Type   | 説明                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連続エクスポートの名前 |

## <a name="output"></a>出力

変更された連続エクスポートの[連続エクスポートの表示コマンド](show-continuous-export.md)の結果。 
