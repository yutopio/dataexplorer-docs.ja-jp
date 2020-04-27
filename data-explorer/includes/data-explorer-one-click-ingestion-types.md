---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 56095df921864896dacdb100302d686d66f40572
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2020
ms.locfileid: "82109634"
---
## <a name="select-an-ingestion-type"></a>インジェストの種類を選択する

**[Ingestion type]\(インジェストの種類\)** には、次のいずれかのオプションを選択します。
   * **ストレージから** - **[Link to storage]\(ストレージにリンク\)** フィールドに、ストレージ アカウントの URL を追加します。 プライベート ストレージ アカウントには [BLOB SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) を使用します。
   
      ![ストレージからのワンクリックでのインジェスト](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **ファイルから** - **[Browse]\(参照\)** を選択してファイルを見つけるか、ファイルをフィールドにドラッグします。
  
      ![ファイルからのワンクリックでのインジェスト](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **コンテナーから** - **[Link to storage]\(ストレージにリンク\)** フィールドにコンテナーの [SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) を追加し、必要に応じてサンプル サイズを入力します。

      ![コンテナーからのワンクリックでのインジェスト](media/data-explorer-one-click-ingestion-types/from-container.png)

  データのサンプルが表示されます。 必要に応じて、特定の文字で始まる、または終わるファイルだけを表示するようにフィルター処理することができます。 フィルターを調整すると、プレビューが自動的に更新されます。
  
  たとえば、*data* という単語で始まり、 *.csv.gz* 拡張子で終わるすべてのファイルを表示するようにフィルター処理できます。

  ![ワンクリックでのインジェスト フィルター](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
