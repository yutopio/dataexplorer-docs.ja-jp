---
title: Kusto インジェストクライアントライブラリ-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの Kusto インジェストクライアントライブラリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 03/24/2020
ms.openlocfilehash: 5770c59ff7298567cad01bb3ed4cc6a684b2378a
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373692"
---
# <a name="kusto-ingest-client-library"></a>Kusto インジェストクライアントライブラリ

## <a name="overview"></a>概要
Kusto. インジェスト library は、Kusto サービスにデータを送信できるようにする .NET 4.6.2 ライブラリです。
Kusto. インジェストは、次のライブラリと Sdk に依存します。

* AAD 認証用 ADAL
* Azure Storage クライアント

Kusto インジェストメソッドは[IKustoIngestClient](kusto-ingest-client-reference.md#interface-ikustoingestclient)インターフェイスによって定義され、同期モードと非同期モードの両方でストリーム、IDataReader、ローカルファイル、および Azure blob からのデータインジェストを可能にします。

## <a name="ingest-client-flavors"></a>クライアントのフレーバーの取り込み
概念的には、インジェストクライアントには、Queued と Direct の2つの基本的な種類があります。

### <a name="queued-ingestion"></a>キューに置かれたインジェスト
[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)により定義されます。このモードでは、Kusto サービスに対するクライアントコードの依存関係が制限されます。 インジェストは、Kusto インジェストメッセージを Azure キューに送信することで実行されます。このメッセージは、Kusto データ管理 (インジェスト) サービスから取得されます。 すべての中間ストレージアーティファクトは、Kusto データ管理サービスによって割り当てられたリソースを使用して、インジェストクライアントによって作成されます。

**キューに登録されたモードの利点**は次のとおりです (ただし、これに限定されません)。

* Kusto Engine サービスからのデータインジェストプロセスの分離
* Kusto エンジン (またはインジェスト) サービスを使用できないときにインジェスト要求を永続化することを許可します。
* インジェストサービスによる受信データの効率的で制御可能な集計を可能にし、パフォーマンスを向上させます。
* Kusto インジェストサービスが Kusto エンジンサービスのインジェスト負荷を管理できるようにします
* Kusto インジェストサービスは、一時的な取り込みエラー (XStore の制限など) で必要に応じて再試行します
* すべてのインジェスト要求の進行状況と結果を追跡するための便利なメカニズムを提供します。

次の図は、キューに格納されたインジェストクライアントと Kusto のやり取りの概要を示しています。

![alt text](../images/queued-ingest.jpg "キューに登録済み-取り込み")

### <a name="direct-ingestion"></a>直接インジェスト
IKustoDirectIngestClient により定義されます。このモードでは、Kusto エンジンサービスと直接やり取りします。 このモードでは、Kusto インジェストサービスはデータをモデレートまたは管理しません。 Direct モードでのすべてのインジェスト要求は、最終的に `.ingest` Kusto エンジンサービスで直接実行されるコマンドに変換されます。
次の図は、Kusto との直接インジェストクライアントの対話の概要を示しています。

![alt text](../images/direct-ingest.jpg "直接取り込み")

> [!NOTE]
> Direct モードは、実稼働グレードのインジェストソリューションにはお勧めできません。

**ダイレクトモードの利点**は次のとおりです。

* 低待機時間 (集計はありません)。 ただし、キューに置かれたインジェストで待機時間を短くすることもできます。
* 同期メソッドが使用されている場合、メソッドの完了時にインジェスト操作の終了が示されます。

**ダイレクトモードの短所**は次のとおりです。

* クライアントコードでは、再試行またはエラー処理のロジックを実装する必要があります。
* Kusto Engine サービスを利用できない場合、Ingestions は不可能です。
* クライアントコードでは、エンジンサービスの容量が認識されないため、インジェスト要求で Kusto Engine サービスが過負荷になる可能性があります。

## <a name="ingestion-best-practices"></a>インジェストのベストプラクティス

### <a name="general"></a>全般
[インジェストのベストプラクティス](kusto-ingest-best-practices.md)では、インジェストと、インジェストのスループットについて説明します。

### <a name="thread-safety"></a>スレッド セーフ
Kusto インジェストクライアントの実装はスレッドセーフであり、再利用が想定されています。 `KustoQueuedIngestClient`または複数の取り込み操作に対してクラスのインスタンスを作成する必要はありません。 の1つのインスタンス `KustoQueuedIngestClient` が、ターゲット Kusto クラスターごとのプロセスごとに必要です。 複数のインスタンスを実行すると、カウンターの生産性が向上し、データ管理クラスターが DoS になることがあります。

### <a name="supported-data-formats"></a>サポートされるデータ形式
ネイティブインジェストがまだ存在しない場合は、そのデータを1つ以上の Azure Storage blob にアップロードします。 現在サポートされている blob 形式については、「[サポートされるデータ形式](../../../ingestion-supported-formats.md)」を参照してください。

### <a name="schema-mapping"></a>スキーマ マッピング
[スキーママッピング](../../management/mappings.md)は、ソースデータフィールドを変換先テーブルの列に確定的にバインドするのに役立ちます。

## <a name="usage-and-further-reading"></a>使用方法と参考資料

* 前述のように、Kusto 向けの持続可能な高スケールインジェストソリューションの推奨される基準は、 **KustoQueuedIngestClient**である必要があります。
* Kusto サービスの不要な負荷を最小限に抑えるには、kusto インジェスト (キューに登録またはダイレクト) の1つのインスタンスを、Kusto クラスターごとのプロセスごとに使用することをお勧めします。 Kusto インジェストクライアントの実装は、スレッドセーフで完全再入可能です。

### <a name="ingestion-permissions"></a>インジェストアクセス許可
* [Kusto インジェストのアクセス許可](kusto-ingest-client-permissions.md)については、kusto インジェストパッケージを使用してインジェストを成功させるために必要なアクセス許可の設定について説明します

### <a name="kustoingest-library-reference"></a>Kusto. インジェストライブラリリファレンス
* [Kusto. インジェストクライアントリファレンス](kusto-ingest-client-reference.md)には、kusto インジェストクライアントインターフェイスおよび実装の完全なリファレンスが含まれています。<BR>ここでは、インジェストクライアントを作成する方法、インジェスト要求を強化する方法、インジェストの進行状況を管理する方法などについて説明します。
* [Kusto. 取り込み操作の状態](kusto-ingest-client-status.md)について、インジェストの状態を追跡するための**KustoQueuedIngestClient**機能について説明します。
* [Kusto. インジェストエラー](kusto-ingest-client-errors.md)ドキュメント Kusto インジェストクライアントのエラーと例外
* [Kusto. インジェストの例](kusto-ingest-client-examples.md)では、kusto にデータを取り込みするさまざまな手法を示すコードスニペットを紹介しています。

### <a name="data-ingestion-rest-apis"></a>データインジェスト REST Api
[Kusto. インジェストライブラリを使用しないデータインジェスト](kusto-ingest-client-rest.md): KUSTO の REST api を使用して、kusto インジェストライブラリに依存せずに、キューに登録された kusto インジェストを実装する方法について説明します。
