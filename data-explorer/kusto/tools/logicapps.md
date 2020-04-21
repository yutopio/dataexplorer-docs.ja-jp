---
title: マイクロソフト ロジック アプリと Kusto - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのマイクロソフト ロジック アプリと Kusto について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f7d719ece5df6eb3f6d4060a2fb07e7092902601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523819"
---
# <a name="microsoft-logic-app-and-kusto"></a>マイクロソフト ロジック アプリとクストー

Azure Kusto ロジック アプリ コネクタを使用すると、スケジュールされたタスクまたはトリガーされたタスクの一部として、自動的に[Kusto](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)クエリとコマンドを実行できます。

Logic App コネクタと Flow コネクタは同じコネクタの上に構築されているため、フロー[actions](flow.md#azure-kusto-flow-actions)ドキュメント[ページ](flow.md)で説明[したように、アクション](flow.md#limitations)、[認証](flow.md#authentication)、[および使用法の例](flow.md#usage-examples)は両方に適用されます。


## <a name="how-to-create-a-logic-app-with-azure-kusto"></a>Azure Kusto でロジック アプリを作成する方法

Azure ポータルを開き、[新しいロジック アプリ リソースの作成] をクリックします。
希望の名前、サブスクリプション、resoure グループと場所を追加し、[作成] をクリックします。

![ロジック アプリを作成する](./Images/KustoTools-LogicApp/logicapp-createlogicapp.png "ロジックアプリ作成ロジックアプリ")

ロジック アプリが作成されたら、編集ボタン

![ロジック アプリ デザイナーの編集](./Images/KustoTools-LogicApp/logicapp-editdesigner.png "ロジックアプリ編集デザイナー")

空のロジック アプリを作成する

![ロジック アプリの空白テンプレート](./Images/KustoTools-LogicApp/logicapp-blanktemplate.png "ロジックアプリ空白テンプレート")

定期的なアイテムのアクションを追加し、[Azure Kusto] を選択します。

![ロジック アプリ クストー フロー コネクタ](./Images/KustoTools-LogicApp/logicapp-kustoconnector.png "ロジックアプリ-クストーコネクタ")