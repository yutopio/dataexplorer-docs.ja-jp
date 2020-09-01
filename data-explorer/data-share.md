---
title: Azure Data Share を使用して Azure Data Explorer とデータを共有する
description: Azure Data Explorer と Azure Data Share でデータを共有する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: 01c8edee98a8f44ae4ad480cb14a327c4edcee83
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88873187"
---
# <a name="use-azure-data-share-to-share-data-with-azure-data-explorer"></a>Azure Data Share を使用して Azure Data Explorer とデータを共有する

ファイル共有、FTP、電子メール、API など、従来からデータを共有するたくさんの方法があります。 これらの方法では、チームや組織間でデータを移動するデータ パイプラインを両者が作成して維持する必要があります。 Azure Data Explorer を使用すると、会社や社外のパートナーのユーザーとデータを簡単かつ安全に共有できます。 共有はほぼリアルタイムで行われ、データ パイプラインの構築や管理は必要はありません。 スキーマやデータを含む、プロバイダー側でのすべてのデータベースの変更がコンシューマー側ですぐに使用できるようになります。

[![Azure Friday のビデオ](https://img.youtube.com/vi/Q3MJv90PegE/0.jpg)](https://www.youtube.com/watch?v=Q3MJv90PegE?&autoplay=1)

Azure Data Explorer では、ストレージとコンピューティングが分離されているため、お客様は複数のコンピューティング (読み取り専用) インスタンスを基になる同じストレージで実行できます。 データベースを[フォロワー データベース](follower.md)としてアタッチできます。これは、リモート クラスター上の読み取り専用データベースです。

## <a name="configure-data-sharing"></a>データ共有を構成する 

データ共有は、次の方法のいずれかを使用して構成できます。

* [Azure Data Share](/azure/data-share/) を使用して、会社全体または外部のパートナーや顧客との招待と共有を送信および管理します。 Azure Data Share では、[フォロワー データベース](follower.md)を使用して、プロバイダーとコンシューマーの Azure Data Explorer クラスターの間にシンボリック リンクが作成されます。 このオプションを使用すると、Azure Data Explorer クラスターおよびその他のデータ サービス全体のすべてのデータ共有を 1 つのウィンドウで表示および管理できます。 Azure Data Share を使用すると、異なる Azure Active Directory テナント内の組織間でデータを共有することもできます。
* [フォロワー データベース](follower.md)を直接構成するのは、レポートのニーズに合わせてスケールアウトする追加のコンピューティングが必要であり、あなたが両方のクラスターの管理者であるシナリオに限られます。

> [!Note] 
> 共有関係が確立されると、Azure Data Share によって、プロバイダーとコンシューマーの Azure Data Explorer クラスターの間にシンボリック リンクが作成されます。 データ プロバイダーがアクセスを取り消すと、シンボリック リンクが削除され、データ コンシューマーは共有データベースを使用できなくなります。

:::image type="content" source="media/data-share/adx-datashare-image.png" alt-text="Azure Data Explorer でのデータ共有":::

データ プロバイダーは、データベース レベルまたはクラスター レベルでデータを共有できます。 データベースを共有するクラスターはリーダー クラスターであり、共有を受け取るクラスターはフォロワー クラスターです。 フォロワー クラスターは、1 つ以上のリーダー クラスター データベースをフォローすることができます。 フォロワー クラスターは、変更を確認するために定期的に同期されます。 リーダーとフォロワーの間の時間差は、メタデータとデータの全体的なサイズによって、数秒から数分になります。 データはコンシューマー クラスターにキャッシュされ、読み取りまたはクエリ操作でのみ使用できますが、ホット キャッシュ ポリシーとデータベースのアクセス許可の上書きは例外です。 フォロワー クラスターで実行されているクエリではローカル キャッシュは使用され、リーダー クラスターのリソースは使用されません。

## <a name="prerequisites"></a>前提条件

1. Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
1. リーダーとフォロワー用の[プロバイダー クラスターと DB を作成](create-cluster-database-portal.md)します。
1. 「[データ取り込みの概要](ingest-data-overview.md)」に記載されているさまざまな方法の 1 つを使用して、リーダー データベースに[データを取り込み](ingest-sample-data.md)ます。

## <a name="data-provider---share-data"></a>データ プロバイダー - データを共有する

ビデオの説明に従って、データ共有アカウントを作成し、データセットを追加して、招待状を送信します。

[![データ プロバイダー - データを共有する](https://img.youtube.com/vi/QmsTnr90_5o/0.jpg)](https://youtu.be/QmsTnr90_5o?&autoplay=1)

## <a name="data-consumer---receive-data"></a>データ コンシューマー - データの受信

ビデオの説明に従って、招待を受け入れ、データ共有アカウントを作成して、コンシューマー クラスターにマップします。

[![データ コンシューマー - データの受信](https://img.youtube.com/vi/vBq6iFaCpdA/0.jpg)](https://youtu.be/vBq6iFaCpdA?&autoplay=1)

データ コンシューマーは、Azure Data Explorer クラスターにアクセスして、共有データベースへのアクセス許可をユーザーに付与し、データにアクセスできるようになりました。 ソースの Azure Data Explorer クラスターにバッチ モードを使用して取り込まれたデータは、数秒から数分以内にターゲット クラスターに表示されます。

## <a name="limitations"></a>制限事項

* [フォロワー DB の制限事項](follower.md#limitations)

## <a name="next-steps"></a>次のステップ

* [Azure Data Share のドキュメント](/azure/data-share/)
* フォロワー クラスターの詳細については、「[フォロワー クラスター](follower.md)」を参照してください。
