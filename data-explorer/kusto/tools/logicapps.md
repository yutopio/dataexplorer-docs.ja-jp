---
title: Logic Apps を使用して Kusto クエリを自動的に実行する
description: Logic Apps を使用して Kusto クエリとコマンドを自動的に実行し、スケジュールを設定する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: docohe
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: 4c918c43f748f97b3bb3f6d0342c660775c1e5c8
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342656"
---
# <a name="microsoft-logic-app-and-azure-data-explorer"></a>Microsoft Logic App と Azure データエクスプローラー

Azure Kusto Logic App コネクタを使用すると、 [Microsoft Logic app](/azure/logic-apps/logic-apps-what-are-logic-apps) コネクタを使用して、スケジュールされたタスクまたはトリガーされたタスクの一部として kusto クエリとコマンドを自動的に実行することができます。

ロジックアプリとパワー自動化は、同じコネクタ上に構築されています。 そのため、電源自動化に適用される [制限事項](../../flow.md#limitations)、 [アクション](../../flow.md#flow-actions)、 [認証](../../flow.md#authentication) 、および [使用例](../../flow-usage.md) は、Logic Apps にも適用されます。これについては、「 [power の自動化のドキュメント」ページ](../../flow.md)を参照してください。

## <a name="how-to-create-a-logic-app-with-azure-data-explorer"></a>Azure データエクスプローラーでロジックアプリを作成する方法

1. [Microsoft Azure portal](https://ms.portal.azure.com/)を開きます。 
1. `logic app` を検索して選択します。

    :::image type="content" source="./images/logicapps/logicapp-search.png" alt-text="Azure portal、Azure データエクスプローラーで ' logic app ' を検索しています" lightbox="./images/logicapps/logicapp-search.png#lightbox":::

1. **[+追加]** を選択します。

    ![ロジックアプリの追加](./Images/logicapps/logicapp-add.png)

1. フォームの必要な詳細を入力します。
    * サブスクリプション
    * Resource group
    * ロジック アプリ名
    * 地域または統合サービス環境
    * 場所
    * ログ分析のオン/オフ
1. **[Review + create]\(レビュー + 作成\)** を選択します。

    ![ロジック アプリを作成する](./Images/logicapps/logicapp-create-new.png)

1. ロジックアプリが作成されたら、[ **編集**] を選択します。

    ![ロジックアプリデザイナーの編集](./Images/logicapps/logicapp-editdesigner.png "logicapp-editdesigner")

1. 空のロジックアプリを作成します。

    ![ロジックアプリの空のテンプレート](./Images/logicapps/logicapp-blanktemplate.png "logicapp-空白のテンプレート")

1. 繰り返しアクションを追加し、[ **Azure Kusto**] を選択します。

    ![ロジックアプリ Kusto Flow コネクタ](./Images/logicapps/logicapp-kustoconnector.png "logicapp-kustoconnector")

## <a name="next-steps"></a>次の手順

* 繰り返しアクションの構成の詳細については、「 [Power オートメーションのドキュメント」ページ](../../flow.md)を参照してください。
* ロジックアプリのアクションを構成する方法については、 [使用例](../../flow-usage.md) を参照してください。
