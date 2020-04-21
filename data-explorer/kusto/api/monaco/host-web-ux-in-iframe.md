---
title: Iframe に Web UI を埋め込む - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの iframe に Web UI を埋め込む方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 9c5469a577c3e09cd433ec250f9215e5f450d5f5
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81524465"
---
# <a name="embed-web-ui-in-an-iframe"></a>Iframe に Web UI を埋め込む

Azure データ エクスプローラー Web UI は iframe に埋め込まれ、サード パーティの Web サイトでホストできます。

![alt text](../images/web-ux.jpg "Azure データ エクスプローラー Web UI")

Web サイトに Azure データ エクスプローラー Web UX を埋め込むと、ユーザーは次の操作を実行できます。

- クエリの編集 (色付けやインテリセンスなどのすべての言語機能を含む)
- テーブル スキーマを視覚的に探索する
- AAD に対する認証
- クエリの実行
- クエリ実行結果の表示
- 複数のタブを作成する
- クエリをローカルに保存する
- メールでクエリを共有する

すべての機能は、アクセシビリティのためにテストされ、暗いと明るいテーマをサポートしています。

## <a name="use-monaco-kusto-or-embed-the-web-ui"></a>モナコ-クストを使用するか、Web UIを埋め込みますか?

Monaco-Kusto は、完了、色付け、リファクタリング、名前変更、定義に移動するなどの編集エクスペリエンスを提供します。 一方、ニーズに合ったユーザー エクスペリエンスを柔軟に提供できます。

Azure データ エクスプローラー Web UI を埋め込むと、手間のかからず広範な機能が提供されますが、ユーザー エクスペリエンスに関する柔軟性は制限されます。 システムの外観と動作を制限できるクエリ パラメーターの固定セットがあります。

## <a name="how-to-embed-the-web-ui-in-an-iframe"></a>Iframe に Web UI を埋め込む方法

### <a name="host-the-website-in-iframe"></a>iframeでウェブサイトをホストする

次のコードを Web サイトに追加します。

```html
<iframe
  src="https://dataexplorer.azure.com/clusters/<cluster>?ibizaPortal=true"
></iframe>
```

クエリ`ibizaPortal`パラメーターは、認証トークンを取得するためにリダイレクト_しないように_Azure データ エクスプローラー Web UI に指示します。 これは、ホスト Web サイトが組み込み iframe に認証トークンを提供する責任があるため、必要です。

接続`<cluster>`ペイン (の)`example: help.kusto.windows.net`にロードするクラスタのホスト名で置き換えます。 既定では、iframe-Embedded モードでは、ホスト Web サイトが必要なクラスターを認識していると想定されているため、UI からクラスターを追加する方法は提供されません。

### <a name="handle-authentication"></a>認証を処理する

1. 'iframe モード' (`ibizaPortal=true`) に設定すると、Azure データ エクスプローラー Web UI は認証用にリダイレクトを試みません。 Web UI は、ブラウザーがトークンの要求と受信に使用するメッセージ投稿メカニズムを使用します。 ページの読み込み中に、次のメッセージが親ウィンドウにポストされます。

   ```javascript
   window.parent.postMessage(
     {
       signature: "queryExplorer",
       type: "getToken"
     },
     "*"
   );
   window.addEventListener(
     "message",
     event => this.handleIncomingMessage(event),
     false
   );
   ```

1. 次に、次の構造のメッセージをリッスンします。

   ```json
   {
     "type": "postToken",
     "message": "${the actual authentication token}"
   }
   ```

1. 提供されるトークンは[、[AAD 認証エンドポイント]](../../management/access-control/how-to-authenticate-with-aad.md#web-client-javascript-authentication-and-authorization)から取得した[JWT トークン](https://tools.ietf.org/html/rfc7519)である必要があります。

> [!IMPORTANT]
> 有効期限が切れる前にトークンを更新し、同じメカニズムを使用してアプリケーションに更新済みトークンを提供するのは、ホスティング ウィンドウの役割です。 それ以外の場合、トークンの有効期限が切れると、サービス呼び出しは常に失敗します。

### <a name="feature-flags"></a>機能フラグ

ホスト アプリケーションは、ユーザー エクスペリエンスの特定の側面を処理する必要があります。 たとえば、接続ペインを非表示にしたり、他のクラスターへの接続を無効にしたりできます。
このシナリオでは、Web エクスプローラーは機能フラグをサポートします。

url では、機能フラグをクエリ パラメーターとして使用できます。 たとえば、ホスト アプリケーションが他のクラスタの追加を無効にする場合は、https://dataexplorer.azure.com/?ShowConnectionButtons=false

| 設定                 | 説明                                                                                                                                                                                                                                                                                       | Default value |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| メニューを表示します。           | 共有メニュー項目を表示する                                                                                                                                                                                                                                                                          | true          |
| 接続ボタンの表示   | 新しいクラスターを追加する接続の追加ボタンを表示します。                                                                                                                                                                                                                                               | true          |
| 開く新しいウィンドウボタンを表示します。 | 「Web UIで開く」ボタンを表示します。 これにより、適切なクラスタとデータベースをスコープ内にhttps://dataexplorer.azure.com示す新しいブラウザ ウィンドウが開きます                                                                                                                                   | False         |
| ファイルメニューを表示します。            | ファイルメニューを表示する(ダウンロードタブコンテンツETC)                                                                                                                                                                                                                                                     | true          |
| ショート                 | 設定ダイアログから Azure データ エクスプローラーのサービス条件へのリンクを表示します。                                                                                                                                                                                                                | true          |
| ショーペルソナ             | 右上と設定メニューからユーザー名を表示                                                                                                                                                                                                                                        | true          |
| フレーム認証              | true の場合、web explorer は iframe が認証を処理し、メッセージを介してトークンを提供することを期待します。 これは常に iframe シナリオに当てはまります                                                                                                                                              | False         |
| 次の実行後に実行する     | 通常、Web エクスプローラーは onunload イベントに保持されます。 iframeでホストする場合、場合によっては起動しません。 したがって、このフラグは、各クエリの実行後にローカル状態を実行するトリガーになります。 したがって、発生したデータ損失は、実行されたことのないテキストにのみ影響を与え、影響を制限します。 | False         |
| スムージングを表示     | true の場合、データベースを右クリックしたときに 1 クリックのインジェクション エクスペリエンスを表示します。                                                                                                                                                                                                                  | true          |
| リフレッシュコネクション       | true の場合、ページの読み込み時にスキーマを常に更新し、ローカル ストレージに依存することはありません。                                                                                                                                                                                                      | False         |
| ヘッダーを表示します。          | true の場合、ページ ヘッダー (Azure データ エクスプローラーのタイトル、設定、およびその他を含む) が表示されます。                                                                                                                                                                                                 | true          |
| 接続ウィンドウの非表示      | true の場合、左側の接続ペインは表示されません。                                                                                                                                                                                                                                                | False         |
| スキップモナコフォーカスオンイニト   | iframeでホストする際のフォーカス問題を修正                                                                                                                                                                                                                                                          | False         |

### <a name="feature-flag-presets"></a>機能フラグのプリセット

プリセットは、一度に機能フラグの束を設定します。
今日は、単一のプリセットがあります。

`IbizaPortal`- デフォルトから次のフラグを変更します。

```json
ShowOpenNewWindowButton: true,
PersistAfterEachRun: true,
IFrameAuth: true,
ShowPageHeader: false,                                 |
```

> [!WARNING]
> プリセットを使用する場合、その上に追加の機能フラグを追加することはできません。 その柔軟性が必要な場合は、個々の機能フラグを使用する必要があります。
