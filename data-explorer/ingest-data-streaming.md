---
title: ストリーミング インジェストを使用して Azure Data Explorer にデータを取り込む
description: ストリーミング インジェストを使用して、Azure Data Explorer にデータを取り込む (読み込む) 方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4bbb076b4c21ac2f93b2bdaf775d513483332934
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257962"
---
# <a name="streaming-ingestion-preview"></a>ストリーミング インジェスト (プレビュー)

ストリーミング インジェストは、多様なボリューム データでインジェスト時間が 10 秒未満の短い待機時間を必要とするときに使用します。 各テーブルへのデータ ストリームが比較的小さい (1 秒あたりのレコード数が少ない) 1 つまたは複数のデータベースで、数多くのテーブルの操作処理を最適化するために使用されますが、データ インジェスト ボリューム全体が高くなります (1 秒あたり数千レコード)。 

データ量がテーブルごとに 1 時間あたり 4 GB を超える場合は、ストリーミング インジェストではなく一括インジェストを使用します。 さまざまなインジェスト方法の詳細については、「[Azure データ エクスプローラーでのデータ インジェスト](ingest-data-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Web UI](https://dataexplorer.azure.com/) にサインインします。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)を作成する

## <a name="enable-streaming-ingestion-on-your-cluster"></a>クラスターでストリーミング インジェストを有効にする

> [!WARNING]
> ストリーミング インジェストを有効にする前に[制限事項](#limitations)を確認してください。

1. Azure portal で、Azure Data Explorer クラスターに移動します。 **[設定]** で **[構成]** を選択します。 
1. **[構成]** ウィンドウで、 **[オン]** を選択して **[ストリーミング インジェスト]** を有効にします。
1. **[保存]** を選択します。
 
    ![ストリーミング インジェストのオン](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. [Web UI](https://dataexplorer.azure.com/) で、ストリーミング データを受信するテーブルまたはデータベースに[ストリーミング インジェスト ポリシー](kusto/management/streamingingestionpolicy.md)を定義します。 

    > [!NOTE]
    > * ポリシーがデータベース レベルで定義されている場合、データベース内のすべてのテーブルでストリーミング インジェストが有効になります。
    > * 適用されるポリシーでは、新しく取り込まれたたデータのみを参照でき、データベース内の他のテーブルは参照できません。

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>ストリーミング インジェストを使用してクラスターにデータを取り込む

2 種類のストリーミング インジェストがサポートされています。

* データ ソースとして使用される[**イベント ハブ**](ingest-data-event-hub.md)。
* **カスタム インジェスト**では、Azure Data Explorer [クライアント ライブラリ](kusto/api/client-libraries.md)のいずれかを使用するアプリケーションを作成する必要があります。 サンプル アプリケーションについては、[ストリーミング インジェストのサンプル](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample)を参照してください。

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>適切なストリーミング インジェストの種類を選択する

|   |イベント ハブ  |カスタム インジェスト  |
|---------|---------|---------|
|インジェスト開始からクエリでデータが使用可能になるまでのデータ遅延   |    より長い遅延     |   より短い遅延      |
|開発のオーバーヘッド    |   高速で簡単なセットアップ、開発オーバーヘッドなし    |   アプリケーションでエラーを処理し、データの一貫性を確保するための高い開発オーバーヘッド     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>クラスターでストリーミング インジェストを無効にする

> [!WARNING]
> ストリーミング インジェストの無効化には数時間かかることがあります。

1. 関連するすべてのテーブルとデータベースから[ストリーミング インジェスト ポリシー](kusto/management/streamingingestionpolicy.md)をドロップします。 ストリーミング インジェスト ポリシーの削除によって、初期ストレージから列ストア (エクステントまたはシャード) 内の永続的なストレージへのストリーミング インジェストのデータ移動がトリガーされます。 データ移動には、初期ストレージ上のデータ量とクラスターによる CPU およびメモリ使用率に応じて、数秒から数時間かかることがあります。
1. Azure portal で、Azure Data Explorer クラスターに移動します。 **[設定]** で **[構成]** を選択します。
1. **[構成]** ウィンドウで、 **[オフ]** を選択して **[ストリーミング インジェスト]** を無効にします。
1. **[保存]** を選択します。

    ![ストリーミング インジェストのオフ](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>制限事項

* データベース自体またはそのいずれかのテーブルに[ストリーミング インジェスト ポリシー](kusto/management/streamingingestionpolicy.md)が定義され、有効になっている場合、データベースで[データベース カーソル](kusto/management/databasecursor.md)はサポートされません。
* [データ マッピング](kusto/management/mappings.md)は、ストリーミング インジェストで使用するために[事前に作成](kusto/management/create-ingestion-mapping-command.md)する必要があります。 個々のストリーミング インジェスト要求は、インライン データ マッピングには対応していません。
* ストリーミング インジェストのパフォーマンスと容量は、VM とクラスターのサイズを増やして拡張されます。 同時インジェスト要求の数は、コアあたり 6 個に制限されています。 たとえば、D14 や L16 などの 16 コアの SKU の場合、サポートされる最大負荷は 96 の同時インジェスト要求です。 D11 などの 2 コアの SKU の場合、サポートされる最大負荷は 12 の同時インジェスト要求です。
* ストリーミング インジェスト要求のデータ サイズの制限は 4 MB です。
* スキーマの更新は、ストリーミング インジェスト サービスで利用できるようになるまでに最大 5 分かかる場合があります。 これらの更新の例としては、テーブルおよびインジェスト マッピングの作成と変更があります。 
* クラスターでストリーミング インジェストを有効にすると、データがストリーミング経由で取り込まれていない場合でも、インジェスト データをストリーミングするためにクラスター マシンのローカル SSD ディスクの一部を使用して、ホット キャッシュに使用できるストレージを減らします。
* [extent タグ](kusto/management/extents-overview.md#extent-tagging)は、ストリーミング インジェスト データには設定できません。

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer でデータのクエリを実行する](web-query-data.md)
