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
ms.openlocfilehash: 33168f001dd3d998fbf82169c7637e2eb390e0bc
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423062"
---
## <a name="limitations"></a>制限事項

* データベース自体またはそのいずれかのテーブルに[ストリーミング インジェスト ポリシー](../kusto/management/streamingingestionpolicy.md)が定義され、有効になっている場合、データベースで[データベース カーソル](../kusto/management/databasecursor.md)はサポートされません。
* [データ マッピング](../kusto/management/mappings.md)は、ストリーミング インジェストで使用するために[事前に作成](../kusto/management/create-ingestion-mapping-command.md)する必要があります。 個々のストリーミング インジェスト要求は、インライン データ マッピングには対応していません。
* ストリーミング インジェストのパフォーマンスと容量は、VM とクラスターのサイズを増やして拡張されます。 同時インジェスト要求の数は、コアあたり 6 個に制限されています。 たとえば、D14 や L16 などの 16 コアの SKU の場合、サポートされる最大負荷は 96 の同時インジェスト要求です。 D11 などの 2 コアの SKU の場合、サポートされる最大負荷は 12 の同時インジェスト要求です。
* ストリーミング インジェスト要求のデータ サイズの制限は 4 MB です。
* テーブルとインジェスト マッピングの作成や変更など、スキーマの更新には、ストリーミング インジェスト サービスに最大 5 分かかることがあります。 詳細については、「[ストリーミング インジェストとスキーマ変更](../kusto/management/data-ingestion/streaming-ingestion-schema-changes.md)」を参照してください。
* クラスターでストリーミング インジェストを有効にすると、データがストリーミング経由で取り込まれていない場合でも、インジェスト データをストリーミングするためにクラスター マシンのローカル SSD ディスクの一部を使用して、ホット キャッシュに使用できるストレージを減らします。
* [extent タグ](../kusto/management/extents-overview.md#extent-tagging)は、ストリーミング インジェスト データには設定できません。
* データベースのいずれかのテーブルでストリーミング インジェストが使用されている場合、そのデータベースを[フォロワー データベース](../follower.md)のリーダーとして、または Azure Data Share の[データ プロバイダー](../data-share.md#data-provider---share-data)として使用することはできません。
