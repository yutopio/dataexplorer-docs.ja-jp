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
ms.openlocfilehash: 348a614c8b3336085a59a113f18f6858f024e8c7
ms.sourcegitcommit: e66c5f4b833b4f6269bb7bfa5695519fcb11d9fa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83630240"
---
# <a name="embed-web-ui-in-an-iframe"></a>Iframe への Web UI の埋め込み

Azure データエクスプローラー Web UI は iframe に埋め込んで、サードパーティの web サイトでホストすることができます。
 
:::image type="content" source="../images/host-web-ux-in-iframe/web-ux.png" alt-text="Azure データエクスプローラー Web UI":::

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

モナコ-Kusto では、入力候補、色付け、リファクタリング、名前の変更、定義の変更などの編集エクスペリエンスが提供されます。 認証、クエリ実行、結果表示、およびスキーマ探索のためのソリューションを構築する必要がありますが、ニーズに合ったユーザーエクスペリエンスを柔軟に示すことができます。

Azure データエクスプローラー Web UI を埋め込むことで、ほとんどの作業を行うことなく広範な機能が提供されますが、ユーザーエクスペリエンスの柔軟性は限られています。 システムの外観と動作を限定的に制御できるようにする、固定されたクエリパラメーターのセットがあります。

## <a name="how-to-embed-the-web-ui-in-an-iframe"></a>Iframe に Web UI を埋め込む方法

### <a name="host-the-website-in-an-iframe"></a>Iframe で web サイトをホストする

Web サイトに次のコードを追加します。

```html
<iframe
  src="https://dataexplorer.azure.com/clusters/<cluster>?ibizaPortal=true"
></iframe>
```

`ibizaPortal`Query パラメーターは、認証トークンを取得するためにリダイレクトし*ない*ように AZURE データエクスプローラー Web UI に指示します。 この操作が必要なのは、ホスティング web サイトが埋め込み iframe に認証トークンを提供する役割を果たすためです。

を、 `<cluster>` 接続ウィンドウに読み込むクラスターのホスト名 (など) に置き換え `help.kusto.windows.net` ます。 既定では、iframe-embedded モードでは、ホストする web サイトが必要なクラスターを認識していることが前提となっているため、UI からクラスターを追加する方法は提供されません。

### <a name="handle-authentication"></a>認証を処理する

1. ' Iframe mode ' () に設定すると、 `ibizaPortal=true` Azure データエクスプローラー WEB UI は認証のためにリダイレクトを試行しません。 Web UI は、ブラウザーが使用するメッセージの送信機構を使用して、トークンを要求および受信します。 ページの読み込み中に、次のメッセージが親ウィンドウにポストされます。

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

ホスティングアプリケーションでは、ユーザーエクスペリエンスの特定の側面を制御することが必要になる場合があります。 たとえば、[接続] ウィンドウを非表示にするか、他のクラスターへの接続を無効にします。
このシナリオでは、web エクスプローラーは機能フラグをサポートしています。

機能フラグは、url のクエリパラメーターとして使用できます。 ホストアプリケーションで他のクラスターの追加を無効にする必要がある場合は、https://dataexplorer.azure.com/?ShowConnectionButtons=false

| 設定                 | 説明                                                                                                        | Default value |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------ | ------------- |
| Show[メニュー]           | [共有] メニュー項目を表示する                                                                                           | true          |
| ShowConnectionButtons   | 新しいクラスターを追加するには、[**接続の追加**] ボタンを表示します                                                            | true          |
| ShowOpenNewWindowButton | [ **Web UI で開く**] ボタンを表示します。このボタンをクリックすると、新しいブラウザーウィンドウが開き、 https://dataexplorer.azure.com 適切なクラスターとデータベースのスコープが表示されます。           | false         |
| ShowFileMenu            | [ファイル] メニューを表示する (**ダウンロード**、**タブ**、**コンテンツ**など)                                                 | true          |
| ShowToS                 | [設定] ダイアログから**Azure データエクスプローラーのサービス利用規約へのリンク**を表示する                             | true          |
| ShowPersona             | 右上隅にある [設定] メニューからユーザー名を表示します。                                                 | true          |
| Iフレーム認証              | True の場合、web エクスプローラーは iframe が認証を処理し、メッセージを介してトークンを提供することを想定します。 このプロセスは、iframe シナリオでは常に true になります。                                                                                                                                      | false         |
| 各実行     | 通常、web エクスプローラーはアンロードイベントで保持されます。 Iframe でホストする場合、常に起動するわけではありません。 このフラグによって、各クエリの実行後に**ローカルの永続化状態**がトリガーされます。 その結果、データ損失が発生しても、実行されたことのないテキストのみが影響を受け、影響が制限されます。          | false         |
| ShowSmoothIngestion     | True の場合、データベースを右クリックすると、1クリックでの取り込みエクスペリエンスが表示されます。                                   | true          |
| RefreshConnection       | True の場合、ページの読み込み時には常にスキーマが更新され、ローカルストレージに依存することはありません。                      | false         |
| ShowPageHeader          | True の場合、Azure データエクスプローラーのタイトルと設定を含むページヘッダーを表示します。                            | true          |
| HideConnectionPane      | True の場合、左側の [接続] ウィンドウは表示されません。                                                                  | false         |
| SkipMonacoFocusOnInit   | Iframe でホストするときのフォーカスの問題を修正します                                                                       | false         |

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
