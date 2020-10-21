---
title: current_cluster_endpoint ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの current_cluster_endpoint () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfd35837b0c1affbb2101e6c71a4fa638ef8e466
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252566"
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