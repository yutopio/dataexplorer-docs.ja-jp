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
ms.openlocfilehash: 1ee658d2c16bcf4174bb82d61ddc8c9b2d7d126f
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423226"
---
## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>ストリーミング インジェストを使用してクラスターにデータを取り込む

2 種類のストリーミング インジェストがサポートされています。

* [**イベント ハブ**](../ingest-data-event-hub.md)または[**IoT Hub**](../ingest-data-iot-hub.md)。これはデータ ソースとして使用されます。
* **カスタム インジェスト**では、Azure Data Explorer [クライアント ライブラリ](../kusto/api/client-libraries.md)のいずれかを使用するアプリケーションを作成する必要があります。 サンプル アプリケーションについては、[ストリーミング インジェストのサンプル](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample)を参照してください。

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>適切なストリーミング インジェストの種類を選択する

|条件|イベント ハブ|カスタム インジェスト|
|---------|---------|---------|
|インジェスト開始からクエリでデータが使用可能になるまでのデータ遅延 | より長い遅延 | より短い遅延  |
|開発のオーバーヘッド | 高速で簡単なセットアップ、開発オーバーヘッドなし | アプリケーションでエラーを処理し、データの一貫性を確保するための高い開発オーバーヘッド |
