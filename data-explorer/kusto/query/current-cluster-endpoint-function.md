---
title: current_cluster_endpoint ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの current_cluster_endpoint () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2c3ddbee55e729ae8afbb6c1fbcc213bd6bfd9ce
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348719"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

照会されている現在のクラスターのネットワークエンドポイント (DNS 名) を返します。

## <a name="syntax"></a>構文

`current_cluster_endpoint()`

## <a name="returns"></a>戻り値

クエリ対象の現在のクラスターのネットワークエンドポイント (DNS 名) を型の値として指定し `string` ます。

## <a name="example"></a>例

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```