---
title: インクルード ファイル
description: インクルード ファイル
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 07/13/2020
ms.author: orspodek
ms.reviewer: alexefro
ms.custom: include file
ms.openlocfilehash: e6704592a5b0f1a984ea5651eb8073ac105fda3d
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423178"
---
インジェストとクエリの間で待機時間を短くする必要がある場合は、ストリーミング インジェストを使用してデータを読み込みます。 ストリーミング インジェスト操作は 10 秒以内に完了し、データは完了後すぐにクエリで使用できるようになります。 このインジェスト方法は、大量のデータを取り込む場合に適しています。たとえば、1 秒あたり数千のレコードを、数千のテーブルに分散するような場合です。 各テーブルは、1 秒あたり数レコードなど、比較的少ない量のデータを受け取ります。

取り込まれるデータ量がテーブルごとに 1 時間あたり 4 GB を超える場合は、ストリーミング インジェストではなく一括インジェストを使用します。

さまざまなインジェスト方法の詳細については、「[データ取り込みの概要](../ingest-data-overview.md)」を参照してください。
