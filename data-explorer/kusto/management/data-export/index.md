---
title: データ エクスポート - Azure Data Explorer
description: この記事では、Azure Data Explorer のデータ エクスポートについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/09/2019
ms.openlocfilehash: e6afcf86a02f1f74fe024c1b94d7f9458a72a4e7
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763427"
---
# <a name="data-export"></a>データのエクスポート

データ エクスポートは、Kusto クエリを実行し、その結果を書き込むプロセスです。 クエリ結果は、後の検査で使用できます。

データ エクスポートにはいくつかの方法があります。

## <a name="client-side-export"></a>クライアント側のエクスポート
  最も単純な形式では、クライアント側でデータ エクスポートを行うことができます。 クライアントは、サービスに対してクエリを実行し、結果を読み取り、それを書き込みます。
この形式のデータ エクスポートは、クライアント ツールに依存して、(通常は、ツールが実行されているローカル ファイルシステムへの) エクスポートを実行します。 このモデルをサポートするツールとしては、[Kusto.Explorer](../../tools/kusto-explorer.md) と [Web UI](../../../web-query-data.md) です。

## <a name="service-side-export-pull"></a>サービス側のエクスポート (プル)
  エクスポートのターゲットがクエリと同じまたは別のクラスターやデータベース内のテーブルの場合は、ターゲット テーブルで "クエリからの取り込み" を使用します。 このフローでは、クエリが実行されて、その結果がすぐにテーブルに取り込まれます。 詳細については、[クエリからの取り込み](../../management/data-ingestion/ingest-from-query.md)に関する記事をご覧ください。

## <a name="service-side-export-push"></a>サービス側のエクスポート (プッシュ)
  上記の[クライアント側のエクスポート](#client-side-export)と[サービス側のエクスポート (プル)](#service-side-export-pull) は制限されています。 クエリ結果は、クエリを実行するプロデューサーとその結果を書き込むコンシューマーとの間の 1 つのネットワーク接続を介してストリームする必要があります。
スケーラブルなデータ エクスポートには、"プッシュ" エクスポート モデルを使用します。このモデルでは、クエリを実行しているサービスが、最適化された方法でその結果の書き込みも行います。 このモデルは、一連の `.export` 制御コマンドを通じて公開され、[外部テーブル](export-data-to-an-external-table.md)、[SQL テーブル](export-data-to-sql.md)、[外部 BLOB ストレージ](export-data-to-storage.md)へのクエリ結果のエクスポートをサポートしています。
  
  サービス側のエクスポート コマンドは、クラスターの使用可能なデータ エクスポート容量によって制限されます。
[show capacity コマンド](../../management/diagnostics.md#show-capacity)を実行すると、クラスターの合計、消費済み、残りのデータ エクスポート容量を表示できます。

## <a name="recommendations-for-secret-management-when-using-data-export-commands"></a>データ エクスポート コマンドを使用する場合のシークレットの管理に関する推奨事項

理想的なのは、Azure Blob Storage や Azure SQL Database などのリモート ターゲットにデータをエクスポートすることです。 データ エクスポート コマンドを実行するセキュリティ プリンシパルの資格情報を暗黙的に使用します。 一部のシナリオでは、この方法は使用できません。 たとえば、Azure Blob Storage はセキュリティ プリンシパルの概念をサポートしておらず、独自のトークンのみをサポートしています。
この機能では、必要な資格情報をデータ エクスポート制御コマンドの一部としてインラインで導入することをサポートしています。

安全な方法でエクスポートを行うには、次の手順を実行します。

* シークレットを送信するときに、[難読化された文字列リテラル](../../query/scalar-data-types/string.md#obfuscated-string-literals) (`h@"..."` など) を使用します。 シークレットは、内部で生成されたトレースに表示されないように削除されます。

* パスワードや同様のシークレットは、安全に格納し、必要に応じてアプリケーションによって "プル" します。
