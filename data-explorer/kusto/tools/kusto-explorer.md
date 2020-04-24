---
title: Kusto. エクスプローラーツール-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの Kusto エクスプローラーツールについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 1a643a282deec5a98230a17e7335fff7638812b8
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108441"
---
# <a name="kustoexplorer-tool"></a>Kusto. エクスプローラーツール

Kusto. エクスプローラーは、Kusto クエリ言語を使用してデータを探索できる、豊富なデスクトップアプリケーションです。

## <a name="getting-the-tool"></a>ツールの入手

* [Kusto エクスプローラーツール](https://aka.ms/Kusto.Explorer)をインストールする

* または、お使いのブラウザーで Kusto クラスターに`https://<your_cluster>.kusto.windows.net`アクセスします。 <your_cluster> を Azure データエクスプローラークラスター名に置き換えます。



## <a name="using-chrome-and-kustoexplorer"></a>Chrome と Kusto. エクスプローラーを使用する

Chrome を既定のブラウザーとして使用する場合は、Chrome 用の ClickOnce 拡張機能をインストールしてください。

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)



## <a name="overview-of-the-user-experience"></a>ユーザーエクスペリエンスの概要

Kusto Explorer ウィンドウには、いくつかの UI パーツがあります。

1. [メニューパネル](#menu-panel)
2. [接続パネル](#connections-panel)
3. スクリプトパネル
4. 結果パネル

![Kusto. エクスプローラーの起動](./Images/KustoTools-KustoExplorer/ke-start.png "ke-開始")

### <a name="keyboard-shortcuts"></a>キーボード ショートカット

キーボードショートカットを使用すると、マウスよりも操作を高速に実行できることがわかります。 この[Kusto エクスプローラーのキーボードショートカットの一覧](kusto-explorer-shortcuts.md)を見てみましょう。

### <a name="menu-panel"></a>メニューパネル

Kusto. エクスプローラーのメニューパネルには、次のタブがあります。

* [ホーム](#home-tab)
* [[最近使ったファイル]](#file-tab)
* [接続](#connections-tab)
* [表示](#view-tab)
* [ツール](#tools-tab)

* [管理](#management-tab)
* [ヘルプ](#help-tab)

### <a name="home-tab"></a>[ホーム] タブ

![Kusto. エクスプローラーホーム](./Images/KustoTools-KustoExplorer/home-tab.png "[ホーム] タブ")

[ホーム] タブには、最近使用した関数がセクションに分けて表示されます。

* [クエリ](#query-section)
* [共有](#share-section)
* [視覚化](#visualizations-section)
* [表示](#view-section)
* [ヘルプ](#help-tab) 

#### <a name="query-section"></a>クエリセクション

![Kusto. エクスプローラーの [クエリ] メニュー](./Images/KustoTools-KustoExplorer/home-query-menu.png "クエリ-メニュー")

|メニュー|    動作|
|----|----------|
|モードのドロップダウン | <ul><li>クエリモード: クエリウィンドウを[スクリプトモード](#query-mode)に切り替えます。 コマンドはスクリプトとして読み込んで保存できます (既定)。</li> <li> 検索モード: 入力した各コマンドが直ちに処理され、結果ウィンドウに結果が表示される単一のクエリモード</li> <li>Search + + モード: 1 つ以上のテーブルで検索構文を使用して用語を検索できます。 [検索 + + モード](kusto-explorer.md#search-mode)の使用に関する詳細情報</li></ul> |
|新しいタブ| Kusto に対するクエリを実行するための新しいタブを開きます。 |

#### <a name="share-section"></a>セクションを共有する

![Kusto. エクスプローラーの共有セクション](./Images/KustoTools-KustoExplorer/home-share-menu.png "共有メニュー")

|メニュー|    動作|
|----|----------|
|クリップボードへのデータ|    クエリとデータセットをクリップボードにエクスポートします。 グラフが表示される場合は、グラフをビットマップとしてエクスポートします。| 
|結果をクリップボードに出力| データセットをクリップボードにエクスポートします。 グラフが表示される場合は、グラフをビットマップとしてエクスポートします。| 
|クリップボードへのクエリ| クエリをクリップボードにエクスポートします。|

#### <a name="visualizations-section"></a>視覚エフェクトセクション

![alt text](./Images/KustoTools-KustoExplorer/home-visualizations-menu.png "メニュー-視覚エフェクト")

|メニュー         | 動作|
|-------------|---------|
|面グラフ      | X 軸が最初の列 (数値である必要があります) で、すべての数値列が異なる系列 (Y 軸) にマップされている面グラフを表示します。 |
|Column Chart | すべての数値列が異なる系列 (Y 軸) にマップされ、数値が X 軸になる前のテキスト列 (UI で制御可能) を表す縦棒グラフを表示します。|
|横棒グラフ    | すべての数値列が異なる系列 (X 軸) にマップされ、数値が Y 軸 (UI で制御可能) になる前のテキスト列が横棒グラフで表示されます。|
|積み上げ面グラフ      | X 軸が最初の列 (数値である必要があります) と、すべての数値列が異なる系列 (Y 軸) にマップされる積み上げ面グラフを表示します。 |
|タイムライングラフ   | X 軸が最初の列 (datetime である必要があります) で、すべての数値列が異なる系列 (Y 軸) にマップされる時間グラフを表示します。|
|折れ線グラフ   | X 軸が最初の列 (数値である必要があります) と、すべての数値列が異なる系列 (Y 軸) にマップされた折れ線グラフを表示します。|
|異常グラフ|    線上に似ていますが、機械学習の異常アルゴリズムを使用して、時系列データの異常を検出します。 異常検出の場合、Kusto は[series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md)関数を使用します。(*) 
|円グラフ    |    カラー軸が最初の列で、シータ軸 (メジャーである必要があります) が2番目の列である円グラフを表示します。|
|ラダーグラフ |    X 軸が最後の2つの列 (datetime である必要があります) と Y 軸が他の列の複合型である、ラダーグラフを表示します。|
|散布図| X 軸が最初の列 (数値である必要があります) で、すべての数値列が異なる系列 (Y 軸) にマップされるポイントグラフを表示します。|
|[ピボット グラフ]  | Displaya ピボットテーブルとピボットグラフを使用すると、データ、列、行、さまざまな種類のグラフを柔軟に選択できます。| 
|時間ピボット   | イベントのタイムラインでの対話型ナビゲーション (タイム軸でのピボット)|

(*)異常チャート: アルゴリズムでは、次の2つの列で構成される timeseries データが必要です。
1. 固定間隔バケットの時間
2. Kusto に生成する異常検出の数値。時間フィールドで集計し、タイムバケットビンを指定します。

#### <a name="view-section"></a>セクションの表示

![alt text](./Images/KustoTools-KustoExplorer/home-view-menu.png "[表示] メニュー")

|メニュー           | 動作|
|---------------|---------|
|全表示モード | リボンメニューと接続パネルを非表示にしてワークスペースを最大化します|
|空の列を非表示にする| データグリッドから空の列を削除します。|
|単数形の列を折りたたむ| 単数形の値で列を折りたたむ|
|列の値を調べる| 列の値の分布を表示します|
|フォントの拡大  | [クエリ] タブおよび結果データグリッドのフォントサイズを大きくします。|  
|フォントの縮小  | [クエリ] タブおよび結果データグリッドのフォントサイズを小さくします。|

(*)データビューの設定: Kusto. エクスプローラーは、一意の列セットごとにどの設定が使用されているかを追跡します。 列が並べ替えられたり削除されたりすると、データビューが保存され、同じ列を持つデータが取得されるたびに再利用されます。 ビューを既定値にリセットするには、[**表示**] タブで [**ビューのリセット**] を選択します。 

### <a name="file-tab"></a>[ファイル] タブ

![Kusto. エクスプローラーファイル](./Images/KustoTools-KustoExplorer/file-tab.png "[ファイル] タブ")

|メニュー| 動作|
|---------------|---------|
||---------*クエリスクリプト*---------|
|新しいタブ | Kusto に対するクエリを実行するための新しいタブウィンドウを開きます |
|[ファイルを開く]| * Kql ファイルからアクティブスクリプトパネルにデータを読み込みます。|
|ファイルに保存| アクティブスクリプトパネルの内容を * kql ファイルに保存します。|
|タブを閉じる| 現在のタブウィンドウを閉じます。|
||---------*データの保存*---------|
|CSV に       | CSV (コンマ区切り値) ファイルにデータをエクスポートします| 
|To JSON      | JSON 形式のファイルにデータをエクスポートします|
|Excel に     | .XLSX (Excel) ファイルにデータをエクスポートします|
|テキストに      |    TXT (テキスト) ファイルにデータをエクスポートします。| 
|CSL スクリプトに|    クエリをスクリプトファイルにエクスポートします| 
|結果に   |    クエリとデータを結果 (QRES) ファイルにエクスポートします|
||---------*データの読み込み*---------|
|結果から|    クエリとデータを結果 (QRES) ファイルから読み込みます。| 
||---------*Clipboard*---------|
|クリップボードへのデータ|    クエリとデータセットをクリップボードにエクスポートします。 グラフが表示される場合は、グラフをビットマップとしてエクスポートします。| 
|結果をクリップボードに出力| データセットをクリップボードにエクスポートします。 グラフが表示される場合は、グラフをビットマップとしてエクスポートします。| 
|クリップボードへのクエリ| クエリをクリップボードにエクスポートします|
||---------*生じ*---------|
|結果キャッシュのクリア| 以前に実行されたクエリのキャッシュされた結果をクリアします| 

### <a name="connections-tab"></a>[接続] タブ

![Kusto. エクスプローラーの [接続] タブ](./Images/KustoTools-KustoExplorer/connections-tab.png "[接続]-タブ")

|メニュー|動作|
|----|----------|
||---------*グループ*---------|
|グループの追加| 新しい Kusto サーバーグループを追加します。|
|グループ名の変更| 既存の Kusto サーバーグループの名前を変更します。|
|グループの削除| 既存の Kusto サーバーグループを削除します。|
||---------*クラスター*---------|
|インポートの接続| 接続を指定するファイルから接続をインポートします|
|接続のエクスポート| ファイルへの接続をエクスポートします。|
|接続の追加| 新しい Kusto サーバー接続を追加します。| 
|接続の編集| Kusto サーバーの接続プロパティを編集するためのダイアログを開きます。|
|接続の削除| Kusto サーバーへの既存の接続を削除します。|
|更新| Kusto サーバー接続のプロパティを更新します。|
||---------*Id プロバイダー*---------|
|接続プリンシパルの検査| 流れ着くアクティブユーザーの詳細を表示します|
|AAD からサインアウトする| AAD への接続から現在のユーザーをサインアウトします|
||---------*データスコープ*---------|
|キャッシュスコープ|<ul><li>ホット[データキャッシュ](../management/cachepolicy.md)に対してのみホット dataexecute クエリを実行する</li><li>すべてのデータ: 使用可能なすべてのデータに対してクエリを実行します (既定)。</li></ul> |
|DateTime 列| 時間前フィルターに使用できる列の名前|
|時間フィルター| 時間前フィルターの値|

### <a name="view-tab"></a>[表示] タブ

![[表示] タブ](./Images/KustoTools-KustoExplorer/view-tab.png "[表示] タブ")

|メニュー|動作|
|----|----------|
||---------*内容*---------|
|全表示モード | リボンメニューと接続パネルを非表示にしてワークスペースを最大化します|
|フォントの拡大  | [クエリ] タブおよび結果データグリッドのフォントサイズを大きくします。|  
|フォントの縮小  | [クエリ] タブおよび結果データグリッドのフォントサイズを小さくします。|
|レイアウトのリセット|ツールのドッキングコントロールとウィンドウのレイアウトをリセットします。|
||---------*データビュー*---------|
|ビューのリセット| データビューの設定をリセットします (*)|
|列の値を調べる|列の値の分布を表示します|
|クエリ統計にフォーカスする|クエリの完了時にクエリの結果ではなくクエリの統計にフォーカスを変更します|
|重複を非表示|クエリ結果からの重複行の削除を切り替えます。|
|空の列を非表示にする|クエリ結果からの空の列の削除を切り替えます|
|単数形の列を折りたたむ|単数形の値による列の折りたたみを切り替えます|
||---------*データのフィルター処理*---------|
|検索で行をフィルター処理する|クエリ結果の検索で一致する行のみを表示するオプションを切り替えます (Ctrl + F)|
||---------*効果*---------|
|視覚化|上の「[視覚化](#visualizations-section)」を参照してください。 |

(*)データビューの設定: Kusto. エクスプローラーは、一意の列セットごとにどの設定が使用されているかを追跡します。 列が並べ替えられたり削除されたりすると、データビューが保存され、同じ列を持つデータが取得されるたびに再利用されます。 ビューを既定値にリセットするには、[**表示**] タブで [**ビューのリセット**] を選択します。 

### <a name="tools-tab"></a>[ツール] タブ

![[ツール] タブ](./Images/KustoTools-KustoExplorer/tools-tab.png "[ツール]-タブ")

|メニュー|動作|
|----|----------|
||---------*Tab*---------|
|[IntelliSense を有効にする]| スクリプトパネルで IntelliSense を有効または無効にします。|
||---------*検討*---------|
|クエリ アナライザー| クエリアナライザーツールを起動します。|
|クエリチェッカー | 現在のクエリを分析し、適用可能な改善に関する推奨事項のセットを出力します。|
|Calculator| 電卓を起動します|
||---------*解析*---------|
|分析レポート| データ分析用にビルド済みの複数のレポートを含むダッシュボードを開きます|
||---------*翻訳*---------|
|クエリを Power BI| クエリをのの使用に適した形式に変換し Power BI|
||---------*オプション*---------|
|リセットオプション| アプリケーション設定を既定値に設定します|
|Options| アプリケーション設定を構成するためのツールを開きます。 [詳細](kusto-explorer-options.md)|



### <a name="management-tab"></a>[管理] タブ

![[管理] タブ](./Images/KustoTools-KustoExplorer/management-tab.png "[管理] タブ")

|メニュー             | 動作|
|-----------------|---------|
||---------*承認されたプリンシパル*---------|
|クラスターの承認されたプリンシパルを管理する |承認されたユーザーに対するクラスターのプリンシパルの管理を有効にします。| 
|データベース承認済みプリンシパルの管理 | 承認されたユーザーのデータベースプリンシパルの管理を有効にします。| 
|テーブルの承認されたプリンシパルの管理 | 承認されたユーザーのテーブルプリンシパルの管理を有効にします。| 
|関数の承認されたプリンシパルの管理 | 権限のあるユーザーのための関数のプリンシパルの管理を有効にします。| 

### <a name="help-tab"></a>[ヘルプ] タブ

![[ヘルプ] タブ](./Images/KustoTools-KustoExplorer/help-tab.png "[ヘルプ] タブ")

|メニュー             | 動作|
|-----------------|---------|
||---------*書*---------|
|Help             | Kusto オンラインドキュメントへのリンクを開きます。  | 
|新機能       | すべての Kusto エクスプローラーの変更を一覧表示するドキュメントを開きます。|
|Report Issue (問題の報告)      |次の2つのオプションでダイアログを開きます。 <ul><li>サービスに関連する問題を報告する</li><li>クライアントアプリケーションの問題を報告する</li></ul> | 
|機能の提案  | Kusto フィードバックフォーラムへのリンクを開きます。 | 
|更新プログラムの確認     | お使いのバージョンの Kusto に更新プログラムがあるかどうかを確認します。 | 

## <a name="connections-panel"></a>接続パネル

![alt text](./Images/KustoTools-KustoExplorer/connectionsPanel.png "接続-パネル") 

Kusto エクスプローラーの左側のウィンドウに、クライアントが構成されているすべてのクラスター接続が表示されます。 各クラスターには、格納するデータベース、テーブル、および属性 (列) が表示されます。 [接続] パネルでは、項目を選択できます (メインパネルの検索/クエリに対して暗黙的なコンテキストを設定します)。または、項目をダブルクリックして、名前を [検索/クエリ] パネルにコピーします。

実際のスキーマが大きい場合 (数百のテーブルを含むデータベースなど)、CTRL キーを押しながら F キーを押して、探しているエンティティ名の部分文字列 (大文字と小文字を区別しない) を入力することにより、スキーマを検索することができます。

Kusto. エクスプローラーでは、クエリウィンドウからの接続パネルの制御がサポートされています。
これは、スクリプトに非常に便利です。 たとえば、スクリプトによってクエリされるデータを含むクラスター/データベースに接続するために Kusto エクスプローラーに指示するコマンドを使用してスクリプトファイルを起動する場合は、次の構文を使用できます。 通常どおり、または類似し`F5`た行を実行する必要があります。

```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

### <a name="controlling-the-user-identity-used-for-connecting-to-kusto"></a>Kusto への接続に使用するユーザー id の制御

新しい接続を追加するときに使用される既定のセキュリティモデルは AAD フェデレーションセキュリティで、既定の AAD ユーザーエクスペリエンスを使用して Azure Active Directory を通じて認証が行われます。

場合によっては、AAD で使用できる認証パラメーターよりも詳細な制御が必要になることがあります。 その場合は、[詳細: 接続文字列] 編集ボックスを展開して、有効な[Kusto 接続文字列](../api/connection-strings/kusto.md)値を指定することができます。

たとえば、複数の AAD テナントにプレゼンスがあるユーザーは、特定の AAD テナントに id の特定の "プロジェクション" を使用することが必要になる場合があります。 これを行うには、次のような接続文字列を指定します (大文字の単語は特定の値に置き換えます)。

```
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

一意であること`AAD_TENANT_OF_CLUSTER`は、クラスターがホストされている aad テナントのドメイン名または AAD テナント ID (GUID) (通常は、クラスターを所有する組織のドメイン名`contoso.com`)、USER_DOMAIN はそのテナントに招待されたユーザーの id ( `joe@fabrikam.com`など) です。 

>[!Note]
> ユーザーのドメイン名は、クラスターをホストしているテナントの名前と必ずしも同じではありません。

## <a name="table-row-colors"></a>テーブルの行の色

Kusto。エクスプローラーは、結果ペインで各行の重大度または詳細レベルを "推測" し、それに応じて色を設定しようとします。 これを行うには、各列の個別の値を一連の既知のパターン ("警告"、"エラー" など) と照合します。

出力の配色を変更するか、この動作をオフにするには、[**ツール**] メニューの [**オプション** > ] [**結果ビューアー** > の**詳細設定**] [配色] を選択します。

![alt text](./Images/KustoTools-KustoExplorer/ke-color-scheme.png)

## <a name="search-mode"></a>検索 + + モード

1. [ホーム] タブの [クエリ] ドロップダウンで、[検索 + +] を選択します。
2. **複数のテーブル**を選択し、[**テーブルの選択**] で検索するテーブルを定義します。
3. [編集] ボックスで、検索語句を入力して [検索]**を選択し**ます。
4. テーブル/タイムスロットグリッドのヒートマップには、表示される用語とその表示場所が示されます。
5. グリッド内のセルを選択し、[**詳細の表示**] を選択して関連するエントリを表示します。

![alt text](./Images/KustoTools-KustoExplorer/ke-search-beta.jpg "ke-検索-ベータ") 

## <a name="query-mode"></a>クエリ モード

Kusto. エクスプローラーには、アドホッククエリの作成、編集、および実行を可能にする強力なスクリプトモードが用意されています。 スクリプトモードには、構文の強調表示と IntelliSense が用意されているため、Kusto CSL 言語まですばやく拡大できます。

### <a name="basic-queries"></a>基本的なクエリ

テーブルログがある場合は、次のように入力することで、そのログを調べることができます。

```kusto
StormEvents | count 
```

カーソルがこの行に配置されると、灰色で表示されます。 F5 キーを押すとクエリが実行されます。 

次に、クエリの例をいくつか示します。

```kusto
// Take 10 lines from the table. Useful to get familiar with the data
StormEvents | limit 10 
```

```kusto
// Filter by EventType == 'Flood' and State == 'California' (=~ means case insensitive) 
// and take sample of 10 lines
StormEvents 
| where EventType == 'Flood' and State =~ 'California'
| limit 10
```

## <a name="importing-a-local-file-into-a-kusto-table"></a>Kusto テーブルへのローカルファイルのインポート

Kusto. エクスプローラーを使用すると、コンピューターから Kusto テーブルにファイルを簡単にアップロードできます。

1. ファイルに一致するスキーマを使用してテーブルを作成したことを確認します (たとえば、 [. create table](../management/tables.md)コマンドを使用します)。

1. ファイル拡張子がファイルの内容に適していることを確認します。 たとえば、次のように入力します。
    * ファイルにコンマ区切りの値が含まれている場合は、ファイルの拡張子が .csv であることを確認してください。
    * ファイルにタブ区切りの値が含まれている場合は、ファイルの拡張子が .tsv であることを確認します。

1. [[接続] パネル](#connections-panel)でターゲットデータベースを右クリックし、[**更新**] を選択して、テーブルが表示されるようにします。

    ![alt text](./Images/KustoTools-KustoExplorer/right-click-refresh-schema.png "[-更新-スキーマ] を右クリックします。")

1. [[接続] パネル](#connections-panel)でターゲットテーブルを右クリックし、[**ローカルファイルからデータをインポート**] を選択します。

    ![alt text](./Images/KustoTools-KustoExplorer/right-click-import-local-file.png "右クリック-ローカル-ファイル")

1. アップロードするファイルを選択し、[**開く**] を選択します。

    ![alt text](./Images/KustoTools-KustoExplorer/import-local-file-choose-files.png "ローカルファイルのインポート-ファイルの選択")

    進行状況バーに進行状況が表示され、操作が完了するとダイアログが表示されます。

    ![alt text](./Images/KustoTools-KustoExplorer/import-local-file-progress.png "ローカルファイルのインポート-進行状況")

    ![alt text](./Images/KustoTools-KustoExplorer/import-local-file-complete.png "ローカルファイルのインポート-完了")

1. テーブル内のデータに対してクエリを実行します ([[接続] パネル](#connections-panel)のテーブルをダブルクリックします)。

## <a name="managing-authorized-principals"></a>承認されたプリンシパルの管理

Kusto. エクスプローラーを使用すると、クラスター、データベース、テーブル、または機能が承認されたプリンシパルを簡単に管理できます。

> [!Note]
> 承認されたプリンシパルを独自のスコープ内に追加または削除できるのは[管理者](../management/access-control/role-based-authorization.md)だけです。

1. [[接続] パネル](#connections-panel)でターゲットエンティティを右クリックし、[**承認**されたプリンシパルの管理] を選択します。 (これは、[管理] メニューからも実行できます)。

    ![alt text](./Images/KustoTools-KustoExplorer/right-click-manage-authorized-principals.png "右クリック-管理-承認されたプリンシパル")

    ![alt text](./Images/KustoTools-KustoExplorer/manage-authorized-principals-window.png "管理-承認されたプリンシパル-ウィンドウ")

1. 新しい承認されたプリンシパルを追加するには、[**プリンシパルの追加**] を選択し、プリンシパルの詳細を入力して、操作を確定します。

    ![alt text](./Images/KustoTools-KustoExplorer/add-authorized-principals-window.png "追加-承認されたプリンシパル-ウィンドウ")

    ![alt text](./Images/KustoTools-KustoExplorer/confirm-add-authorized-principals.png "confirm-add-承認済みプリンシパル")

1. 既存の承認されたプリンシパルを削除するには、[**プリンシパルの削除**] を選択して操作を確定します。

    ![alt text](./Images/KustoTools-KustoExplorer/confirm-drop-authorized-principals.png "confirm-drop-承認されたプリンシパル")

## <a name="sharing-queries-and-results-by-email"></a>クエリと結果を電子メールで共有する

Kusto. エクスプローラーは、クエリとクエリ結果を電子メールで共有するための便利な方法を提供します。 [**クリップボードにエクスポート**] を選択すると、次の項目がクリップボードにコピーされます。
1. クエリ
1. クエリ結果 (テーブルまたはグラフ)
1. Kusto クラスターとデータベースの接続の詳細
1. 自動的にクエリを再実行するリンク

しくみは次のとおりです。

1. Kusto エクスプローラーでクエリを実行する
1. [**クリップボードにエクスポート**] ( `Ctrl+Shift+C`または押す) を選択します。

    ![alt text](./Images/KustoTools-KustoExplorer/menu-export.png "メニュー-エクスポート")

1. たとえば、新しい Outlook メッセージを開きます。

    ![alt text](./Images/KustoTools-KustoExplorer/share-results.png "共有-結果")
    
1. クリップボードの内容を Outlook メッセージに貼り付けます。

    ![alt text](./Images/KustoTools-KustoExplorer/share-results-2.png "共有-結果-2")

## <a name="client-side-query-parametrization"></a>クライアント側のクエリ parametrization

> [!WARNING]
> Kusto には、次の2種類のクエリ parametrization 手法があります。
> * [統合言語クエリ parametrization](../query/queryparametersstatement.md)は、クエリエンジンの一部として実装されており、プログラムによってサービスをクエリするアプリケーションによって使用されます。
>
> * 以下で説明するクライアント側クエリ parametrization は、Kusto. エクスプローラーアプリケーションの機能です。 これは、サービスによって実行されるように送信する前に、クエリに対して文字列置換操作を使用することと同じです。 次に示す構文はクエリ言語自体の一部ではなく、Kusto エクスプローラー以外の方法でサービスにクエリを送信するときには使用できません。

複数のクエリまたは複数のタブで同じ値を使用する予定がある場合は、それを変更するのが困難になります。 ただし、Kusto. エクスプローラーではクエリパラメーターがサポートされています。 パラメーターは、角{}かっこで示されます。 たとえば次のようになります。`{parameter1}`

スクリプトエディターでは、クエリパラメーターが強調表示されます。

![alt text](./Images/KustoTools-KustoExplorer/parametrized-query-1.png "パラメーター化-1")

既存のクエリパラメーターを簡単に定義および編集できます。

![alt text](./Images/KustoTools-KustoExplorer/parametrized-query-2.png "パラメーター化-2")

![alt text](./Images/KustoTools-KustoExplorer/parametrized-query-3.png "パラメーター化-3")

スクリプトエディターには、既に定義されているクエリパラメーターの IntelliSense もあります。

![alt text](./Images/KustoTools-KustoExplorer/parametrized-query-4.png "パラメーター化-4")

複数のパラメーターセットを指定できます ([**パラメーターセット**] コンボボックスに表示されます)。
パラメーターセットの一覧を操作するには、 **Add new**と**Delete current**を使用します。

![alt text](./Images/KustoTools-KustoExplorer/parametrized-query-5.png "パラメーター化-5")

クエリパラメーターはタブ間で共有されるため、簡単に再利用できます。

## <a name="deep-linking-queries"></a>ディープリンククエリ

### <a name="overview"></a>概要
ブラウザーで開いたときに、Kusto. エクスプローラーがローカルで起動し、指定した Kusto データベースに対して特定のクエリを実行する URI を作成できます。

### <a name="limitations"></a>制限事項
クエリは、Internet Explorer の制限によって最大2000文字までに制限されています (制限はクラスターとデータベース名の長さhttps://support.microsoft.com/kb/208427に依存しているため、この制限はクラスターとデータベース名の長さに依存しているため、文字数の制限に達する可能性があります。以下の「[短いリンクを取得](#getting-shorter-links)する」を参照)。

URI の形式は次のとおりです<ClusterCname>: https://<DatabaseName>. kusto.windows.net/? query =<QueryToExecute>

次に例 を示します。https://help.kusto.windows.net/Samples?query=StormEvents+%7c+limit+10
 
この URI は Kusto. エクスプローラーを開き、 `help` kusto クラスターに接続して、 `Samples`データベースに対して指定されたクエリを実行します。 既に実行されている Kusto. エクスプローラーのインスタンスがある場合は、実行中のインスタンスによって新しいタブが開き、そこでクエリが実行されます。)

**セキュリティ**に関する注意: セキュリティ上の理由から、コントロールコマンドに対してディープリンクは無効になっています。



### <a name="creating-a-deep-link"></a>ディープリンクの作成
ディープリンクを作成する最も簡単な方法は、Kusto エクスプローラーでクエリを作成し、[クリップボードにエクスポート] を使用してクエリ (ディープリンクや結果を含む) をクリップボードにコピーする方法です。 その後、電子メールで共有できます。
        
電子メールにコピーすると、ディープリンクが小さいフォントで表示されます。例えば：

https://help.kusto.windows.net:443/Samples[[クリックしてクエリを実行](https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d)] 

最初のリンクを開くと、Kusto. エクスプローラーが開き、クラスターとデータベースのコンテキストが適切に設定されます。
2番目のリンク (クリックしてクエリを実行) はディープリンクです。 電子メールメッセージへのリンクに移動し、CTRL + K キーを押すと、実際の URL が表示されます。

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

### <a name="deep-links-and-parametrized-queries"></a>ディープリンクとパラメーター化クエリ

ディープリンクでパラメーター化クエリを使用できます。

1. パラメーター化クエリとして書式設定するクエリを作成します ( `KustoLogs | where Timestamp > ago({Period}) | count`たとえば、)。 
2. この場合は、URI 内のすべてのクエリパラメーターにパラメーターを指定します。

`https://mycluster.kusto.windows.net/MyDatabase?web=0&query=KustoLogs+%7c+where+Timestamp+>+ago({Period})+%7c+count&Period=1h`

### <a name="getting-shorter-links"></a>短いリンクの取得

クエリが長くなる可能性があります。 クエリが最大長を超える可能性を減らすには、 `String Kusto.Data.Common.CslCommandGenerator.EncodeQueryAsBase64Url(string query)` Kusto クライアントライブラリで使用可能なメソッドを使用します。 この方法では、よりコンパクトな形式のクエリが生成されます。 短い形式は、Kusto. エクスプローラーでも認識されます。

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

次の変換を適用することで、クエリがより簡潔になります。

```csharp
 UrlEncode(Base64Encode(GZip(original query)))
```

## <a name="kustoexplorer-command-line-arguments"></a>Kusto. エクスプローラーのコマンドライン引数

Kusto. エクスプローラーでは、次の構文でいくつかのコマンドライン引数がサポートされています (順序は重要です)。

[*LocalScriptFile*][*QueryString*]

各値の説明:
* *LocalScriptFile*ローカルコンピューター上のスクリプトファイルの名前を指定します。この名前は`.kql`拡張子を持つ必要があります。 このようなファイルが存在する場合、このファイルは起動時に自動的に読み込まれます。
* *QueryString*は、HTTP クエリ文字列の書式設定を使用して書式設定された文字列です。 このメソッドは、次の表で説明するように、追加のプロパティを提供します。

たとえば、という`c:\temp\script.kql`スクリプトファイルで Kusto. エクスプローラーを起動し、cluster `help` `Samples`と通信するように構成されている場合は、次のコマンドを使用します。

```
Kusto.Explorer.exe c:\temp\script.kql uri=https://help.kusto.windows.net/Samples;Fed=true&name=Samples
```

|引数  |説明                                                               |
|----------|--------------------------------------------------------------------------|
|**実行するクエリ**                                                                 |
|`query`   |実行するクエリ (base64 エンコード)。 空の場合は`querysrc`、を使用します。          |
|`querysrc`|実行するクエリが格納されているファイルまたは blob の`query` URL (が空の場合)。|
|**Kusto クラスターへの接続**                                                  |
|`uri`     |接続先の Kusto クラスターの接続文字列。                 |
|`name`    |Kusto クラスターへの接続の表示名。                  |
|**接続グループ**                                                                 |
|`path`    |ダウンロードする接続グループファイルの URL (URL エンコード)。             |
|`group`   |接続グループの名前。                                         |
|`filename`|接続グループを保持しているローカルファイル。                              |

## <a name="kustoexplorer-connection-files"></a>Kusto. エクスプローラー接続ファイル

Kusto. エクスプローラーは`%LOCALAPPDATA%\Kusto.Explorer` 、その接続設定をフォルダー内に保持します。
接続グループの一覧は内`%LOCALAPPDATA%\Kusto.Explorer\UserConnectionGroups.xml`に保持され、各接続グループはの下`%LOCALAPPDATA%\Kusto.Explorer\Connections\`の専用ファイル内に保持されます。

### <a name="format-of-connection-group-files"></a>接続グループファイルの形式

ファイルの場所は`%LOCALAPPDATA%\Kusto.Explorer\UserConnectionGroups.xml`です。  

これは、次のプロパティを持つ`ServerGroupDescription`オブジェクトの配列の XML シリアル化です。

```
  <ServerGroupDescription>
    <Name>`Connection Group name`</Name>
    <Details>`Full path to XML file containing the list of connections`</Details>
  </ServerGroupDescription>
```

例:

```
<?xml version="1.0"?>
<ArrayOfServerGroupDescription xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <ServerGroupDescription>
    <Name>Connections</Name>
    <Details>C:\Users\alexans\AppData\Local\Kusto.Explorer\UserConnections.xml</Details>
  </ServerGroupDescription>
</ArrayOfServerGroupDescription>  
```

### <a name="format-of-connection-list-files"></a>接続リストファイルの形式

ファイルの場所は`%LOCALAPPDATA%\Kusto.Explorer\Connections\`です。

これは、次のプロパティを持つ`ServerDescriptionBase`オブジェクトの配列の XML シリアル化です。

```
   <ServerDescriptionBase xsi:type="ServerDescription">
    <Name>`Connection name`</Name>
    <Details>`Details as shown in UX, usually full URI`</Details>
    <ConnectionString>`Full connection string to the cluster`</ConnectionString>
  </ServerDescriptionBase>
```

例:

```
<?xml version="1.0"?>
<ArrayOfServerDescriptionBase xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <ServerDescriptionBase xsi:type="ServerDescription">
    <Name>Help</Name>
    <Details>https://help.kusto.windows.net</Details>
    <ConnectionString>Data Source=https://help.kusto.windows.net:443;Federated Security=True</ConnectionString>
  </ServerDescriptionBase>
</ArrayOfServerDescriptionBase>
```

## <a name="resetting-kustoexplorer"></a>Kusto にリセットしています

必要に応じて、Kusto を完全にリセットできます。 コンピューターに展開されている Kusto を完全に削除してからインストールしなければならないようにするには、次の手順を使用します。

1. Windows で、[**プログラムの変更と削除**] ( **[プログラムと機能**] とも呼ばれます) を開きます。
1. 名前がで始まるすべての`Kusto.Explorer`項目を選択します。
1. **[アンインストール]** を選択します。

   これによってアプリケーションがアンインストールされない場合 (ClickOnce アプリケーションで発生する可能性のある既知の問題)、「[このスタックオーバーフロー](https://stackoverflow.com/questions/10896223/how-do-i-completely-uninstall-a-clickonce-application-from-my-computer) 」の記事を参照して、その方法を説明してください。

1. フォルダー `%LOCALAPPDATA%\Kusto.Explorer`を削除します。 これにより、すべての接続と履歴が削除されます。

1. フォルダー `%APPDATA%\Kusto`を削除します。 これにより、Kusto. エクスプローラーのトークンキャッシュが削除されます。 すべてのクラスターに対して再認証を行う必要があります。

また、特定のバージョンの Kusto エクスプローラーに戻すこともできます。

1. `appwiz.cpl` を実行します。
1. [ **Kusto エクスプローラー** ] を選択し、[**アンインストールと変更**] を選択します。
3. [**アプリケーションを以前の状態に復元する**] を選択します。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="kustoexplorer-fails-to-start"></a>Kusto. エクスプローラーが起動に失敗する

#### <a name="kustoexplorer-shows-error-dialog-during-or-after-start-up"></a>Kusto. エクスプローラーの起動時または起動後にエラーダイアログが表示される

**現象:**

起動時に、Kusto. エクスプローラーにエラーが`InvalidOperationException`表示されます。

**考えられる解決策:**

このエラーは、オペレーティングシステムが破損した、または重要なモジュールの一部が不足していることを示唆する場合があります。
不足または破損しているシステムファイルを確認するには、次の手順に従います。   
[https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system](https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system)

### <a name="kustoexplorer-always-downloads-even-when-there-are-no-updates"></a>Kusto. エクスプローラーは、更新プログラムがない場合でも常にダウンロードされます。

**現象:**

Kusto. エクスプローラーを開くたびに、新しいバージョンをインストールするように求められます。 Kusto. エクスプローラーは、インストール済みのバージョンを実際に更新せずに、パッケージ全体をダウンロードします。

**考えられる解決策:**

これは、ローカルの ClickOnce ストアが破損した場合に発生する可能性があります。 管理者特権でのコマンドプロンプトで次のコマンドを実行して、ローカルの ClickOnce ストアを消去できます。
> [!Important]
> 1. ClickOnce アプリケーションまたはの`dfsvc.exe`他のインスタンスがある場合は、このコマンドを実行する前にそれらを終了します。
> 2. ClickOnce アプリは、アプリショートカットに格納されている元のインストール場所にアクセスできる限り、次回の実行時に自動的に再インストールされます。 アプリのショートカットは削除されません。

```
rd /q /s %userprofile%\appdata\local\apps\2.0
```

[インストールミラー](#getting-the-tool)の1つから、Kusto をもう一度インストールしてみてください。

#### <a name="clickonce-error-cannot-start-application"></a>ClickOnce エラー: アプリケーションを開始できません

**現象**  

* プログラムの起動に失敗し、次のものを含むエラーが表示されます。`External component has thrown an exception`
* プログラムの起動に失敗し、次のものを含むエラーが表示されます。`Value does not fall within the expected range`
* プログラムの起動に失敗し、次のものを含むエラーが表示されます。`The application binding data format is invalid.` 
* プログラムの起動に失敗し、次のものを含むエラーが表示されます。`Exception from HRESULT: 0x800736B2`

エラーの詳細を調べるには、 `Details`次のエラーダイアログをクリックします。

![alt text](./Images/KustoTools-KustoExplorer/clickonce-err-1.jpg "clickonce-err-1")

```
Following errors were detected during this operation.
    * System.ArgumentException
        - Value does not fall within the expected range.
        - Source: System.Deployment
        - Stack trace:
            at System.Deployment.Application.NativeMethods.CorLaunchApplication(UInt32 hostType, String applicationFullName, Int32 manifestPathsCount, String[] manifestPaths, Int32 activationDataCount, String[] activationData, PROCESS_INFORMATION processInformation)
            at System.Deployment.Application.ComponentStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.SubscriptionStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.Activate(DefinitionAppId appId, AssemblyManifest appManifest, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.ProcessOrFollowShortcut(String shortcutFile, String& errorPageUrl, TempFile& deployFile)
            at System.Deployment.Application.ApplicationActivator.PerformDeploymentActivation(Uri activationUri, Boolean isShortcut, String textualSubId, String deploymentProviderUrlFromExtension, BrowserSettings browserSettings, String& errorPageUrl)
            at System.Deployment.Application.ApplicationActivator.ActivateDeploymentWorker(Object state)
```

**提案されたソリューションの手順:**

1. ( `Programs and Features` `appwiz.cpl`) を使用して、kusto エクスプローラーアプリケーションをアンインストールします。

1. を実行`CleanOnlineAppCache`してから、Kusto をもう一度インストールしてみてください。 管理者特権のコマンドプロンプトから次のコマンドを実行します。 
    
    ```
    rundll32 %windir%\system32\dfshim.dll CleanOnlineAppCache
    ```

    [インストールミラー](#getting-the-tool)のいずれかからもう一度 Kusto をインストールします。

1. それでも失敗する場合は、ローカルの ClickOnce ストアを削除します。 ClickOnce アプリは、アプリショートカットに格納されている元のインストール場所にアクセスできる限り、次回の実行時に自動的に再インストールされます。 アプリのショートカットは削除されません。

管理者特権のコマンドプロンプトから次のコマンドを実行します。

    ```
    rd /q /s %userprofile%\appdata\local\apps\2.0
    ```

    Install Kusto.Explorer again from one of the [installation mirrors](#getting-the-tool)

1. それでも失敗する場合は、一時デプロイファイルを削除し、Kusto エクスプローラーのローカル AppData フォルダーの名前を変更します。

    管理者特権のコマンドプロンプトから次のコマンドを実行します。

    ```
    rd /s/q %userprofile%\AppData\Local\Temp\Deployment
    ren %LOCALAPPDATA%\Kusto.Explorer Kusto.Explorer.bak
    ```

    [インストールミラー](#getting-the-tool)の1つから Kusto をもう一度インストールします。

1. 管理者特権のコマンドプロンプトから次のように、Kusto. .bak. .bak からの接続を復元します。

    ```
    copy %LOCALAPPDATA%\Kusto.Explorer.bak\User*.xml %LOCALAPPDATA%\Kusto.Explorer
    ```

1. それでも失敗する場合は、次のように LogVerbosityLevel string 値1を作成して、詳細な ClickOnce ログ記録を有効にします。

`HKEY_CURRENT_USER\Software\Classes\Software\Microsoft\Windows\CurrentVersion\Deployment`を再再現し、詳細な出力をにKEBugReport@microsoft.com送信します。 

#### <a name="clickonce-error-your-administrator-has-blocked-this-application-because-it-potentially-poses-a-security-risk-to-your-computer"></a>ClickOnce エラー: コンピューターにセキュリティ上のリスクが生じる可能性があるため、管理者がこのアプリケーションをブロックしました

**現象:**  
次のいずれかのエラーにより、プログラムをインストールできませんでした:
* `Your administrator has blocked this application because it potentially poses a security risk to your computer`.
* `Your security settings do not allow this application to be installed on your computer.`

**解決策:**

1. これは、別のアプリケーションによって、ClickOnce 信頼プロンプトの既定の動作がオーバーライドされたことが原因である可能性があります。 [このハウツー記事で](https://docs.microsoft.com/visualstudio/deployment/how-to-configure-the-clickonce-trust-prompt-behavior)説明されているように、既定の構成設定を表示し、コンピューター上の実際の設定と比較して、必要に応じてリセットすることができます。

#### <a name="cleanup-application-data"></a>アプリケーションデータのクリーンアップ

前のトラブルシューティングの手順を実行しても Kusto を開始できない場合があります。エクスプローラーを起動するには、ローカルに保存されているデータをクリーニングすることができます。

Kusto エクスプローラーアプリケーションによって保存されるデータについ`C:\Users\\[your alias]\AppData\Local\Kusto.Explorer`ては、「」を参照してください。

> [!NOTE]
> データをクリーニングすると、開いているタブ (回復フォルダー)、保存された接続 (接続フォルダー)、およびアプリケーションの設定 (UserSettings フォルダー) が失われる可能性があります。