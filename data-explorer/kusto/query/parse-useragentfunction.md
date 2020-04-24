---
title: parse_user_agent() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの parse_user_agent() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 2ebe85c622dda116366e30ba22e2e495e4cb76ac
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511732"
---
# <a name="parse_user_agent"></a>parse_user_agent()

ユーザーエージェント文字列を解釈し、ユーザーのブラウザを識別し、ユーザーが訪問した Web サイトをホストするサーバーに特定のシステム詳細を提供します。 結果は として[`dynamic`](./scalar-data-types/dynamic.md)返されます。 

**構文**

`parse_user_agent(`*ユーザー エージェント文字列*,*検索対象*`)`

**引数**

* *ユーザー エージェント文字列*: ユーザー エージェント`string`文字列を表す型 の式。

* *検索*: ユーザー エージェント文字列`string` `dynamic`(解析対象) で関数が何を検索するかを表す型または の式。 可能なオプション: "ブラウザ"、"os"、"デバイス"。 単一の解析対象のみが必要な場合は、パラメーターを`string`渡すことができます。
2 つまたは 3 つの必要な場合は`dynamic array`、 として渡すことができます。

**戻り値**

要求された解析ターゲット`dynamic`に関する情報を格納する型のオブジェクト。

ブラウザ: 家族, メジャーバージョン, マイナーバージョン, パッチ                 

オペレーティングシステム: 家族, メジャーバージョン, マイナーバージョン, パッチ, パッチマイナー             

デバイス: 家族, ブランド, モデル

> [!WARNING]
> 関数の実装は、入力文字列の正規表現チェックに基づいて構築され、多数の定義済みパターンに対して行われます。 したがって、予想される時間とCPUの消費が高くなっています。
関数がクエリで使用される場合は、複数のコンピューターで分散して実行されていることを確認します。
この関数を使用するクエリが頻繁に使用される場合は、[更新ポリシー](../management/updatepolicy.md)を使用して結果を事前に作成する必要がありますが、更新ポリシー内でこの関数を使用すると、取り込みの待機時間が長くなることを考慮する必要があります。
 
**例**

```kusto
print useragent = "Mozilla/5.0 (Windows; U; en-US) AppleWebKit/531.9 (KHTML, like Gecko) AdobeAIR/2.5.1"
| extend x = parse_user_agent(useragent, "browser") 
```

期待される結果は動的オブジェクトです:

{ "ブラウザ": { "家族" : "AdobeAIR", "メジャーバージョン": "2", "マイナーバージョン": "5", "パッチ": "1" }

```kusto
print useragent = "Mozilla/5.0 (SymbianOS/9.2; U; Series60/3.1 NokiaN81-3/10.0.032 Profile/MIDP-2.0 Configuration/CLDC-1.1 ) AppleWebKit/413 (KHTML, like Gecko) Safari/4"
| extend x = parse_user_agent(useragent, dynamic(["browser","os","device"])) 
```

期待される結果は動的オブジェクトです:

{ "ブラウザ": { "家族": "ノキアOSSブラウザ", "メジャーバージョン": "3", "マイナーバージョン": "1", "パッチ": "" }, "オペレーティングシステム": { "家族"" " "家族" " """ """ "メジャーバージョン" " """ """ """ "" "" "" "パッチ"" "" "" "" "" "" "" "" "デバイス" " " " "家族" " "ブランド" " "Nokia"