---
title: '**Iframe**への Web UI の埋め込み-Azure データエクスプローラー'
description: この記事では、Azure データエクスプローラーの**iframe**に Web UI を埋め込む方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: c6b9d1d5cb971b5c9c51cf9f9918562f12323a03
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799664"
---
# <a name="embed-web-ui-in-an-iframe"></a>Iframe への Web UI の埋め込み

Azure データエクスプローラー Web UI は iframe に埋め込んで、サードパーティの web サイトでホストすることができます。

![alt text](../images/web-ux.jpg "Azure データエクスプローラー Web UI")

Azure データエクスプローラー Web UX を web サイトに埋め込むことで、ユーザーは次のことができるようになります。

- クエリの編集 (色付けや intellisense などのすべての言語機能を含む)
- テーブルスキーマを視覚的に調べる
- Azure AD に対して認証する
- クエリの実行
- クエリ実行の結果を表示する
- 複数のタブを作成する
- クエリをローカルに保存する
- クエリを電子メールで共有する

すべての機能がユーザー補助に対してテストされ、画面上の濃いテーマと淡色表示のテーマをサポートします。

## <a name="use-monaco-kusto-or-embed-the-web-ui"></a>モナコ-Kusto を使用するか、Web UI を埋め込む

モナコ-Kusto を使用すると、補完、色付け、リファクタリング、名前の変更、および定義への変更を使用した編集エクスペリエンスが向上します。 認証、クエリ実行、結果表示、およびスキーマ探索のためのソリューションを構築できます。 モナコ-Kusto を使用すると、ニーズに合ったユーザーエクスペリエンスを柔軟に示すことができます。

Azure データエクスプローラー Web UI を埋め込むことで、ほとんどの作業を行うことなく広範な機能が提供されます。 ただし、埋め込みによって、ユーザーエクスペリエンスの柔軟性も制限されます。 システムの外観と動作を限定的に制御できるようにする、固定されたクエリパラメーターのセットがあります。

## <a name="how-to-embed-the-web-ui-in-an-iframe"></a>Iframe に Web UI を埋め込む方法

### <a name="host-the-website-in-an-iframe"></a>Iframe で web サイトをホストする

Web サイトに次のコードを追加します。

```html
<iframe
  src="https://dataexplorer.azure.com/clusters/<cluster>?ibizaPortal=true"
></iframe>
```

Query `ibizaPortal`パラメーターは、認証トークンを取得するためにリダイレクトし*ない*ように Azure データエクスプローラー Web UI に指示します。 この操作が必要なのは、ホスティング web サイトが埋め込み iframe に認証トークンを提供する役割を果たすためです。

を`<cluster>` 、接続ウィンドウに読み込むクラスターのホスト名に置き換えます (例: `help.kusto.windows.net`)。 既定では、iframe-embedded モードでは、ホストする web サイトが必要なクラスターを認識していることが前提となっているため、UI からクラスターを追加する方法は提供されません。

### <a name="handle-authentication"></a>認証を処理する

1. ' Iframe mode ' (`ibizaPortal=true`) に設定すると、Azure データエクスプローラー Web UI は認証のためにリダイレクトを試行しません。 Web UI は、ブラウザーが使用するメッセージの送信機構を使用して、トークンを要求および受信します。 ページの読み込み中に、次のメッセージが親ウィンドウにポストされます。

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

1. 指定されたトークンは、 [[AAD 認証エンドポイント]](../../management/access-control/how-to-authenticate-with-aad.md#web-client-javascript-authentication-and-authorization)から取得された[JWT トークン](https://tools.ietf.org/html/rfc7519)である必要があります。

> [!IMPORTANT]
> ホストウィンドウは、有効期限が切れる前にトークンを更新し、同じメカニズムを使用して更新されたトークンをアプリケーションに提供する必要があります。 それ以外の場合、トークンの有効期限が切れると、サービスの呼び出しは失敗します。

### <a name="feature-flags"></a>機能フラグ

ホストアプリケーションでは、接続ウィンドウを非表示にしたり、他のクラスターへの接続を無効にしたりするなど、ユーザーエクスペリエンスの特定の側面を制御することが必要になる場合があります。
このシナリオでは、web エクスプローラーは機能フラグをサポートしています。

機能フラグは、url のクエリパラメーターとして使用できます。 たとえば、ホストアプリケーションで他のクラスターの追加を無効にする必要がある場合は、次のようにします。https://dataexplorer.azure.com/?ShowConnectionButtons=false

| 設定                 | 説明                    | Default value |
| ----------------------- | ------------------------------ | ------------- |
| Show[メニュー]           | [共有] メニュー項目を表示する       | true          |
| ShowConnectionButtons   | 新しいクラスターを追加する [**接続の追加**] ボタンを表示する                                                                                                              | true          |
| ShowOpenNewWindowButton | [ **Web で開く**] ボタンをクリックして、新しいブラウザーウィンドウを開きます。 ウィンドウは、適切https://dataexplorer.azure.comなクラスターとスコープ内のデータベースをポイントします。                                                                                                                        | false         |
| ShowFileMenu            | [ファイル] メニューを表示する (**ダウンロード**、**タブ**、**コンテンツ**など)                                                                                                      | true          |
| ShowToS                 | [設定] ダイアログから**Azure データエクスプローラーのサービス利用規約へのリンク**を表示する                                                                                  | true          |
| ShowPersona             | 右上隅にある [設定] メニューからユーザー名を表示します。                                                                                                      | true          |
| Iフレーム認証              | True の場合、web エクスプローラーは iframe が認証を処理し、メッセージを介してトークンを提供することを想定します。 このプロセスは、iframe シナリオでは常に true になります。      | false         |
| 各実行     | 通常、web エクスプローラーは unload イベントに保持されます (iframe でホストする場合、常に起動するわけではありません)。 このフラグによって、各クエリの実行後に**ローカルの永続化状態**がトリガーされます。 データの損失が発生しても、実行されていないテキストにのみ影響を与え、その影響を制限します。 | false         |
| ShowSmoothIngestion     | True の場合、データベースを右クリックすると、1クリックでの取り込みエクスペリエンスが表示されます。                                                                                        | true          |
| RefreshConnection       | True の場合、ページの読み込み時には常にスキーマが更新され、ローカルストレージに依存することはありません。                                                                          | false         |
| ShowPageHeader          | True の場合、ページヘッダー (Azure データエクスプローラーのタイトルと設定を含む) が表示されます。                                                                              | true          |
| HideConnectionPane      | True の場合、左側の [接続] ウィンドウは表示されません。                                                                                                                      | false         |
| SkipMonacoFocusOnInit   | Iframe でホストするときのフォーカスの問題を修正します                                                                                                                            | false         |

### <a name="feature-flag-presets"></a>機能フラグプリセット

プリセットは、一連の機能フラグを一度に設定します。
現時点では、プリセットが1つだけ存在します。

`IbizaPortal`-次のフラグを既定値から変更します。

```json
ShowOpenNewWindowButton: true,
PersistAfterEachRun: true,
IFrameAuth: true,
ShowPageHeader: false,                                 |
```

> [!WARNING]
> プリセットを使用する場合、その上に機能フラグを追加することはできません。 柔軟性が必要な場合は、個々の機能フラグを使用する必要があります。
