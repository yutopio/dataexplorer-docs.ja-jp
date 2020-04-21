---
title: Kusto エクスプローラー コード リファクタリング - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの Kusto エクスプローラー コード リファクタリングについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 0a89cf9c648fc4811d56c22012cdb25d5505eb4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523989"
---
# <a name="kusto-explorer-code-refactoring"></a>Kusto エクスプローラ のコード リファクタリング

他の IDE と同様に、Kusto.Explorer では KQL クエリの編集とリファクタリングにいくつかの機能を提供しています。

## <a name="rename-variable-or-column-name"></a>変数名または列名の名前変更

クエリ`Ctrl`+`R``Ctrl`+`R`エディター ウィンドウで をクリックすると、現在選択されているシンボルの名前を変更できます。

以下のスナップショットを参照して、エクスペリエンスを示します。

![alt text](./Images/KustoTools-KustoExplorer/ke-refactor-rename.gif "リファクタリング名の変更")

## <a name="extract-scalars-as-let-expressions"></a>スカラーを式として`let`抽出する

を`Alt`+クリック`Ctrl``let`+すると、現在選択されているリテラルを式として昇格`M`できます。 

![alt text](./Images/KustoTools-KustoExplorer/ke-extract-as-let-literal.gif "抽出 -let-literal として")

## <a name="extract-tabular-statements-as-let-expressions"></a>表形式のステートメント`let`を式として抽出する

表形式の式をステートメントとして`let`昇格するには、そのテキストを選択して`Alt`+`Ctrl`+`M`をクリックします。 

![alt text](./Images/KustoTools-KustoExplorer/ke-extract-as-let-tabular.gif "表形式として抽出")
