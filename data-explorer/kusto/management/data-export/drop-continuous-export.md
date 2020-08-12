---
title: 継続的なデータエクスポートの削除-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの連続データエクスポートを削除する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: f08d8c99c242aa63e3847d3965bf9511967b60e1
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149336"
---
# <a name="drop-continuous-export"></a>連続エクスポートの削除

連続エクスポートジョブを削除します。

## <a name="syntax"></a>構文

`.drop` `continuous-export` *ContinuousExportName*

## <a name="properties"></a>Properties

| プロパティ             | Type   | 説明                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連続エクスポートの名前 |

## <a name="output"></a>出力

データベース内の残りの連続エクスポート (削除後)。 [[連続エクスポートの表示] コマンド](show-continuous-export.md)のようにスキーマを出力します。
