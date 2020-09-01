---
title: Azure Data Explorer でデータ消去を使用してデバイスまたはサービスから個人データを削除する
description: データ消去を使用して Azure Data Explorer からデータを消去 (削除) する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/12/2020
ms.openlocfilehash: 6bf447a845954bde58a0308a03bee45f98f16111
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88873238"
---
# <a name="enable-data-purge-on-your-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターでのデータ消去を有効にする

[!INCLUDE [gdpr-intro-sentence](includes/gdpr-intro-sentence.md)]

Azure Data Explorer では、個々のレコードを削除する機能がサポートされています。 `.purge` コマンドを使用したデータの削除では個人データが保護されるため、他のシナリオでは使用しないでください。 それは、頻繁な削除要求や大量のデータの削除をサポートするように設計されていないため、サービスのパフォーマンスに多大な影響を与える可能性があります。

`.purge` コマンドを実行すると、完了までに数日かかる可能性のあるプロセスがトリガーされます。 `predicate` の適用対象であるレコードの "密度" が高い場合、プロセスでは、テーブル内のすべてのデータが再び取り込まれます。 このプロセスは、パフォーマンスと COGS に多大な影響を与えます。 詳細については、[Azure Data Explorer でのデータ消去](kusto/concepts/data-purge.md)に関する記事をご覧ください。

## <a name="methods-of-invoking-purge-operations"></a>消去操作の呼び出し方法 

Azure Data Explorer (Kusto) では、個々のレコードの削除とテーブル全体の消去の両方がサポートされています。 異なる使用シナリオに対応するため、`.purge` コマンドは [2 つの方法で呼び出す](kusto/concepts/data-purge.md#purge-table-tablename-records-command)ことができます。

* プログラムによる呼び出し:アプリケーションによって呼び出されることを目的とした単一の手順。 このコマンドを呼び出すと、消去実行シーケンスが直接トリガーされます。

* ユーザーによる呼び出し:独立した手順として明確な確認を必要とする 2 段階のプロセス。 コマンドを呼び出すと、検証トークンが返されます。実際の消去を実行するには、このトークンを指定する必要があります。 このプロセスでは、不適切なデータが誤って削除されるリスクが軽減されます。 大きなテーブルでこのオプションを使用すると、完了に時間がかかり、大量のコールド キャッシュ データが使用される可能性があります。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Web UI](https://dataexplorer.azure.com/) にサインインします。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)を作成する

## <a name="enable-data-purge-on-your-cluster"></a>クラスターでのデータ消去を有効にする

> [!WARNING]
> * データ消去を有効にするには、サービスの再起動が必要です。これにより、クエリの削除が発生する可能性があります。
> * データ消去を有効にする前に、[制限事項](#limitations)を確認してください。

1. Azure portal で、Azure Data Explorer クラスターに移動します。 
1. **[設定]** で **[構成]** を選択します。 
1. **[構成]** ペインで、 **[オン]** を選択して **[消去を有効にする]** を有効にします。
1. **[保存]** を選択します。
 
    ![消去をオンにする](media/data-purge-portal/enable-purge-on.png)

## <a name="disable-data-purge-on-your-cluster"></a>クラスターでのデータ消去を無効にする

1. Azure portal で、Azure Data Explorer クラスターに移動します。 
1. **[設定]** で **[構成]** を選択します。 
1. **[構成]** ペインで、 **[オフ]** を選択して **[消去を有効にする]** を無効にします。
1. **[保存]** を選択します。

    ![消去をオフにする](media/data-purge-portal/enable-purge-off.png)

## <a name="limitations"></a>制限事項

* 消去プロセスは最終処理であり、元に戻すことはできません。 このプロセスを "元に戻す" ことや、消去されたデータを回復することはできません。 したがって、[undo table drop](kusto/management/undo-drop-table-command.md) などのコマンドによって消去されたデータを回復することはできません。同様に、データを前のバージョンにロールバックしても、最後の消去の "前" の状態に戻すことはできません。
* `.purge` コマンドは、次のデータ管理エンドポイントに対して実行されます: *https://ingest- [YourClusterName].[Region].kusto.windows.net*。 このコマンドを実行するには、関連するデータベースに対する[データベース管理者](kusto/management/access-control/role-based-authorization.md)権限が必要です。 
* 消去プロセスによるパフォーマンスへの影響のため、呼び出し元には、最小限のテーブルに関連データが含まれるようにデータ スキーマを変更し、テーブルごとにバッチ コマンドを実行して、消去プロセスによる COGS への多大な影響を軽減することが期待されています。
* 消去コマンドの `predicate` パラメーターを使用して、消去するレコードを指定します。 `Predicate` のサイズは 63 KB に制限されています。 

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer でのデータ消去](kusto/concepts/data-purge.md)
