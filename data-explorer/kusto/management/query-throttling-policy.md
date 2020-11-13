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
ms.openlocfilehash: 0f50170e3ccfab20d434a90188baf511de056cef
ms.sourcegitcommit: 541164d8745022906b1da4aaca41ec15393f85d5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570567"
---
# <a name="query-throttling-policy"></a>クエリ調整ポリシー

クエリの制限ポリシーを定義して、同時にクラスターが実行できる同時実行クエリの数を制限します。 このポリシーにより、クラスターは、維持できるよりも多くの同時実行クエリで過負荷になることがなくなります。 ポリシーは実行時に変更でき、alter policy コマンドが完了するとすぐに実行されます。

* [`.show cluster policy querythrottling`](query-throttling-policy-commands.md#show-cluster-policy-querythrottling)クラスターの現在のクエリ制限ポリシーを表示するには、を使用します。
* [`.alter cluster policy querythrottling`](query-throttling-policy-commands.md#alter-cluster-policy-querythrottling)クラスターの現在のクエリ制限ポリシーを設定するには、を使用します。
* [`.delete cluster policy querythrottling`](query-throttling-policy-commands.md#delete-cluster-policy-querythrottling)クラスターの現在のクエリの制限のポリシーを削除するには、を使用します。

> [!NOTE]
> 詳細なテストを行わずに、クエリの調整ポリシーを変更しないでください。 クラスターリソースが負荷によってスケールされていない場合、変更によってパフォーマンスが低下する可能性があり、その結果、クエリが失敗することがあります。 
