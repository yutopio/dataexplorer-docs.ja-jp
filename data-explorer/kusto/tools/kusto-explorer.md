---
title: Kusto エクスプローラーの概要
description: Kusto の機能について説明し、データを探索するために役立つ情報を紹介します。
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: overview
ms.date: 05/19/2020
ms.openlocfilehash: 1f3b273260451dc0ce36730d20f1bc357a453397
ms.sourcegitcommit: 2126c5176df272d149896ac5ef7a7136f12dc3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2020
ms.locfileid: "86280543"
---
# <a name="getting-started-with-kustoexplorer"></a>Kusto エクスプローラーの概要

Kusto. エクスプローラーは、使いやすいユーザーインターフェイスで Kusto クエリ言語を使用してデータを探索できる、豊富なデスクトップアプリケーションです。 この概要では、Kusto のセットアップを開始する方法と、使用するユーザーインターフェイスについて説明します。

Kusto. エクスプローラーを使用すると、次のことができます。
* [データのクエリ](kusto-explorer-using.md#query-mode)を実行します。
* テーブル間で[データを検索](kusto-explorer-using.md#search-mode)します。
* さまざまなグラフで[データを視覚](#visualizations-section)化します。
* [クエリと結果](kusto-explorer-using.md#share-queries-and-results)を電子メールで共有するか、ディープリンクを使用します。

## <a name="installing-kustoexplorer"></a>Kusto のインストール

* [Kusto エクスプローラーツール](https://aka.ms/ke)をインストールします。

* 代わりに、ブラウザーで Kusto クラスターにアクセスします。`https://<your_cluster>.kusto.windows.net.`
   &lt;Your_cluster &gt; を Azure データエクスプローラークラスター名に置き換えます。

### <a name="using-chrome-and-kustoexplorer"></a>Chrome と Kusto. エクスプローラーを使用する

Chrome を既定のブラウザーとして使用する場合は、Chrome 用の ClickOnce 拡張機能をインストールしてください。

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)

## <a name="overview-of-the-user-interface"></a>ユーザーインターフェイスの概要

Kusto. エクスプローラーユーザーインターフェイスは、他の Microsoft 製品と同じように、タブとパネルに基づくレイアウトで設計されています。 

1. [メニューパネル](#menu-panel)のタブ間を移動して、さまざまな操作を実行します。
2. [[接続] パネル](#connections-panel)で接続を管理する
3. スクリプトパネルで実行するスクリプトを作成する
4. 結果パネルでのスクリプトの結果の表示

:::image type="content" source="images/kusto-explorer/ke-start.png" alt-text="Kusto Explorer の開始":::

## <a name="menu-panel"></a>メニューパネル

Kusto. エクスプローラーのメニューパネルには、次のタブがあります。

* [Home](#home-tab)
* [File](#file-tab)
* [接続](#connections-tab)
* [表示](#view-tab)
* [ツール](#tools-tab)
* [Monitoring](#monitoring-tab)
* [管理](#management-tab)
* [ヘルプ](#help-tab)

### <a name="home-tab"></a>[ホーム] タブ

:::image type="content" source="images/kusto-explorer/home-tab.png" alt-text="Kusto Explorer の [ホーム] タブ":::

[ホーム] タブには、最近使用した関数がセクションに分けて表示されます。

* [Query](#query-section)
* [共有](#share-section)
* [視覚化](#visualizations-section)
* [表示](#view-section)
* [ヘルプ](#help-tab) 

### <a name="query-section"></a>クエリセクション

:::image type="content" source="images/kusto-explorer/home-query-menu.png" alt-text="クエリメニュー Kusto Explorer":::

|メニュー|    動作|
|----|----------|
|モードのドロップダウン | <ul><li>クエリモード: クエリウィンドウを[スクリプトモード](kusto-explorer-using.md#query-mode)に切り替えます。 コマンドはスクリプトとして読み込んで保存できます (既定)。</li> <li> 検索モード: 入力した各コマンドが直ちに処理され、結果ウィンドウに結果が表示される単一のクエリモード</li> <li>Search + + モード: 1 つ以上のテーブルで検索構文を使用して用語を検索できます。 [検索 + + モード](kusto-explorer-using.md#search-mode)の使用に関する詳細情報</li></ul> |
|新しいタブ| Kusto に対するクエリを実行するための新しいタブを開きます。 |

### <a name="share-section"></a>セクションを共有する

:::image type="content" source="images/kusto-explorer/home-share-menu.png" alt-text="Kusto Explorer 共有メニュー":::

|メニュー|    動作|
|----|----------|
|クリップボードへのデータ|    クエリとデータセットをクリップボードにエクスポートします。 グラフが表示される場合は、グラフをビットマップとしてエクスポートします。| 
|結果をクリップボードに出力| データセットをクリップボードにエクスポートします。 グラフが表示される場合は、グラフをビットマップとしてエクスポートします。| 
|クリップボードへのクエリ| クエリをクリップボードにエクスポートします。|

### <a name="visualizations-section"></a>視覚エフェクトセクション

:::image type="content" source="images/kusto-explorer/home-visualizations-menu.png" alt-text="Kusto Explorer メニューの視覚エフェクト":::

|メニュー         | 動作|
|-------------|---------|
|面グラフ      | X 軸が最初の列である面グラフを表示します (数値である必要があります)。 すべての数値列が異なる系列 (Y 軸) にマップされます |
|Column Chart | すべての数値列が異なる系列 (Y 軸) にマップされる縦棒グラフを表示します。 数値の前のテキスト列は X 軸 (UI で制御可能) です。|
|横棒グラフ    | すべての数値列が異なる系列 (X 軸) にマップされている棒グラフを表示します。 数値の前のテキスト列は、Y 軸 (UI で制御可能) です。|
|積み上げ面グラフ      | X 軸が1番目の列である積み上げ面グラフを表示します (数値である必要があります)。 すべての数値列が異なる系列 (Y 軸) にマップされます |
|タイムライン グラフ   | X 軸が最初の列である時間グラフを表示します (datetime である必要があります)。 すべての数値列が異なる系列 (Y 軸) にマップされます。|
|折れ線グラフ   | X 軸が最初の列である折れ線グラフを表示します (数値である必要があります)。 すべての数値列が異なる系列 (Y 軸) にマップされます。|
|[異常グラフ](#anomaly-chart)|    線上に似ていますが、機械学習の異常アルゴリズムを使用して、時系列データの異常を検出します。 異常検出の場合、Kusto は[series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md)関数を使用します。
|円グラフ    |    カラー軸が最初の列である円グラフを表示します。 シータ軸 (パーセントに変換されたメジャーである必要があります) は2番目の列です。|
|時間ラダー |    X 軸が最後の2つの列 (datetime である必要があります) であるラダーグラフを表示します。 Y 軸は、他の列の複合型です。|
|散布図| X 軸が最初の列であるポイントグラフを表示します (数値である必要があります)。 すべての数値列が異なる系列 (Y 軸) にマップされます。|
|[ピボット グラフ]  | ピボットテーブルとピボットグラフを表示します。このグラフでは、データ、列、行、およびさまざまな種類のグラフを柔軟に選択できます。| 
|時間ピボット   | イベントのタイムラインでの対話型ナビゲーション (タイム軸でのピボット)|

> [!NOTE]
> <a id="anomaly-chart">異常チャート</a>: アルゴリズムでは、次の2つの列で構成される timeseries データが必要です。
>* 固定間隔バケットの時間
>* Kusto エクスプローラーで timeseries データを生成するための異常検出の数値。時間フィールドで集計し、時間バケットビンを指定します。

### <a name="view-section"></a>セクションの表示

:::image type="content" source="images/kusto-explorer/home-view-menu.png" alt-text="Kusto Explorer ビューメニュー":::

|メニュー           | 動作|
|---------------|---------|
|全表示モード | リボンメニューと接続パネルを非表示にして、ワークスペースを最大化します。 完全表示モードを終了するには、[**ホーム**  >  **完全表示モード**] を選択するか、 **F11**キーを押します。|
|空の列を非表示にする| データグリッドから空の列を削除します。|
|単数形の列を折りたたむ| 単数形の値で列を折りたたむ|
|列の値を調べる| 列の値の分布を表示します|
|フォントの拡大  | [クエリ] タブおよび結果データグリッドのフォントサイズを大きくします。|  
|フォントの縮小  | [クエリ] タブおよび結果データグリッドのフォントサイズを小さくします。|

>[!NOTE]
> データビューの設定:
>
> Kusto. エクスプローラーは、一意の列セットごとにどの設定が使用されているかを追跡します。 列が並べ替えられるか削除されると、データビューが保存され、同じ列を持つデータが取得されるたびに再利用されます。 ビューを既定値にリセットするには、[**表示**] タブで [**ビューのリセット**] を選択します。 

## <a name="file-tab"></a>[ファイル] タブ

:::image type="content" source="images/kusto-explorer/file-tab.png" alt-text="Kusto Explorer の [ファイル] タブ":::

|メニュー| 動作|
|---------------|---------|
||---------*クエリスクリプト*---------|
|新しいタブ | Kusto に対するクエリを実行するための新しいタブウィンドウを開きます |
|[ファイルを開く]| * Kql ファイルからアクティブスクリプトパネルにデータを読み込みます。|
|ファイルに保存| アクティブスクリプトパネルの内容を * kql ファイルに保存します。|
|タブを閉じる| 現在のタブウィンドウを閉じます。|
||---------*データの保存*---------|
|CSV へのデータ       | CSV (コンマ区切り値) ファイルにデータをエクスポートします| 
|データを JSON に      | JSON 形式のファイルにデータをエクスポートします|
|Excel へのデータ     | .XLSX (Excel) ファイルにデータをエクスポートします|
|テキストへのデータ      | TXT (テキスト) ファイルにデータをエクスポートします。| 
|KQL スクリプトへのデータ| クエリをスクリプトファイルにエクスポートします| 
|結果へのデータ   | クエリとデータを結果 (QRES) ファイルにエクスポートします|
|CSV へのクエリの実行 |クエリを実行し、結果をローカルの CSV ファイルに保存します。|
||---------*データの読み込み*---------|
|結果から|    クエリとデータを結果 (QRES) ファイルから読み込みます。| 
||---------*Clipboard*---------|
|クエリと結果をクリップボードに出力|    クエリとデータセットをクリップボードにエクスポートします。 グラフが表示される場合は、グラフをビットマップとしてエクスポートします。| 
|結果をクリップボードに出力| データセットをクリップボードにエクスポートします。 グラフが表示される場合は、グラフをビットマップとしてエクスポートします。| 
|クリップボードへのクエリ| クエリをクリップボードにエクスポートします。|
||---------*生じ*---------|
|結果キャッシュのクリア| 以前に実行されたクエリのキャッシュされた結果をクリアします| 

## <a name="connections-tab"></a>[接続] タブ

:::image type="content" source="images/kusto-explorer/connections-tab.png" alt-text="Kusto Explorer の [接続] タブ":::

|メニュー|動作|
|----|----------|
||---------*グループ*---------|
|グループの追加| 新しい Kusto サーバーグループを追加します。|
|グループ名の変更| 既存の Kusto サーバーグループの名前を変更します。|
|グループの削除| 既存の Kusto サーバーグループを削除します。|
||---------*クラスター*---------|
|接続のインポート| 接続を指定するファイルから接続をインポートします|
|接続のエクスポート| ファイルへの接続をエクスポートします。|
|接続の追加| 新しい Kusto サーバー接続を追加します。| 
|接続の編集| Kusto サーバーの接続プロパティを編集するためのダイアログを開きます。|
|接続の削除| Kusto サーバーへの既存の接続を削除します。|
|更新| Kusto サーバー接続のプロパティを更新します。|
||---------*保護*---------|
|プリンシパルの追加を検査する| 流れ着くアクティブユーザーの詳細を表示します|
|AAD からサインアウトする| AAD への接続から現在のユーザーをサインアウトします|
||---------*データスコープ*---------|
|キャッシュスコープ|<ul><li>ホット[データキャッシュ](../management/cachepolicy.md)に対してのみホット dataexecute クエリを実行する</li><li>すべてのデータ: 使用可能なすべてのデータに対してクエリを実行します (既定)。</li></ul> |
|DateTime 列| 時間前フィルターに使用できる列の名前|
|時間フィルター| 時間前フィルターの値|

## <a name="view-tab"></a>[表示] タブ

:::image type="content" source="images/kusto-explorer/view-tab.png" alt-text="Kusto Explorer の [表示] タブ":::

|メニュー|動作|
|----|----------|
||---------*内容*---------|
|全表示モード | リボンメニューと接続パネルを非表示にしてワークスペースを最大化します|
|フォントの拡大  | [クエリ] タブおよび結果データグリッドのフォントサイズを大きくします。|  
|フォントの縮小  | [クエリ] タブおよび結果データグリッドのフォントサイズを小さくします。|
|レイアウトのリセット|ツールのドッキングコントロールとウィンドウのレイアウトをリセットします。|
|ドキュメントタブの名前変更 |選択したタブの名前を変更する |
||---------*データビュー*---------|
|ビューのリセット| [データビューの設定](#dvs)を既定値にリセットします |
|列の値を調べる|列の値の分布を表示します|
|クエリ統計にフォーカスする|クエリの完了時にクエリの結果ではなくクエリの統計にフォーカスを変更します|
|重複を非表示|クエリ結果からの重複行の削除を切り替えます。|
|空の列を非表示にする|クエリ結果からの空の列の削除を切り替えます|
|単数形の列を折りたたむ|単数形の値による列の折りたたみを切り替えます|
||---------*データのフィルター処理*---------|
|検索で行をフィルター処理する|クエリ結果の検索で一致する行のみを表示するオプションを切り替えます (**Ctrl + F**)|
||---------*効果*---------|
|視覚化|上の「[視覚化](#visualizations-section)」を参照してください。 |

> [!NOTE]
> <a id="dvs">データビューの設定:</a> 
>
> Kusto. エクスプローラーは、一意の列のセットごとに使用される設定を追跡します。 列が並べ替えられるか削除されると、データビューが保存され、同じ列を持つデータが取得されるたびに再利用されます。 ビューを既定値にリセットするには、[**表示**] タブで [**ビューのリセット**] を選択します。 

## <a name="tools-tab"></a>[ツール] タブ

:::image type="content" source="images/kusto-explorer/tools-tab.png" alt-text="Kusto Explorer の [ツール] タブ":::

|メニュー|動作|
|----|----------|
||---------*Tab*---------|
|[IntelliSense を有効にする]| スクリプトパネルで IntelliSense を有効または無効にします。|
||---------*検討*---------|
|クエリ アナライザー| クエリアナライザーツールを起動します。|
|クエリチェッカー | 現在のクエリを分析し、適用可能な改善に関する推奨事項のセットを出力します。|
|電卓| 電卓を起動します|
||---------*解析*---------|
|分析レポート| データ分析用にビルド済みの複数のレポートを含むダッシュボードを開きます|
||---------*翻訳*---------|
|Power BI へのクエリ| クエリをのの使用に適した形式に変換し Power BI|
||---------*オプション*---------|
|リセットオプション| アプリケーション設定を既定値に設定します|
|オプション| アプリケーション設定を構成するためのツールを開きます。 詳細について[は、Kusto. エクスプローラオプション](kusto-explorer-options.md)に関するページを参照してください。|

## <a name="monitoring-tab"></a>[監視] タブ

:::image type="content" source="images/kusto-explorer/monitoring-tab.png" alt-text="Kusto Explorer の [監視] タブ":::

|メニュー             | 動作|
|-----------------|---------| 
||---------*Monitor*---------|
|クラスター診断 | [接続] パネルで現在選択されているサーバーグループの正常性の概要を表示します。 | 
|最新データ: すべてのテーブル| 現在選択されているデータベースのすべてのテーブルの最新データの概要を表示します|
|最新データ: 選択されたテーブル|選択したテーブルの最新データをステータスバーに表示します| 

## <a name="management-tab"></a>[管理] タブ

:::image type="content" source="images/kusto-explorer/management-tab.png" alt-text="Kusto Explorer の [管理] タブ":::

|メニュー             | 動作|
|-----------------|---------|
||---------*承認されたプリンシパル*---------|
|クラスターの承認されたプリンシパルを管理する |承認されたユーザーに対するクラスターのプリンシパルの管理を有効にします。| 
|データベース承認済みプリンシパルの管理 | 承認されたユーザーのデータベースプリンシパルの管理を有効にします。| 
|テーブルの承認されたプリンシパルの管理 | 承認されたユーザーのテーブルプリンシパルの管理を有効にします。| 
|関数の承認されたプリンシパルの管理 | 権限のあるユーザーのための関数のプリンシパルの管理を有効にします。| 

## <a name="help-tab"></a>[ヘルプ] タブ

:::image type="content" source="images/kusto-explorer/help-tab.png" alt-text="Kusto Explorer の [ヘルプ] タブ":::

|メニュー             | 動作|
|-----------------|---------|
||---------*書*---------|
|ヘルプ             | Kusto オンラインドキュメントへのリンクを開きます。  | 
|新着情報       | すべての Kusto エクスプローラーの変更を一覧表示するドキュメントを開きます。|
|Report Issue (問題の報告)      |次の2つのオプションでダイアログを開きます。 <ul><li>サービスに関連する問題を報告する</li><li>クライアントアプリケーションの問題を報告する</li></ul> | 
|機能の提案  | Kusto フィードバックフォーラムへのリンクを開きます。 | 
|更新プログラムの確認     | お使いのバージョンの Kusto に更新プログラムがあるかどうかを確認します。 | 

## <a name="connections-panel"></a>接続パネル

:::image type="content" source="images/kusto-explorer/connections-panel.png" alt-text="Kusto Explorer 接続パネル":::

[接続] ウィンドウには、構成されているすべてのクラスター接続が表示されます。 クラスターごとに、データベース、テーブル、およびそれらに格納されている属性 (列) が表示されます。 [項目] を選択します ([メイン] パネルの検索/クエリに対して暗黙的なコンテキストを設定します)。または、[項目] をダブルクリックして、[検索]/[クエリ] パネルに名前をコピーします。

実際のスキーマが大きい場合 (数百のテーブルを含むデータベースの場合など)、CTRL キーを押し**ながら F**キーを押して、検索対象のエンティティ名の部分文字列 (大文字と小文字を区別しない) を入力します。

Kusto. エクスプローラーでは、クエリウィンドウからの接続パネルの制御がサポートされています。これは、スクリプトに便利です。 たとえば、次の構文を使用して、スクリプトによってクエリされるデータを含むクラスター/データベースに接続するように Kusto エクスプローラーに指示するコマンドを使用してスクリプトファイルを開始できます。

<!-- csl -->
```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

、、または同様のを使用して各行を実行し `F5` ます。

### <a name="control-the-user-identity-connecting-to-kustoexplorer"></a>Kusto に接続するユーザー id を制御します。

新しい接続の既定のセキュリティモデルは、AAD フェデレーションセキュリティです。 認証は、既定の AAD ユーザーエクスペリエンスを使用して Azure Active Directory によって行われます。

認証パラメーターをより細かく制御する必要がある場合は、[詳細: 接続文字列] 編集ボックスを展開し、有効な[Kusto 接続文字列](../api/connection-strings/kusto.md)値を指定します。

たとえば、複数の AAD テナントにプレゼンスがあるユーザーは、特定の AAD テナントに id の特定の "プロジェクション" を使用する必要がある場合があります。 これを行うには、次のような接続文字列を指定します (大文字の単語は特定の値に置き換えます)。

```kusto
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

* `AAD_TENANT_OF_CLUSTER`は、クラスターがホストされている AAD テナントのドメイン名または AAD テナント ID (GUID) です。 これは、通常、クラスターを所有する組織のドメイン名です (など) `contoso.com` 。 
* USER_DOMAIN は、そのテナントに招待されたユーザーの id です (など `user@example.com` )。 

>[!Note]
> ユーザーのドメイン名は、クラスターをホストしているテナントの名前と必ずしも同じではありません。

:::image type="content" source="images/kusto-explorer/advanced-connection-string.png" alt-text="Kusto Explorer の高度な接続文字列":::

## <a name="keyboard-shortcuts"></a>キーボード ショートカット

キーボードショートカットを使用すると、マウスよりも操作を高速に実行できることがわかります。 詳細については、この[Kusto. エクスプローラーのキーボードショートカットの一覧](kusto-explorer-shortcuts.md)を参照してください。

## <a name="table-row-colors"></a>テーブルの行の色

Kusto。エクスプローラーは、結果パネルで各行の重大度または詳細レベルを解釈し、それに応じて色を設定しようとします。 これを行うには、各列の個別の値を一連の既知のパターン ("警告"、"エラー" など) と照合します。

出力の配色を変更するか、この動作をオフにするには、[**ツール**] メニューの [**オプション**] [  >  **結果ビューアー**の  >  **詳細設定**] [配色] を選択します。

:::image type="content" source="images/kusto-explorer/ke-color-scheme.png" alt-text="Kusto Explorer の配色の変更":::

## <a name="next-steps"></a>次の手順

Kusto の操作の詳細については、次を参照してください。

* [Kusto.Explorer の使用](kusto-explorer-using.md)
* [Kusto. エクスプローラーのキーボードショートカット](kusto-explorer-shortcuts.md)
* [Kusto.Explorer のオプション](kusto-explorer-options.md)
* [Kusto.Explorer のトラブルシューティング](kusto-explorer-troubleshooting.md)

Kusto エクスプローラーのツールとユーティリティの詳細については、以下を参照してください。
* [Kusto. エクスプローラーコードアナライザー](kusto-explorer-code-analyzer.md)
* [Kusto. エクスプローラーコードナビゲーション](kusto-explorer-codenav.md)
* [Kusto. エクスプローラーコードのリファクタリング](kusto-explorer-refactor.md)
* [Kusto クエリ言語 (KQL)](https://docs.microsoft.com/azure/kusto/query/)
