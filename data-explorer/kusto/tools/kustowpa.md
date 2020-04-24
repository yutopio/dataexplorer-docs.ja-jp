---
title: クストー WPA - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの KUSTO WPA について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 2d8c5a9b11d8045897d8a9bd798e40ceb0f48b6a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503300"
---
# <a name="kusto-wpa"></a>クストー WPA

Kusto WPA は、Windows パフォーマンス アナライザー (WPA) で Kusto クエリ結果を実行および視覚化する機能を追加します。 これは、2つの主要な部分で構成されています。

1. Kusto.Explorer`Share`&gt;`Query to Clipboard`共有クエリを受け入れ、WPA で処理されるクエリ ファイル`.kpq`( ) を生成できるランチャー ツールです。

1. 生成されたクエリファイルを「開く」ことが可能なWPA`.kpq`プラグイン( ) このようなファイルを開くと、ファイルで指定されたクエリが Kusto クラスターに自動的に送信され、結果が "標準" ETL ファイルから取得されたかのように読み込まれます。

詳細およびhttps://aka.ms/kustowpaダウンロード情報については、 [ ] を参照してください。