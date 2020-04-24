---
title: Kusto インジェスト クライアント ライブラリ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで Kusto INGEST クライアント ライブラリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0fd86579f4ca6471903305ca9249b78bc03b8cdf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503130"
---
# <a name="kusto-ingest-client-library"></a>Kusto インジェスト クライアント ライブラリ

## <a name="overview"></a>概要
Kusto.Ingest ライブラリは、Kusto サービスにデータを送信できるようにする .NET 4.6.2 ライブラリです。
Kusto.Ingest は、次のライブラリと SDK に依存します。

* AAD 認証の ADAL
* Azure ストレージ クライアント
* [TBD: 外部依存関係の完全なリスト]

Kusto インジェスト メソッドは[IKustoIngestClient](kusto-ingest-client-reference.md#interface-ikustoingestclient)インターフェイスによって定義され、同期モードと非同期モードの両方でストリーム、IDataReader、ローカル ファイル、および Azure BLOB からデータを取り込むことができます。

## <a name="ingest-client-flavors"></a>クライアントフレーバーの取り込み
概念的には、Ingest クライアントにはキューイングとダイレクトの 2 つの基本的なフレーバーがあります。

### <a name="queued-ingestion"></a>キューイングインジェスティション
によって定義される[IKustoQueuedIngest クライアント](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)このモードは、Kusto サービスに対するクライアント コードの依存関係を制限します。 取り込みは、Kusto インジェスト メッセージを Azure キューにポストして実行し、その後、Kusto データ管理 (インジェスト) サービスから取得します。 すべての中間ストレージ成果物は、Kusto データ管理サービスによって割り当てられたリソースを使用して、INGEST クライアントによって作成されます。

**キューモードの利点**は次のとおりです(ただし、これらに限定されません)。

* Kusto エンジン サービスからのデータ取り込みプロセスの分離
* Kusto Engine (またはインジェスト) サービスが利用できない場合に、インジェスト要求を永続化できます。
* インジェスション サービスによるインバウンド データの効率的で制御可能な集約が可能で、パフォーマンスの向上
* Kusto インジェスト サービスが Kusto Engine サービスのインジェスト負荷を管理できるようにします。
* Kusto インジェスト サービスは、一時的なインジェストの失敗 (たとえば、XStore の調整) に必要に応じて再試行します。
* すべてのインジェスト要求の進行状況と結果を追跡するための便利なメカニズムを提供します。

次の図は、キューイングインジェスト クライアントと Kusto との対話の概要を示しています。

![alt text](../images/queued-ingest.jpg "キューイングインジェスト")

### <a name="direct-ingestion"></a>直接摂取
IKustoDirectIngest クライアントによって定義され、このモードは Kusto エンジン サービスとの直接の相互作用を強制します。 このモードでは、Kusto インジェスト サービスはデータを管理または管理しません。 ダイレクトモードでのインジェスト要求は、最終的には`.ingest`Kusto Engine サービスで直接実行されるコマンドに変換されます。
次の図は、Kusto との直接取り込みクライアントの対話の概要を示しています。

![alt text](../images/direct-ingest.jpg "直接摂取")

> [!NOTE]
> 直接モードは、プロダクション グレードの取り込みソリューションには推奨されません。

**ダイレクトモードの利点**は次のとおりです。

* 待機時間が短い (集計なし)。 ただし、キューイング インジェスティションを使用すると、低遅延も実現できます。
* 同期メソッドを使用する場合、メソッドの完了はインジェスト操作の終了を示します。

**ダイレクトモードの欠点**は次のとおりです。

* クライアント コードは、再試行またはエラー処理ロジックを実装する必要があります。
* Kustoエンジンサービスが利用できない場合、インジェクションは不可能です
* クライアント コードは、エンジン サービスの容量を認識していないので、取り込み要求で Kusto Engine サービスを圧倒する可能性があります。

## <a name="ingestion-best-practices"></a>インジェクションのベスト プラクティス

### <a name="general"></a>全般
[取り込みベストプラクティスは](kusto-ingest-best-practices.md)、取り込み時にCGとスループットPOVを提供します。

### <a name="thread-safety"></a>スレッド セーフ
Kusto Ingest クライアントの実装はスレッド セーフで、再利用することを意図しています。 各インジェスト操作ごとにクラスの`KustoQueuedIngestClient`インスタンスを作成する必要はありません。 のインスタンス`KustoQueuedIngestClient`は、ユーザー プロセスごとにターゲット Kusto クラスターごとに必要です。 複数のインスタンスを実行すると、逆効果になり、データ管理クラスターを DoS できます。

### <a name="supported-data-formats"></a>サポートされるデータ形式
ネイティブ インジェストを使用する場合、まだそこにない場合は、データを 1 つ以上の Azure Storage BLOB にアップロードします。 現在サポートされている BLOB 形式については、「[サポートされているデータ形式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)」に記載されています。

### <a name="schema-mapping"></a>スキーマ マッピング
[スキーマ マッピングは、](../../management/mappings.md)変換元データ フィールドを変換先テーブルの列に明確にバインドするのに役立ちます。

## <a name="usage-and-further-reading"></a>使用法とさらなる読み取り

* 上記のように、Kusto の持続可能で高規模のインジェスト ソリューションの推奨される基礎は **、KustoQueuedIngestClient である**必要があります。
* Kusto サービスでの不要な負荷を最小限に抑えるには、Kusto Ingest クライアント (キューに入れるまたは直接) の単一のインスタンスを Kusto クラスタごとにプロセスごとに使用することをお勧めします。 Kusto Ingest クライアントの実装はスレッド セーフで完全に再入可能です。

### <a name="ingestion-permissions"></a>取り込み権限
* [Kusto インジェストのアクセス許可](kusto-ingest-client-permissions.md)は Kusto.Ingest パッケージを使用して正常に取り込むために必要なアクセス許可の設定を説明します。

### <a name="kustoingest-library-reference"></a>Kusto.Ingest ライブラリ リファレンス
* [Kusto.Ingest クライアント リファレンス](kusto-ingest-client-reference.md)には、Kusto INGEST クライアント インターフェイスと実装の完全なリファレンスが含まれています。<BR>インジェスト クライアントの作成方法、インジェスト要求の強化、取り込み処理の進捗管理などについての情報が記載されています。
* [Kusto.Ingest 操作ステータス](kusto-ingest-client-status.md)は、インジェストステータスを追跡するための**クストーキューイングインジェストクライアント**機能を説明します
* [Kusto.Ingest エラーは](kusto-ingest-client-errors.md)、クストインジェストクライアントのエラーと例外を文書化します。
* [Kusto.Ingest の例は](kusto-ingest-client-examples.md)、クストにデータを取り込むさまざまな手法を示すコード スニペットを示します。

### <a name="data-ingestion-rest-apis"></a>データ取り込み REST API
[Kusto.Ingest ライブラリを使用しないデータインジェストでは、Kusto.Ingest](kusto-ingest-client-rest.md)ライブラリに依存せずに Kusto REST API を使用してキューに入れる Kusto インジェストを実装する方法について説明します。

