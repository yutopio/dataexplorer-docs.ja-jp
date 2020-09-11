---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 3e916c26b95a77be5c35748c6a50e05fe0ef916d
ms.sourcegitcommit: 811cf98edefd919b412d80201400919eedcab5cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89274682"
---
## <a name="explore-quick-queries-and-tools"></a>クイック クエリとツールを探索する

インジェストの進行状況の下にあるタイルで、 **[クイック クエリ]** または **[ツール]** を探索します。 
 * **[Quick queries]\(クイック クエリ\)** には、クエリの例がある Web UI へのリンクが含まれています。
 * **[ツール]** には、Web UI の **[元に戻す]** または **[Delete new data]\(新しいデータの削除\)** へのリンクが含まれています。これを使用すると、関連する `.drop` コマンドを実行して問題をトラブルシューティングできます。

     > [!NOTE]
     > `.drop` コマンドを使用すると、データが失われる可能性があります。 慎重に使用してください。
     > Drop コマンドは、インジェスト フローによって行われた変更 (新しいエクステントと列) を元に戻すだけです。 それ以外はドロップされません。
