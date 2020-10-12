---
title: Kusto インジェストクライアントライブラリ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Kusto インジェストクライアントライブラリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 03/18/2020
ms.openlocfilehash: a79c815202e65fa32f62a76c700d808d0fda86ea
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941997"
---
# <a name="kusto-ingest-client-library"></a>Kusto インジェストクライアントライブラリ 

`Kusto.Ingest` ライブラリは、Kusto サービスにデータを送信するための .NET 4.6.2 ライブラリです。
次のライブラリと Sdk の依存関係があります。

* Azure AD 認証用の ADAL
* Azure storage クライアント

インジェストメソッドは、 [IKustoIngestClient](kusto-ingest-client-reference.md#interface-ikustoingestclient) インターフェイスによって定義されます。  これらのメソッドは、同期モードと非同期モードの両方で、Stream、IDataReader、ローカルファイル、Azure blob からのデータインジェストを処理します。

## <a name="ingest-client-flavors"></a>クライアントのフレーバーの取り込み

インジェストクライアントには、Queued と Direct の2つの基本的な種類があります。

### <a name="queued-ingestion"></a>キューによるインジェスト

[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)で定義されたキューインジェストモードは、Kusto サービスに対するクライアントコードの依存関係を制限します。 インジェストを行うには、Kusto インジェストメッセージを Azure キューに送信します。このメッセージは、Kusto データ管理 (インジェスト) サービスから取得されます。 すべての中間ストレージ項目は、Kusto データ管理サービスのリソースを使用して、インジェストクライアントによって作成されます。

**キューに登録されたモードの利点は次のとおりです。**

* Kusto エンジンサービスからデータインジェストプロセスを分離する
* Kusto エンジン (またはインジェスト) サービスを使用できないときにインジェスト要求を永続化できるようにします
* インジェストサービスによる受信データの効率的で制御可能な集計により、パフォーマンスが向上します。 
* Kusto インジェストサービスで Kusto エンジンサービスのインジェスト負荷を管理できるようにします
* 必要に応じて、(XStore の制限などの) 一時的な取り込みエラーが発生したときに、Kusto インジェストサービスを再試行します。
* すべてのインジェスト要求の進行状況と結果を追跡するための便利なメカニズムを提供します。

次の図は、キューに格納されたインジェストクライアントと Kusto のやり取りの概要を示しています。

:::image type="content" source="../images/about-kusto-ingest/queued-ingest.png" alt-text="クエリのインジェストモードで kusto サービスにクエリを送信する方法を示す図。":::
 
### <a name="direct-ingestion"></a>直接インジェスト

IKustoDirectIngestClient で定義されているダイレクトインジェストモードは、Kusto エンジンサービスと直接やり取りします。 このモードでは、Kusto インジェストサービスがデータを適度に処理したり管理したりすることはありません。 すべてのインジェスト要求は最終的に、 `.ingest` Kusto エンジンサービスで直接実行されるコマンドに変換されます。

次の図は、Kusto との直接インジェストクライアントの対話の概要を示しています。

:::image type="content" source="../images/about-kusto-ingest/direct-ingest.png" alt-text="クエリのインジェストモードで kusto サービスにクエリを送信する方法を示す図。":::

> [!NOTE]
> 実稼働グレードのインジェストソリューションでは、Direct モードは推奨されません。

**ダイレクトモードの利点は次のとおりです。**

* 待機時間が短く、集計がありません。 ただし、キューに置かれたインジェストで待機時間を短くすることもできます。
* 同期メソッドが使用されている場合、メソッドの完了時にインジェスト操作の終了が示されます。

**ダイレクトモードの短所は次のとおりです。**

* クライアントコードでは、再試行またはエラー処理のロジックを実装する必要があります。
* Kusto Engine サービスを利用できない場合、Ingestions は不可能です。
* クライアントコードでは、エンジンサービスの容量が認識されないため、インジェスト要求で Kusto Engine サービスが過負荷になる可能性があります。

## <a name="ingestion-best-practices"></a>インジェストのベストプラクティス

[インジェストのベストプラクティス](kusto-ingest-best-practices.md) では、インジェストと、インジェストのスループットについて説明します。

* **スレッドセーフ-** Kusto インジェストクライアントの実装はスレッドセーフであり、再利用が想定されています。 `KustoQueuedIngestClient`または複数のインジェスト操作に対してクラスのインスタンスを作成する必要はありません。 の1つのインスタンス `KustoQueuedIngestClient` が、ターゲット Kusto クラスターごとのプロセスごとに必要です。 複数のインスタンスを実行すると、カウンターの生産性が向上し、データ管理クラスターで DoS が発生する可能性があります。

* **サポートされているデータ形式-** ネイティブインジェストを使用する場合は、データを1つ以上の Azure storage blob にアップロードします (まだ存在しない場合)。 現在サポートされている blob 形式については、 [サポートされるデータ形式](../../../ingestion-supported-formats.md)に関するドキュメントをご覧ください。

* **スキーママッピング-** 
[スキーママッピング](../../management/mappings.md)は、ソースデータフィールドを変換先テーブルの列に確定的にバインドするのに役立ちます。

* **インジェストアクセス許可-** 
[Kusto インジェストのアクセス許可](kusto-ingest-client-permissions.md)では、パッケージを使用した取り込みを成功させるために必要なアクセス許可の設定について説明 `Kusto.Ingest` します。

* **使用状況-** 前述のように、Kusto の持続可能な高スケールのインジェストソリューションの推奨される基準は、 **KustoQueuedIngestClient**である必要があります。
Kusto サービスの不要な負荷を最小限に抑えるために、kusto クラスターごとに、プロセスごとにクライアント (キューに登録またはダイレクト) を取り込むために kusto 1 つのインスタンスを使用することをお勧めします。 Kusto インジェストクライアントの実装は、スレッドセーフで、完全再入可能です。

## <a name="next-steps"></a>次のステップ

* [Kusto. インジェストクライアントリファレンス](kusto-ingest-client-reference.md) には、kusto インジェストクライアントインターフェイスおよび実装の完全なリファレンスが含まれています。 インジェストクライアントを作成する方法、インジェスト要求を補強する方法、インジェストの進行状況を管理する方法などについて説明します。

* [Kusto. 取り込み操作の状態](kusto-ingest-client-status.md) について、インジェストステータスを追跡するための KustoQueuedIngestClient 機能について説明します。

* [Kusto. インジェストエラー](kusto-ingest-client-errors.md) は、クライアントのエラーと例外を取り込む kusto を説明します。

* [Kusto. インジェストの例](kusto-ingest-client-examples.md) では、kusto にデータを取り込みするさまざまな手法を示すコードスニペットを示しています。

* [Kusto. インジェストライブラリを使用しないデータインジェスト](kusto-ingest-client-rest.md)では、キューに登録された kusto インジェストを実装し、KUSTO REST api を使用してライブラリに依存しないようにする方法について説明します。 `Kusto.Ingest`

