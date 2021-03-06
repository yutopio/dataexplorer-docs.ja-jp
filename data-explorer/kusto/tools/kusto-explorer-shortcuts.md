---
title: Kusto. エクスプローラーのキーボードショートカット (ホットキー)-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの Kusto. エクスプローラーのキーボードショートカット (ホットキー) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/22/2020
ms.openlocfilehash: a5bda2079adc06ea1bca5af65d89418ef5c293f6
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264676"
---
# <a name="kustoexplorer-keyboard-shortcuts-hot-keys"></a>Kusto. エクスプローラーのキーボードショートカット (ホットキー)

## <a name="application-level"></a>アプリケーションレベル

次のキーボードショートカットは、どのようなコンテキストからでも使用できます。

|ホットキー|説明|
|-------|-----------|
|`F1`     | ヘルプを開く|
|`F11`    | 全ビューモードの切り替え|
|`Ctrl`+`F1`| リボンの外観の切り替え |
|`Ctrl`+`+`| クエリとデータの結果のフォントを増やす|
|`Ctrl`+`-`| クエリとデータの結果のフォントを減らす|
|`Ctrl`+`0`| クエリとデータの結果のフォントをリセットします|
|`Ctrl`+`1` .. `7`| それぞれの数値 (1.. 7) でドキュメントパネルにクエリを実行します|
|`Ctrl`+`F2`|クエリエディターパネルのヘッダーの名前を変更|
|`Ctrl`+`N` |新しいクエリエディターを開きます。|
|`Ctrl`+`O` |新しいクエリエディターでクエリエディタースクリプトを開く|
|`Ctrl`+`W` |アクティブなクエリエディターを閉じます。|
|`Ctrl`+`S` |クエリをファイルに保存します|
|`Shift`+`F3` | 分析レポートギャラリーを開きます|
|`Shift`+`F12`| アプリケーションの明るい/濃色テーマを切り替えます|
|`Ctrl`+`Shift`+`O`|Kusto を開きます。エクスプローラーの [オプションと設定] ダイアログ|
|`Esc`|実行中のクエリのキャンセル|
|`Shift`+`F5`|実行中のクエリのキャンセル|

## <a name="query-and-results-view"></a>クエリおよび結果ビュー

クエリエディターで次のキーボードショートカットを使用することも、結果ビューでコンテキストを使用することもできます。

|ホットキー|説明|
|-------|-----------|
|`Ctrl`+`Shift`+`C`|クエリ、ディープリンク、およびデータをクリップボードにコピーします。|
|`Alt`+`Shift`+`C` |クエリをコピーし、クリップボードを HTML 形式でディープリンクします。|
|`Alt`+`Shift`+`R` |クエリ、ディープリンクをコピーし、Markdown 形式でクリップボードにデータを格納します。|
|`Alt`+`Shift`+`M` |クエリをコピーし、クリップボードを Markdown 形式でディープリンクします。|
|`Ctrl`+`~` |クエリおよびデータを markdown 形式でクリップボードにコピーします。 |
|`Ctrl`+`Shift`+`D`|データビュー内の重複する行を非表示にするモードを切り替えます。|
|`Ctrl`+`Shift`+`H`|データビュー内の空の列を非表示にするモードを切り替えます。|
|`Ctrl`+`Shift`+`J`|データビューで1つの値を使用して列を折りたたむモードを切り替えます。|
|`Ctrl`+`Shift`+`A`|新しい [クエリ] パネルでクエリアナライザーツールを開きます。|
|`Alt`+`C`  |既存のデータに対して縦棒グラフを表示します|
|`Alt`+`T`  |既存のデータに対してタイムライングラフをレンダリングします|
|`Alt`+`A`  |既存のデータに対する異常タイムライングラフをレンダリングします|
|`Alt`+`P`  |既存のデータに円グラフを表示します|
|`Alt`+`L`  |既存のデータに対してラダータイムライングラフをレンダリングします|
|`Alt`+`V`  |既存のデータにピボットグラフを表示します|
|`Ctrl`+`Shift`+`V`|既存のデータに対するタイムラインピボットを表示します|
|`Ctrl`+`F9`  | `show only matching rows` / `highlight matching rows` データグリッドのクライアントテキスト検索 () 動作のモードを切り替え `Ctrl` + `F` ます。 |
|`Ctrl`+`F10` |詳細パネルを表示します。選択した行がプロパティグリッドとして表示されます。|
|`Ctrl`+`F`  | 現在フォーカスがあるパネルの検索ボックスを表示します。 、、およびの各パネルでサポートされ `Connetions` ます。 `Data Results` `Query Editor`|
|`Ctrl`+`Tab`| クエリエディターのドキュメントセレクターダイアログを表示します。 `Ctrl`ドキュメントの保存と切り替えは、`Tab` |
|`Ctrl`+`J`|結果パネルの外観を切り替えます|
|`Ctrl`+`E`|クエリエディターと結果パネルの表示を次のサイクルで切り替えます。`Query Editor and Results` -> `Query Editor` -> `Query Editor and Results` -> `Results` |
|`Ctrl`+`Shift`+`E`|クエリエディターと結果パネルの表示を次のサイクルで切り替えます。`Query Editor and Results` -> `Results` -> `Query Editor and Results` -> `Query Editor` |
|`Ctrl`+`Shift`+`R` | 結果パネルにフォーカス |
|`Ctrl`+`Shift`+`T` | [接続] パネルにフォーカス |
|`Ctrl`+`Shift`+`Y` | クエリエディターに焦点を当てます |
|`Ctrl`+`Shift`+`P` | グラフパネルに焦点を当てます |
|`Ctrl`+`Shift`+`I` | クエリ情報パネルにフォーカスする |
|`Ctrl`+`Shift`+`S` | [クエリ統計] パネルにフォーカスする |
|`Ctrl`+`Shift`+`K` | エラーパネルにフォーカスする |
|`Alt`+`Ctrl`+`L`|現在の接続コンテキストをクエリエディターにロックします。そのため、接続パネルで選択した行を変更しても、クエリエディターのコンテキストには影響しません。 |

## <a name="results-table-viewer"></a>結果テーブルビューアー

次のキーボードショートカットは、結果ビュー (テーブル) がアクティブなキーボードフォーカスにある場合に使用できます。

|ホットキー|説明|
|-----------|-----------|
|`Ctrl`+`Q` |現在の列のコンテキストメニューを表示する|
|`Ctrl`+`S` |現在の列の並べ替えの切り替え|
|`Ctrl`+`U` |クライアント側のフィルター処理で現在の列の値を示すパネルを開きます|
|`Ctrl`+`F` | 結果の検索ボックスを表示します|
|`Ctrl`+`F3`| `show only matching rows` / `highlight matching rows` データグリッドのクライアントテキスト検索 () 動作のモードを切り替え `Ctrl` + `F` ます。 |

## <a name="query-editor"></a>クエリ エディター

クエリエディターでクエリを編集するときに、次のキーボードショートカットを使用できます。

|ホットキー|説明|
|-------|-----------|
|`F1`|カーソルが演算子または関数をポイントすると、オペレーターまたは関数に関する情報を含むヘルプウィンドウが開きます。 ヘルプトピックが存在しない場合-ヘルプ URL を開きます。|
|`F5`|現在選択されているクエリの実行|
|`Shift`+`Enter`|現在選択されているクエリの実行|
|`F8`|ローカルキャッシュからクエリの結果を取得します。 結果が存在しない場合-現在選択されているクエリを実行します|
|`F6`|現在選択されているクエリをモードで実行します `Progressive Results`|
|`Ctrl`+`F5` | 選択したクエリの結果をプレビューします (いくつかの結果と合計数を表示します)|
|`Ctrl`+`Shift`+`Space`| クエリにフィルターとしてデータセル選択項目を挿入する|
|`Ctrl`+`Space`| IntelliSense 規則の確認を強制します。 一致するルールでは、使用可能なオプションが表示されます。 |
|`Ctrl`+`Enter`| `pipe`記号を追加し、新しい行に移動します。|
|`Ctrl`+`Z`| 元に戻す |
|`Ctrl`+`Y`| やり直し |
|`Ctrl`+`L`| 現在の行を削除します|
|`Ctrl`+`D`| 現在の行を削除します| 
|`Ctrl`+`F`| ダイアログを開く `Find and Replace` |
|`Ctrl`+`G`| ダイアログを開く `Go-to line` |
|`Ctrl`+`F8` | 過去3日間のクエリを表示 |
|`Ctrl`+ かっこ | カーソルが角かっこで囲む場合: `(` 、、 `)` 、、 `[` `]` `{` 、 `}` -カーソルを対応する左または右の角かっこに移動します。 |
|`Ctrl`+`Shift`+`Q` | Debug.log の現在のクエリ |
|`Ctrl`+`Shift`+`L` | 現在のクエリまたは選択範囲を小文字にする |
|`Ctrl`+`Shift`+`U` |  現在のクエリまたは選択範囲を大文字にする |
|`Ctrl`+`Mouse wheel up`| クエリエディターのフォントを拡大します|
|`Ctrl`+`Mouse wheel down`| クエリエディターのフォントを縮小する|
|`Alt`+`P` | クエリパラメーターダイアログを開く |
|`F2`| エディターダイアログで現在の行または選択したテキストを開く |
|`Ctrl`+`F6`| KQL の静的クエリ分析を実行して、一般的な問題を検出します |
|`F12`| シンボルの定義に移動します。 |
|`Ctrl`+`F12`| 現在のシンボルのすべての参照を検索します |
|`Alt`+`Home`| シンボルの定義に移動します。 |
|`Alt`+`Ctrl`+`M`| 現在選択されているリテラルまたはテーブル式を let ステートメントとして抽出する |
|`Ctrl`+`.`| 現在選択されているリテラルまたはテーブル式を let ステートメントとして抽出する |
|`Ctrl`+`R`, `Ctrl`+`R` | 現在のシンボルの名前を変更 |
|`Ctrl`+`K`, `Ctrl`+`D` | 現在のタイムスタンプを datetime リテラルとして挿入します |
|`Ctrl`+`K`, `Ctrl`+`R` | スニペットの挿入 `range x from 1 to 1 step 1` |
|`Ctrl`+`K`, `Ctrl`+`C` | 現在の行または選択された行にコメントを入れる |
|`Ctrl`+`K`, `Ctrl`+`F` | Debug.log の現在のクエリ |
|`Ctrl`+`K`, `Ctrl`+`V` | 現在のクエリの複製 (現在のクエリドキュメントの末尾に追加) |
|`Ctrl`+`K`, `Ctrl`+`U` | 現在の行または選択した行のコメントを解除 |
|`Ctrl`+`K`, `Ctrl`+`S` | 現在の行または選択した行を複数行の文字列リテラルに変換します |
|`Ctrl`+`K`, `Ctrl`+`M` | 複数行の文字列リテラル記号を削除します (の逆順 `Ctrl` + `K` 、 `Ctrl` + `S` ) |
|`Ctrl`+`M`, `Ctrl`+`M` | 現在のクエリのアウトラインの展開を切り替えます |
|`Ctrl`+`M`, `Ctrl`+`L` | ドキュメント内のすべてのクエリのアウトラインの展開を切り替えます |

## <a name="json-viewer"></a>JSON ビューアー

次のキーボードショートカットは、結果の JSON ビューアー内から使用できます。
結果ビューのセルで、JSON に似た値をダブルクリックすると、次のように表示されます。

|ホットキー|説明|
|-------|-----------|
|`Ctrl`+`Up Arrow`|親に移動|
|`Ctrl`+`Right Arrow`|現在のノードを展開する (1 レベル)|
|`Ctrl`+`Left Arrow`|現在のノードを折りたたむ (1 レベル)|
|`Ctrl`+`.`|現在のノードの展開を切り替えます (すべての子レベルが展開または折りたたまれています)
|`Ctrl`+`Shift`+`.`|現在のノードの親の展開を切り替えます (すべての子レベルが展開または折りたたまれています)|

## <a name="connection-panel"></a>接続パネル

次のキーボードショートカットは、結果の JSON ビューアー内から使用できます。
結果ビューのセルで、JSON に似た値をダブルクリックすると、次のように表示されます。

|ホットキー|説明|
|-------|-----------|
|`Ctrl`+`Up`ボタン|親に移動|
|`Ctrl`+`Right`ボタン|現在のノードを展開する (1 レベル)|
|`Ctrl`+`Left`ボタン|現在のノードを折りたたむ (1 レベル)|
|`Ctrl`+`Shift`+`L`|すべてのレベルを折りたたむ|
|`Ctrl`+`R`|現在選択されている接続の更新|
|`Insert`|新しい接続の追加|
|`Del`|現在の接続の削除|
|`Ctrl`+`E`|現在選択されている接続の編集|
|`Ctrl`+`T`|現在選択されている接続を使用して新しいクエリエディターを開く|

## <a name="diagnostics-and-monitoring"></a>診断と監視

リボンからは、次のキーボードショートカットを使用でき `Monitoring` ます。

|ホットキー|説明|
|-----------|-----------|
|`Ctrl`+`Shift`+`F1`|クラスター診断フローの実行|
