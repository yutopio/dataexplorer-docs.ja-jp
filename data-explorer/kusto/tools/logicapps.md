---
title: Logic Apps を使用して Kusto クエリを自動的に実行する
description: Logic Apps を使用して Kusto クエリとコマンドを自動的に実行し、スケジュールを設定する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: docohe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 8765635e0eea8c1d41640bc0393d39a0afa5f971
ms.sourcegitcommit: e66c5f4b833b4f6269bb7bfa5695519fcb11d9fa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83630198"
---
# <a name="microsoft-logic-app-and-azure-data-explorer"></a>Microsoft Logic App と Azure データエクスプローラー

Azure Kusto Logic App コネクタを使用すると、 [Microsoft Logic app](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)コネクタを使用して、スケジュールされたタスクまたはトリガーされたタスクの一部として kusto クエリとコマンドを自動的に実行することができます。

ロジックアプリとフローは、同じコネクタ上に構築されています。 そのため、フローに適用される[制限事項](flow.md#limitations)、[アクション](flow.md#azure-kusto-flow-actions)、[認証](flow.md#authentication)、[使用例](flow.md#azure-kusto-flow-actions)については、 [flow のドキュメントページ](flow.md)で説明されているように、Logic Apps にも適用されます。

## <a name="how-to-create-a-logic-app-with-azure-data-explorer"></a>Azure データエクスプローラーでロジックアプリを作成する方法

1. [Microsoft Azure portal](https://ms.portal.azure.com/)を開きます。 
1. を検索して `logic app` 選択します。

    [![](./Images/logicapps/logicapp-search.png "Search for logic app")](./Images/logicapps/logicapp-search.png#lightbox)

1. **[+追加]** を選択します。

    ![ロジックアプリの追加](./Images/logicapps/logicapp-add.png)

1. フォームの必要な詳細を入力します。
    * サブスクリプション
    * Resource group
    * ロジックアプリ名
    * 地域または統合サービス環境
    * インストール先
    * ログ分析のオン/オフ
1. **[Review + create]\(レビュー + 作成\)** を選択します。

    ![ロジック アプリを作成する](./Images/logicapps/logicapp-create-new.png)

1. ロジックアプリが作成されたら、[**編集**] を選択します。

    ![ロジックアプリデザイナーの編集](./Images/logicapps/logicapp-editdesigner.png "logicapp-editdesigner")

1. 空のロジックアプリを作成します。

    ![ロジックアプリの空のテンプレート](./Images/logicapps/logicapp-blanktemplate.png "logicapp-空白のテンプレート")

1. 繰り返しアクションを追加し、[ **Azure Kusto**] を選択します。

    ![ロジックアプリ Kusto Flow コネクタ](./Images/logicapps/logicapp-kustoconnector.png "logicapp-kustoconnector")

## <a name="next-steps"></a>次のステップ

* 繰り返しアクションの構成の詳細については、 [Flow のドキュメントページ](flow.md)を参照してください。
* ロジックアプリのアクションを構成する方法については、[使用例](flow.md#azure-kusto-flow-actions)を参照してください。
