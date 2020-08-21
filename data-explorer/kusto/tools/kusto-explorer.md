---
title: Kusto.Explorer のインストールとユーザー インターフェイス
description: Kusto.Explorer の機能について説明し、この機能がデータを探索するのにどのように役立つかについて説明します
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: a96e47eeb8c0a27ffb1f1446b68d6adc8e564e4b
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610646"
---
# <a name="kustoexplorer-installation-and-user-interface"></a>Kusto.Explorer のインストールとユーザー インターフェイス

Kusto.Explorer は、使いやすいユーザー インターフェイスで Kusto クエリ言語を使用してデータを探索できる、リッチ デスクトップ アプリケーションです。 この概要では、Kusto.Explorer のセットアップを開始する方法と、使用するユーザー インターフェイスについて説明します。

Kusto.Explorer を使用すると、次のことができます。
* [データのクエリを実行する](kusto-explorer-using.md#query-mode)。
* テーブル間で[データを検索する](kusto-explorer-using.md#search-mode)。
* さまざまなグラフで[データを視覚化する](#visualizations-section)。
* メールまたはディープ リンクを使用して[クエリと結果を共有する](kusto-explorer-using.md#share-queries-and-results)。

## <a name="installing-kustoexplorer"></a>Kusto.Explorer のインストール

* [https://aka.ms/ke](https://aka.ms/ke) から Kusto.Explorer ツールをダウンロードしてインストールします。

* 代わりに、ブラウザーでご自分の Kusto クラスター (`https://<your_cluster>.kusto.windows.net.`) にアクセスします。
   &lt;your_cluster&gt; をご自分の Azure Data Explorer クラスター名に置き換えます。

### <a name="using-chrome-and-kustoexplorer"></a>Chrome と Kusto.Explorer を使用する

Chrome を既定のブラウザーとして使用する場合は、必ず Chrome 用の ClickOnce 拡張機能をインストールしてください。

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)

## <a name="overview-of-the-user-interface"></a>ユーザー インターフェイスの概要

Kusto.Explorer のユーザー インターフェイスは、他の Microsoft 製品と同様に、タブとパネルに基づくレイアウトで設計されています。 

1. [メニュー パネル](#menu-panel)のタブを移動して、さまざまな操作を実行する
2. [接続パネル](#connections-panel)で接続を管理する
3. スクリプト パネルで実行するスクリプトを作成する
4. 結果パネルにスクリプトの結果を表示する

:::image type="content" source="images/kusto-explorer/ke-start.png" alt-text="Kusto.Explorer の開始":::

## <a name="menu-panel"></a>メニュー パネル

Kusto.Explorer のメニュー パネルには、次のタブがあります。

* [Home](#home-tab)
* [ファイル](#file-tab)
* [接続](#connections-tab)
* [表示](#view-tab)
* [ツール](#tools-tab)
* [Monitoring](#monitoring-tab)
* [管理](#management-tab)
* [ヘルプ](#help-tab)

### <a name="home-tab"></a>[ホーム] タブ

:::image type="content" source="images/kusto-explorer/home-tab.png" alt-text="Kusto.Explorer の [ホーム] タブ":::

[ホーム] タブには、最近使用した機能がセクションに分けて表示されます。

* [クエリ](#query-section)
* [共有](#share-section)
* [視覚化](#visualizations-section)
* [表示](#view-section)
* [ヘルプ](#help-tab) 

### <a name="query-section"></a>[クエリ] セクション

:::image type="content" source="images/kusto-explorer/home-query-menu.png" alt-text="クエリ メニュー Kusto.Explorer":::

|メニュー|    動作|
|----|----------|
|モードのドロップダウン | <ul><li>クエリ モード:クエリ ウィンドウを[スクリプト モード](kusto-explorer-using.md#query-mode)に切り替えます。 コマンドはスクリプトとして読み込んで保存できます (既定)</li> <li> 検索モード:入力した各コマンドが直ちに処理され、結果ウィンドウに結果が表示される単一のクエリ モード</li> <li>検索 ++ モード:1 つ以上のテーブルで検索構文を使用して用語を検索できるようにします。 [検索 ++ モード](kusto-explorer-using.md#search-mode)の使用に関する詳細情報</li></ul> |
|新しいタブ| Kusto に対するクエリを実行するための新しいタブを開きます |

### <a name="share-section"></a>[共有] セクション

:::image type="content" source="images/kusto-explorer/home-share-menu.png" alt-text="Kusto.Explorer の共有メニュー":::

|メニュー|    動作|
|----|----------|
|Data To Clipboard\(データをクリップボードに\)|    クエリとデータセットをクリップボードにエクスポートします。 グラフが表示されている場合は、グラフをビットマップとしてエクスポートします| 
|Result To Clipboard\(結果をクリップボードに\)| データセットをクリップボードにエクスポートします。 グラフが表示されている場合は、グラフをビットマップとしてエクスポートします| 
|Query to Clipboard\(クエリをクリップボードに\)| クエリをクリップボードにエクスポートします|

### <a name="visualizations-section"></a>[視覚化] セクション

:::image type="content" source="images/kusto-explorer/home-visualizations-menu.png" alt-text="Kusto.Explorer の視覚化メニュー":::

|メニュー         | 動作|
|-------------|---------|
|面グラフ      | X 軸 (数値である必要があります) が最初の列である面グラフを表示します。 すべての数値列が異なる系列 (Y 軸) にマップされます |
|Column Chart | すべての数値列が異なる系列 (Y 軸) にマップされる縦棒グラフを表示します。 数値の前のテキスト列は X 軸です (UI で制御可能)|
|横棒グラフ    | すべての数値列が異なる系列 (X 軸) にマップされる横棒グラフを表示します。 数値の前のテキスト列は Y 軸です (UI で制御可能)|
|積み上げ面グラフ      | X 軸 (数値である必要があります) が最初の列である積み上げ面グラフを表示します。 すべての数値列が異なる系列 (Y 軸) にマップされます |
|タイムライン グラフ   | X 軸 (datetime である必要があります) が最初の列である時間グラフを表示します。 すべての数値列が異なる系列 (Y 軸) にマップされます。|
|折れ線グラフ   | X 軸 (数値である必要があります) が最初の列である折れ線グラフを表示します。 すべての数値列が異なる系列 (Y 軸) にマップされます。|
|[異常グラフ](#anomaly-chart)|    時間グラフに似ていますが、機械学習の異常アルゴリズムを使用して、時系列データの異常を検出します。 異常を検出するため、Kusto.Explorer では [series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md) 関数が使用されます。
|円グラフ    |    色軸が最初の列である円グラフを表示します。 シータ軸 (パーセントに変換されたメジャーである必要があります) は 2 番目の列です。|
|Time Ladder\(時間ラダー\) |    X 軸が最後の 2 つの列 (datetime である必要があります) であるラダー グラフを表示します。 Y 軸は、他の列の複合です。|
|散布図| X 軸 (数値である必要があります) が最初の列である点グラフを表示します。 すべての数値列が異なる系列 (Y 軸) にマップされます。|
|[ピボット グラフ]  | データ、列、行、およびさまざまな種類のグラフを柔軟に選択できるピボット テーブルとピボット グラフを表示します。| 
|Time Pivot\(時間ピボット\)   | イベントのタイムラインでの対話型ナビゲーション (時間軸でのピボット)|

> [!NOTE]
> <a id="anomaly-chart">異常グラフ</a>:このアルゴリズムでは、次の 2 つの列で構成される時系列データが必要です。
>* 固定間隔バケットの時間
>* 異常検出の数値。Kusto.Explorer で時系列データを生成するには、時間フィールドで集計し、時間バケット ビンを指定します。

### <a name="view-section"></a>[表示] セクション

:::image type="content" source="images/kusto-explorer/home-view-menu.png" alt-text="Kusto.Explorer の表示メニュー":::

|メニュー           | 動作|
|---------------|---------|
|Full View Mode\(全画面表示モード\) | リボン メニューと接続パネルを非表示にして、ワークスペースを最大化します。 全画面表示モードを終了するには、 **[ホーム]**  >  **[Full View Mode]\(全画面表示モード\)** を選択するか、**F11** キーを押します。|
|Hide Empty Columns\(空の列を非表示にする\)| データ グリッドから空の列を削除します|
|Collapse Singular Columns\(特異値の列を折りたたむ\)| 特異値がある列を折りたたみます|
|Explore Column Values\(列の値を調べる\)| 列の値の分布を表示します|
|フォントの拡大  | [クエリ] タブおよび結果データ グリッドのフォント サイズを大きくします|  
|フォントの縮小  | [クエリ] タブおよび結果データ グリッドのフォント サイズを小さくします|

>[!NOTE]
> データ ビューの設定:
>
> Kusto.Explorer では、一意の列セットごとにどの設定が使用されているかが追跡されます。 列が並べ替えられるか削除されると、データ ビューが保存され、同じ列を持つデータが取得されるたびに再利用されます。 表示を既定値にリセットするには、 **[表示]** タブで、 **[表示のリセット]** を選択します。 

## <a name="file-tab"></a>[ファイル] タブ

:::image type="content" source="images/kusto-explorer/file-tab.png" alt-text="Kusto.Explorer の [ファイル] タブ":::

|メニュー| 動作|
|---------------|---------|
||---------*クエリ スクリプト*---------|
|新しいタブ | Kusto に対するクエリを実行するための新しいタブ ウィンドウを開きます |
|[ファイルを開く]| *.kql ファイルからアクティブ スクリプト パネルにデータを読み込みます|
|ファイルに保存| アクティブ スクリプト パネルの内容を *.kql ファイルに保存します|
|タブを閉じる| 現在のタブ ウィンドウを閉じます|
||---------*データの保存*---------|
|Data To CSV\(データを CSV に\)       | データを CSV (コンマ区切り値) ファイルにエクスポートします| 
|Data To JSON\(データを JSON に\)      | データを JSON 形式のファイルにエクスポートします|
|Data To Excel\(データを Excel に\)     | データを XLSX (Excel) ファイルにエクスポートします|
|Data To Text\(データをテキストに\)      | データを TXT (テキスト) ファイルにエクスポートします| 
|Data To KQL Script\(データを KQL スクリプトに\)| クエリをスクリプト ファイルにエクスポートします| 
|Data To Results\(データを結果に\)   | クエリとデータを結果 (QRES) ファイルにエクスポートします|
|Run Query Into CSV\(クエリを実行して CSV に\) |クエリを実行し、結果をローカルの CSV ファイルに保存します|
||---------*データの読み込み*---------|
|From Results\(結果から\)|    クエリとデータを結果 (QRES) ファイルから読み込みます| 
||---------*クリップボード*---------|
|Query and Results To Clipboard\(クエリと結果をクリップボードに\)|    クエリとデータセットをクリップボードにエクスポートします。 グラフが表示されている場合は、グラフをビットマップとしてエクスポートします| 
|Result To Clipboard\(結果をクリップボードに\)| データ セットをクリップボードにエクスポートします。 グラフが表示されている場合は、グラフをビットマップとしてエクスポートします| 
|Query to Clipboard\(クエリをクリップボードに\)| クエリをクリップボードにエクスポートします|
||---------*結果*---------|
|Clear results cache\(結果のキャッシュをクリア\)| 以前に実行されたクエリのキャッシュされた結果をクリアします| 

## <a name="connections-tab"></a>[接続] タブ

:::image type="content" source="images/kusto-explorer/connections-tab.png" alt-text="Kusto.Explorer の [接続] タブ":::

|メニュー|動作|
|----|----------|
||---------*グループ*---------|
|グループの追加| 新しい Kusto サーバー グループを追加します|
|グループ名の変更| 既存の Kusto サーバー グループの名前を変更します|
|グループの削除| 既存の Kusto サーバー グループの名前を削除します|
||---------*クラスター*---------|
|Import Connections\(接続のインポート\)| 接続を指定するファイルから接続をインポートします|
|Export Connections\(接続のエクスポート\)| ファイルに接続をエクスポートします|
|接続の追加| 新しい Kusto サーバー接続を追加します| 
|接続の編集| Kusto サーバーの接続プロパティを編集するためのダイアログを開きます|
|接続の削除| Kusto サーバーへの既存の接続を削除します|
|更新| Kusto サーバー接続のプロパティを更新します|
||---------*セキュリティ*---------|
|Inspect Your ADD Principal\(ADD プリンシパルを検査する\)| 現在のアクティブ ユーザーの詳細を表示します|
|Sign-out From AAD\(AAD からサインアウトする\)| 現在のユーザーを AAD への接続からサインアウトします|
||---------*データ スコープ*---------|
|Caching scope\(キャッシュ スコープ\)|<ul><li>ホット DataExecute クエリは、[ホット データ キャッシュ](../management/cachepolicy.md)に対してのみ実行されます</li><li>すべてのデータ:使用可能なすべてのデータに対してクエリを実行します (既定)</li></ul> |
|DateTime Column\(DateTime 列\)| 時間の事前フィルターに使用できる列の名前|
|時間フィルター| 時間の事前フィルターの値|

## <a name="view-tab"></a>[表示] タブ

:::image type="content" source="images/kusto-explorer/view-tab.png" alt-text="Kusto.Explorer の [表示] タブ":::

|メニュー|動作|
|----|----------|
||---------*外観*---------|
|Full View Mode\(全画面表示モード\) | リボン メニューと接続パネルを非表示にして、ワークスペースを最大化します|
|フォントの拡大  | [クエリ] タブおよび結果データ グリッドのフォント サイズを大きくします|  
|フォントの縮小  | [クエリ] タブおよび結果データ グリッドのフォント サイズを小さくします|
|レイアウトのリセット|ツールのドッキング コントロールとウィンドウのレイアウトをリセットします|
|Rename Document Tab\([ドキュメント] タブの名前変更\) |選択したタブの名前を変更します |
||---------*データ ビュー*---------|
|ビューのリセット| [データ ビューの設定](#dvs)を既定値にリセットします |
|Explore Column Values\(列の値を調べる\)|列の値の分布を表示します|
|Focus on Query Statistics\(クエリ統計にフォーカスする\)|クエリの完了時にクエリの結果ではなくクエリの統計にフォーカスを変更します|
|重複データ非表示|クエリ結果からの重複行の削除を切り替えます|
|Hide Empty Columns\(空の列を非表示にする\)|クエリ結果からの空の列の削除を切り替えます|
|Collapse Singular Columns\(特異値の列を折りたたむ\)|特異値がある列の折りたたみを切り替えます|
||---------*データ フィルタリング*---------|
|Filter Rows In Search\(検索で行をフィルター処理する\)|クエリ結果の検索で一致する行のみを表示するオプションを切り替えます (**Ctrl + F**)|
||---------*視覚化*---------|
|視覚化|上の「[視覚化](#visualizations-section)」を参照してください。 |

> [!NOTE]
> <a id="dvs">データ ビューの設定:</a> 
>
> Kusto.Explorer では、使用されている設定が一意の列セットごとに追跡されます。 列が並べ替えられるか削除されると、データ ビューが保存され、同じ列を持つデータが取得されるたびに再利用されます。 表示を既定値にリセットするには、 **[表示]** タブで、 **[表示のリセット]** を選択します。 

## <a name="tools-tab"></a>[ツール] タブ

:::image type="content" source="images/kusto-explorer/tools-tab.png" alt-text="Kusto.Explorer の [ツール] タブ":::

|メニュー|動作|
|----|----------|
||---------*IntelliSense*---------|
|[IntelliSense を有効にする]| スクリプト パネルで IntelliSense を有効または無効にします|
||---------*分析*---------|
|クエリ アナライザー| クエリ アナライザー ツールを起動します|
|Query Checker\(クエリ チェッカー\) | 現在のクエリを分析し、適用可能な改善に関する一連の推奨事項を出力します|
|電卓| 電卓を起動します|
||---------*分析*---------|
|分析レポート| データ分析用にビルド済みの複数のレポートを含むダッシュボードを開きます|
||---------*変換*---------|
|Power BI へのクエリ| クエリを Power BI での使用に適した形式に変換します|
||---------*オプション*---------|
|オプションのリセット| アプリケーション設定を既定値に設定します|
|オプション| アプリケーション設定を構成するためのツールを開きます。 詳細については「[Kusto.Explorer オプション](kusto-explorer-options.md)」をご覧ください。|

## <a name="monitoring-tab"></a>[監視] タブ

:::image type="content" source="images/kusto-explorer/monitoring-tab.png" alt-text="Kusto.Explorer の [監視] タブ":::

|メニュー             | 動作|
|-----------------|---------| 
||---------*監視*---------|
|クラスターの診断 | 接続パネルで現在選択されているサーバー グループの正常性の概要を表示します | 
|最新データ:すべてのテーブル| 現在選択されているデータベースのすべてのテーブルの最新データの概要を表示します|
|最新データ:選択したテーブル|選択したテーブルの最新データをステータス バーに表示します| 

## <a name="management-tab"></a>[管理] タブ

:::image type="content" source="images/kusto-explorer/management-tab.png" alt-text="Kusto.Explorer の [管理] タブ":::

|メニュー             | 動作|
|-----------------|---------|
||---------*承認されたプリンシパル*---------|
|Manage Cluster Authorized Principals\(クラスターの承認されたプリンシパルを管理する\) |承認されたユーザーに対してクラスターのプリンシパルの管理を有効にします| 
|Manage Database Authorized Principals\(データベースの承認されたプリンシパルを管理する\) | 承認されたユーザーに対してデータベースのプリンシパルの管理を有効にします| 
|Manage Table Authorized Principals\(テーブルの承認されたプリンシパルを管理する\) | 承認されたユーザーに対してテーブルのプリンシパルの管理を有効にします| 
|Manage Function Authorized Principals\(関数の承認されたプリンシパルを管理する\) | 承認されたユーザーに対して関数のプリンシパルの管理を有効にします| 

## <a name="help-tab"></a>[ヘルプ] タブ

:::image type="content" source="images/kusto-explorer/help-tab.png" alt-text="Kusto.Explorer の [ヘルプ] タブ":::

|メニュー             | 動作|
|-----------------|---------|
||---------*ドキュメント*---------|
|Help             | Kusto オンライン ドキュメントへのリンクを開きます  | 
|新機能       | すべての Kusto.Explorer の変更を一覧表示するドキュメントを開きます|
|Report Issue (問題の報告)      |次の 2 つのオプションがあるダイアログを開きます <ul><li>サービスに関連する問題を報告する</li><li>クライアント アプリケーションの問題を報告する</li></ul> | 
|Suggest Feature\(機能の提案\)  | Kusto フィードバック フォーラムへのリンクを開きます | 
|更新プログラムの確認     | お使いの Kusto.Explorer のバージョンに更新プログラムがあるかどうかを確認します | 

## <a name="connections-panel"></a>接続パネル

:::image type="content" source="images/kusto-explorer/connections-panel.png" alt-text="Kusto.Explorer の接続パネル":::

[接続] ペインには、構成されているすべてのクラスター接続が表示されます。 クラスターごとに、データベース、テーブル、およびそれらに格納されている属性 (列) が表示されます。 項目を選択します (これによりメイン パネルの検索またはクエリに対して暗黙的なコンテキストが設定されます)。または、項目をダブルクリックして、検索またはクエリ パネルに名前をコピーします。

実際のスキーマが大きい場合 (数百のテーブルが含まれているデータベースなど)、**CTRL+F** キーを押して、検索対象のエンティティ名の部分文字列 (大文字と小文字を区別しない) を入力することで検索できます。

Kusto.Explorer では、クエリ ウィンドウからの接続パネルの制御がサポートされています。これは、スクリプトに便利です。 たとえば、次の構文を使用して、スクリプトによってデータがクエリされているクラスターまたはデータベースに接続するように Kusto.Explorer に指示するコマンドでスクリプト ファイルを開始できます。

<!-- csl -->
```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

`F5` などを使用して各行を実行します。

### <a name="control-the-user-identity-connecting-to-kustoexplorer"></a>Kusto.Explorer に接続するユーザー ID を制御する

新しい接続の既定のセキュリティ モデルは、AAD フェデレーション セキュリティです。 認証は、既定の AAD ユーザー エクスペリエンスを使用して Azure Active Directory によって行われます。

認証パラメーターをより細かく制御する必要がある場合は、[詳細:接続文字列] 編集ボックスを展開し、有効な [Kusto 接続文字列](../api/connection-strings/kusto.md)値を指定します。

たとえば、複数の AAD テナントに存在するユーザーは、特定の AAD テナントに自身の ID の特定の "プロジェクション" を使用する必要がある場合があります。 これを行うには、次のような接続文字列を指定します (大文字の単語は特定の値に置き換えます)。

```kusto
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

* `AAD_TENANT_OF_CLUSTER` は、クラスターがホストされている AAD テナントのドメイン名または AAD テナント ID (GUID) です。 これは、通常、`contoso.com` など、クラスターを所有する組織のドメイン名です。 
* USER_DOMAIN は、そのテナントに招待されたユーザーの ID (`user@example.com` など) です。 

>[!NOTE]
> ユーザーのドメイン名は、クラスターをホストしているテナントの名前と同じである必要はありません。

:::image type="content" source="images/kusto-explorer/advanced-connection-string.png" alt-text="Kusto.Explorer の高度な接続文字列":::

## <a name="keyboard-shortcuts"></a>キーボード ショートカット

キーボード ショートカットを使用すると、マウスよりも高速に操作を実行できる場合があります。 詳細については、この [Kusto.Explorer のキーボード ショートカットの一覧](kusto-explorer-shortcuts.md)をご覧ください。

## <a name="table-row-colors"></a>テーブルの行の色

Kusto.Explorer では、結果パネルの各行の重大度または詳細レベルを解釈し、それに応じて色の設定を試みます。 これは、各列の個別の値を一連の既知のパターン ("警告"、"エラー" など) と照合することによって行われます。

出力の配色を変更する、またはこの動作を無効にするには、 **[ツール]** メニューから、 **[オプション]**  >  **[結果ビューアー]**  >  **[Verbosity color scheme]\(配色の詳細\)** を選択します。

:::image type="content" source="images/kusto-explorer/ke-color-scheme.png" alt-text="Kusto.Explorer の配色の変更":::

## <a name="next-steps"></a>次のステップ

Kusto.Explorer の使用の詳細については、以下をご覧ください。

* [Kusto.Explorer の使用](kusto-explorer-using.md)
* [Kusto.Explorer のキーボード ショートカット](kusto-explorer-shortcuts.md)
* [Kusto.Explorer のオプション](kusto-explorer-options.md)
* [Kusto.Explorer のトラブルシューティング](kusto-explorer-troubleshooting.md)

Kusto.Explorer のツールとユーティリティの詳細については、以下をご覧ください。
* [Kusto.Explorer コード アナライザー](kusto-explorer-code-analyzer.md)
* [Kusto.Explorer コード ナビゲーション](kusto-explorer-codenav.md)
* [Kusto.Explorer コードのリファクタリング](kusto-explorer-refactor.md)
* [Kusto クエリ言語 (KQL)](https://docs.microsoft.com/azure/kusto/query/)
