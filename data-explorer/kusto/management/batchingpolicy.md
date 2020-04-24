---
title: インジェクションバッチ処理ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのインジェスティション バッチ処理ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: fa3a4cfd512e3e37ba6b6abf8f8316d6ff12fdec
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522238"
---
# <a name="ingestionbatching-policy"></a>インジェスティションバッチ処理ポリシー

## <a name="overview"></a>概要

取り込みプロセス中に Kusto は、小さな入力データチャンクを一緒にバッチ処理して、インジェストを待つ間にスループットの最適化を試みます。
この種のバッチ処理では、インジェスト プロセスで消費されるリソースが削減されるだけでなく、バッチ処理以外のインジェストによって生成される小さなデータ シャードを最適化するために、取り込み後のリソースは必要ありません。

ただし、強制遅延が発生する前にバッチ処理を実行する場合、クエリの準備が整うまでデータの取り込み要求から終了までの時間が長くなるという欠点があります。

このトレードオフを制御できるようにするために、ポリシーを使用することができます`IngestionBatching`。
このポリシーはキューに入れ込みにのみ適用され、小さな BLOB をまとめてバッチ処理するときに許容される最大強制遅延を提供します。

## <a name="details"></a>詳細

上記で説明したように、一括で摂取されるデータの最適なサイズがあります。
現在、このサイズは、圧縮されていないデータの約 1 GB です。 最適なサイズよりもはるかに少ないデータを保持する BLOB で行われるインジェストは最適ではないため、キューに入れる場合、Kusto はそのような小さな BLOB をまとめてバッチ処理します。 バッチ処理は、最初の条件が真になるまで実行されます。

1. バッチ処理されたデータの合計サイズが最適なサイズに達した場合、
2. ポリシーで許可される最大遅延時間、合計サイズ、または BLOB の`IngestionBatching`数に達した

ポリシー`IngestionBatching`は、データベースまたはテーブルに設定できます。 既定では、ポリシーが定義されていない場合、Kusto は、バッチ処理の最大遅延時間 **、1000**項目、合計サイズ**1G**として 5**分**の既定値を使用します。

> [!WARNING]
> このポリシーを設定するお客様は、まず Kusto ops チームに連絡することをお勧めします。 このポリシーを非常に小さな値に設定すると、クラスタの COGS が増加し、パフォーマンスが低下します。 さらに、この値を減らすと、複数のインジェスト プロセスを並行して管理するオーバーヘッドが生じるため、実際にはエンド ツー エンドのインジェスト遅延が**増加**する可能性があります。

## <a name="additional-resources"></a>その他の技術情報

* [取り込みバッチ処理ポリシー コマンド リファレンス](../management/batching-policy.md)
* [インジェクションのベスト プラクティス - スループットの最適化](../api/netfx/kusto-ingest-best-practices.md#optimizing-for-throughput)