---
title: parse_user_agent ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの parse_user_agent () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: a7261f6996aecdf10446c990dac54afa4638147e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246301"
---
# <a name="parse_user_agent"></a>parse_user_agent()

ユーザーエージェント文字列を解釈します。この文字列は、ユーザーのブラウザーを識別し、ユーザーがアクセスする web サイトをホストしているサーバーに対して特定のシステムの詳細を提供します。 結果はとして返され [`dynamic`](./scalar-data-types/dynamic.md) ます。 

## <a name="syntax"></a>構文

`parse_user_agent(`*ユーザーエージェント-文字列*、 *検索対象*`)`

## <a name="arguments"></a>引数

* *ユーザーエージェント文字列*: `string` ユーザーエージェント文字列を表す型の式。

* [*検索*対象]: 型または型の式。これは、 `string` `dynamic` 関数がユーザーエージェント文字列 (解析ターゲット) で検索する対象を表します。 使用可能なオプションは、"browser"、"os"、"device" です。 1つの解析ターゲットのみが必要な場合は、パラメーターを渡すことができ `string` ます。
2つまたは3つのが必要な場合は、として渡すことができ `dynamic array` ます。

## <a name="returns"></a>戻り値

`dynamic`要求された解析ターゲットに関する情報を格納している型のオブジェクト。

Browser: Family、MajorVersion、MinorVersion、Patch                 

オペレーティングシステム: Family、MajorVersion、MinorVersion、Patch、Patch Minor             

デバイス: ファミリ、ブランド、モデル

> [!WARNING]
> 関数の実装は、多数の定義済みパターンに対する入力文字列の正規表現チェックに基づいて構築されています。 そのため、予想される時間と CPU 消費量が高くなります。
関数がクエリで使用されている場合は、複数のコンピューターで分散方式で実行されていることを確認してください。
この関数を使用するクエリが頻繁に使用される場合は、 [更新ポリシー](../management/updatepolicy.md)を使用して結果を事前に作成することをお勧めしますが、更新ポリシー内でこの関数を使用すると、インジェストの遅延が増加することを考慮する必要があります。
 
## <a name="example"></a>例

```kusto
print useragent = "Mozilla/5.0 (Windows; U; en-US) AppleWebKit/531.9 (KHTML, like Gecko) AdobeAIR/2.5.1"
| extend x = parse_user_agent(useragent, "browser") 
```

予期される結果は動的なオブジェクトです。

{"Browser": {"Family": "AdobeAIR"、"MajorVersion": "2"、"MinorVersion": "5"、"Patch": "1"}}

```kusto
print useragent = "Mozilla/5.0 (SymbianOS/9.2; U; Series60/3.1 NokiaN81-3/10.0.032 Profile/MIDP-2.0 Configuration/CLDC-1.1 ) AppleWebKit/413 (KHTML, like Gecko) Safari/4"
| extend x = parse_user_agent(useragent, dynamic(["browser","os","device"])) 
```

予期される結果は動的なオブジェクトです。

{"Browser": {"Family": "Nokia OSS Browser"、"MajorVersion": "3"、"MinorVersion": "1"、"Patch": ""}、"オペレーティングシステム": {"Family": "Symbian OS", "MajorVersion": "9"、"MinorVersion": "2"、"Patch": ""、"Patch Minor": "" "}"、"Device": {"Family": "Nokia N81", "ブランド": "Nokia", "モデル": "N81-3"}}