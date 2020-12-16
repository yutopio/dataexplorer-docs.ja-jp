---
title: Azure Data Explorer Kusto EngineV3 (プレビュー)
description: Azure Data Explorer (Kusto) EngineV3 の詳細について説明します
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/11/2020
ms.openlocfilehash: 133f01498d4cf430d7fdc2649df88186610b3954
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97388972"
---
# <a name="enginev3---preview"></a>EngineV3 - プレビュー

Kusto EngineV3 は、Azure Data Explorer の次世代ストレージおよびクエリ エンジンです。 テレメトリ、ログ、時系列データの取り込みとクエリで比類のないパフォーマンスを提供するように設計されています。

EngineV3 には、最適化された新しいストレージ形式とインデックスが含まれています。 高度なデータ統計クエリの最適化を使用して、最適なクエリ プランと Just-In-Time でコンパイルされたクエリ実行が作成されます。 また、EngineV3 ではディスク キャッシュが改善され、その結果として、クエリのパフォーマンスが現在のエンジン (EngineV2) と比較して桁違いに向上しています。 EngineV3 により、Azure Data Explorer サービスにおける次の革新の波に対応する基盤が築かれます。

EngineV3 モードで実行される Azure Data Explorer クラスターは、EngineV2 と完全に互換性があるため、データ移行の必要はありません。

> [!IMPORTANT]
> EngineV3 は、次の段階で使用可能になります。
>
> 1. パブリック プレビュー (現在の状態): ユーザーは、EngineV3 モードで新しいクラスターを作成できます。 パブリック プレビュー期間中、クラスターは SLA の対象にはならず、Azure Data Explorer マークアップに対して課金されることはありません。 インフラストラクチャのコストは、通常どおり課金されます。
> 1. 一般提供 (GA): 既定で、すべての新しいクラスターが EngineV3 モードで作成されます。 SLA は、すべての EngineV3 および EngineV2 の運用環境クラスターに適用されます。
> 1. GA 後: EngineV2 で実行されている既存のワークロードは EngineV3 に移行されます。 Azure Data Explorer マークアップ料金が再開されます。

## <a name="how-enginev3-works"></a>EngineV3 の動作のしくみ

EngineV3 は、既存の列ストア (EngineV2) および行ストア (ストリーミング インジェストに使用される) と並行して実行される追加の列ストア ストレージ エンジンです。 テーブルには、3 つすべてのストアのデータを一度に組み込むことができます。そして、この "フェデレーション" データは、ユーザーの観点からは見えません。

:::image type="content" source="media\engine-v3\engine-v3-architecture.png" alt-text="Azure Data Explorer/Kusto EngineV3 アーキテクチャの概略図":::

テーブルに取り込まれたすべてのデータは、テーブルの水平スライスであるシャードにパーティション分割されます。 通常、各シャードには数百万のレコードが含まれ、他のシャードとは別にエンコードされて、インデックスが作成されます。 この機能により、エンジンはインジェスト スループットの線形スケールを実現できます。

シャードはクラスター ノード間で均等に分散され、ローカル SSD とメモリの両方にキャッシュされます。 クエリ プランナーとクエリ エンジンにより、このシャード ディストリビューションとキャッシュの利点を活用する、高度に分散された並列クエリが作成されて実行されます。

EngineV3 では、分散クエリのこの "下部" を最適化することに重点が置かれます。

## <a name="performance"></a>パフォーマンス

パフォーマンスの改善とクエリ速度の向上は、このエンジンにおける 2 つの主要な変更点によるものです。

* **新しく改良されたシャード ストレージ形式。** EngineV2 と同様に、ストレージ形式は、非構造化 (テキスト) と半構造化のデータ型に特化した圧縮列ストアです。 EngineV3 では、これらの異なるデータ型のエンコードが向上しています。 インデックスは、粒度を上げるために再設計されており、データをスキャンせずにインデックスに基づいてクエリの一部を評価できます。
* **低レベルのシャード クエリ エンジンの再設計。** 新しいシャード クエリは、Just-In-Time で非常に効率的なマシン コードにコンパイルされるため、高速で効率的な結合クエリの評価ロジックが得られます。 クエリのコンパイルは、すべてのシャードから収集されたデータ統計を基にしており、列エンコードの詳細に合わせて調整されます。

EngineV3 のパフォーマンスへの影響は、使用されているデータセット、クエリ パターン、コンカレンシー、VM SKU によって異なります。 パフォーマンス テストでは、100 TB のデータセットが使用され、構造化、非構造化、半構造化データに対する分析を含むさまざまなシナリオが調査されました。 同じレベルのコンカレンシーで、同じハードウェア構成を使用した場合、パフォーマンスの向上は平均で約 8 倍でした。 実際のパフォーマンスの向上は、クエリとデータセットによって異なります。

## <a name="create-an-enginev3-cluster"></a>EngineV3 クラスターを作成する

EngineV3 を使用して [新しいクラスターを作成](create-cluster-database-portal.md)するには、クラスター作成画面の **[基本]** タブで、 **[Use Engine V3 preview]\(Engine V3 プレビューを使用する\)** チェックボックスを選択します。

:::image type="content" source="media/engine-v3/create-new-cluster-v3.png" alt-text="クラスター作成時の [Use Engine V3 preview]\(Engine V3 プレビューを使用する\) チェックボックスを示すスクリーンショット":::

## <a name="next-steps"></a>次のステップ

[Azure Data Explorer を使用してデータを取り込む](ingest-data-overview.md)
