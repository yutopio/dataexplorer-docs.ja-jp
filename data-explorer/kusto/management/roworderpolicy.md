---
title: 行の順序ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの行注文ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 4dca1a4083a6141eb94d9bf3cf69f7134ebfca04
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520215"
---
# <a name="row-order-policy"></a>行の順序のポリシー

行順序ポリシーは、データ シャード内の行の順序に関する Kusto に "ヒント" を提供する、テーブルに対するオプションのポリシー セットです。

このポリシーの主な目的は、圧縮を改善すること (副作用の可能性があるもの) ではなく、順序付けられた列の値の小さなサブセットに絞り込まれると知られているクエリのパフォーマンスを向上させることです。

ポリシーの適用は、次の場合に適しています。
* 大多数のクエリは、特定の大きなディメンション列の特定の値 (「アプリケーション ID」や「テナント ID」など) にフィルター処理します。
* テーブルに取り込まれたデータは、この列に従って事前に並べ替えられる可能性は低い。

ポリシーの一部として定義できる列 (ソートキー) の量にハードコーディングされた制限は設定されていませんが、追加の各列はインジェストプロセスにオーバーヘッドを追加し、列が追加されるにつれて、有効なリターンは減少します。

> [!NOTE]
> ポリシーがテーブルに適用されると、その時点から取り込まれたデータに影響します。

行順序ポリシーを管理するための制御コマンドは[、こちらから見つけることができます。](../management/roworder-policy.md)