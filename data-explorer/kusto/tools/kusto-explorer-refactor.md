---
title: Kusto Explorer コードのリファクタリング-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの Kusto Explorer コードリファクタリングについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 2e73e10bd62f9767bfe578eadc747372cd604362
ms.sourcegitcommit: 88923cfb2495dbf10b62774ab2370b59681578b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2020
ms.locfileid: "92175676"
---
# <a name="kusto-explorer-code-refactoring"></a>Kusto Explorer コードのリファクタリング

他の Ide と同様に、Kusto. エクスプローラーには、KQL クエリの編集とリファクタリングの機能がいくつか用意されています。

## <a name="rename-variable-or-column-name"></a>変数名または列名の変更

をクリックすると `Ctrl` + `R` 、 `Ctrl` + `R` クエリエディターウィンドウで現在選択されているシンボルの名前を変更できます。

エクスペリエンスを示す次のスナップショットを参照してください。

![クエリエディターウィンドウで名前が変更されている変数を表示するアニメーション GIF。3回の出現は、同時に新しい名前に置き換えられます。](./Images/kusto-explorer-refactor/ke-refactor-rename.gif "リファクター-名前の変更")

## <a name="extract-scalars-as-let-expressions"></a>スカラーを式として抽出する `let`

をクリックすると、現在選択されているリテラルを式として昇格させることができ `let` `Alt` + `Ctrl` + `M` ます。 

![アニメーション GIF。クエリエディターのポインターは、リテラル式で開始されます。次に、そのリテラル値を新しい変数に設定する let ステートメントが表示されます。](./Images/kusto-explorer-refactor/ke-extract-as-let-literal.gif "let としての文字列の抽出")

## <a name="extract-tabular-statements-as-let-expressions"></a>表形式のステートメントを式として抽出する `let`

また、表形式の式をステートメントとして昇格させる `let` には、そのテキストを選択し、をクリックし `Alt` + `Ctrl` + `M` ます。 

![アニメーション GIF。クエリエディターで表形式の式が選択されます。その後、テーブル式を新しい変数に設定する let ステートメントが表示されます。](./Images/kusto-explorer-refactor/ke-extract-as-let-tabular.gif "let による抽出-表形式")
