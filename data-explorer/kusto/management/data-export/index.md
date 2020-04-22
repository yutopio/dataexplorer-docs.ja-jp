---
title: データ エクスポート - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer のデータ エクスポートについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/09/2019
ms.openlocfilehash: e779571251baa6e87953e546d71adb98e7cfde61
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490397"
---
# <a name="data-export"></a>データのエクスポート

データ エクスポートとは、Kusto のクエリを実行し、その結果を書き込んで、クエリ結果を後の検査で使用できるようにするプロセスです。

データ エクスポートにはいくつかの方法があります。

* **クライアント側のエクスポート**: 最も単純な形式では、クライアント側でデータ エクスポートを実行できます (クライアントはサービスに対してクエリを実行し、結果を読み取り、その後にそれらを書き込みます)。 この形式のデータ エクスポートは、クライアント ツールに依存して、通常、ツールが実行されているローカル ファイルシステムへのエクスポートを実行します。 このモデルをサポートするツールとしては、[Kusto.Explorer](../../tools/kusto-explorer.md)、[Web UI](https://docs.microsoft.com/azure/data-explorer/web-query-data) 


 などがあります。

* **サービス側のエクスポート (プル)** : エクスポートのターゲットが (クエリと同じまたは別のクラスター/データベース上にある) Kusto テーブルの場合は、ターゲット テーブルで "クエリからの取り込み" フローを使用します。 このフローでは、クエリが実行されて、その結果がすぐに Kusto テーブルに取り込まれます。 「[データ インジェスト](../data-ingestion/index.md)」を参照してください。



* **サービス側のエクスポート (プッシュ)** : クエリの結果は、クエリを実行するプロデューサーとその結果を書き込むコンシューマー間の 1 つのネットワーク接続を介してストリーミングする必要があるため、上記の方法は多少制限されています。 スケーラブルなデータ エクスポートについては、Kusto は "プッシュ" エクスポート モデルを用意しています。このモデルでは、クエリを実行しているサービスが、最適化された方法でその結果の書き込みも行います。 このモデルは、一連の `.export` 制御コマンドを通じて公開され、[外部テーブル](export-data-to-an-external-table.md)、[SQL テーブル](export-data-to-sql.md)、または[外部 BLOB ストレージ](export-data-to-storage.md)へのクエリ結果のエクスポートをサポートしています。
  
  サービス側のエクスポート コマンドは、クラスターの使用可能なデータ エクスポート容量によって制限されます。 
  [show capacity コマンド](../../management/diagnostics.md#show-capacity)を実行すると、クラスターの合計、消費済み、および残りのデータ エクスポート容量を表示できます。

## <a name="recommendations-for-secret-management-when-using-data-export-commands"></a>データ エクスポート コマンドを使用する場合のシークレットの管理に関する推奨事項

リモート ターゲット (Azure Blob Storage や Azure SQL Database など) へのデータのエクスポートは、データ エクスポート コマンドを実行するセキュリティ プリンシパルの資格情報を暗黙的に使用して行われるのが理想的です。 一部のシナリオでは、これは実行できません (たとえば、Azure Blob Storage はセキュリティ プリンシパルの概念をサポートしておらず、独自のトークンのみをサポートしています)。そのため、Kusto では、必要な資格情報をデータ エクスポート制御コマンドの一部としてインラインで導入することをサポートしています。 これが安全な方法で行われるようにするためのいくつかの推奨事項を次に示します。

Kusto にシークレットを送信するときに、[難読化された文字列リテラル](../../query/scalar-data-types/string.md#obfuscated-string-literals) (`h@"..."` など) を使用します。
Kusto は、これらのシークレットをスクラブして、内部で出力されるどのトレースにも表示されないようにします。

さらに、パスワードや同様のシークレットは、安全に格納し、必要に応じてアプリケーションによって "プル" する必要があります。
