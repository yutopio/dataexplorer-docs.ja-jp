---
title: クエリ調整ポリシー-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのクエリ調整ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 7d6db9ba7cbb7b1fbaaee7325043fed287673178
ms.sourcegitcommit: acd8aba5ed957bee4a7361057387145f9d3ef4e3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91734029"
---
# <a name="query-throttling-policy"></a>クエリの調整ポリシー

クラスターで同時に実行されるクエリの量を制限するために、クエリの制限ポリシーを定義します。 ポリシーは実行時に変更でき、alter policy コマンドが完了した直後に実行されます。

* [`.show cluster policy querythrottling`](query-throttling-policy-commands.md#show-cluster-policy-querythrottling)クラスターの現在のクエリ制限ポリシーを表示するには、を使用します。
* [`.alter cluster policy querythrottling`](query-throttling-policy-commands.md#alter-cluster-policy-querythrottling)クラスターの現在のクエリ制限ポリシーを設定するには、を使用します。
* [`.delete cluster policy querythrottling`](query-throttling-policy-commands.md#delete-cluster-policy-querythrottling)クラスターの現在のクエリの制限のポリシーを削除するには、を使用します。

> [!NOTE]
> クエリ制限ポリシーを定義していない場合は、多数の同時実行クエリによって、クラスターの可能性やパフォーマンスの低下が発生する可能性があります。
