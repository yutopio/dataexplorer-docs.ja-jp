---
title: extract() - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の extract() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 483c926d60abef120de2a355a6fa040b9608cd7a
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513048"
---
# <a name="extract"></a>extract()

テキスト文字列から [正規表現](./re2.md) との一致を取得します。 

必要に応じて、抽出された部分文字列を、指定した型に変換できます。

```kusto
extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"
```

## <a name="syntax"></a>構文

`extract(`*regex*`,` *captureGroup*`,` *text* [`,` *typeLiteral*]`)`

## <a name="arguments"></a>引数

* *regex*: [正規表現](./re2.md)。
* *captureGroup*: 抽出するキャプチャ グループを示す、正の `int` 定数。 0 は一致全体、1 は正規表現の最初のかっこで囲まれた部分と一致した値、2 以上は後続のかっこを示します。
* *text*:検索する `string`。
* *typeLiteral*: 省略可能な type リテラル (例： `typeof(long)`)。 指定した場合、抽出された部分文字列はこの型に変換されます。 

## <a name="returns"></a>戻り値

*regex* が *text* 内で一致を見つけた場合: 指定されたキャプチャ グループ *captureGroup* に対応する部分文字列。オプションで、*typeLiteral* に変換できます。

一致がないか、型変換が失敗した場合: `null`。 

## <a name="examples"></a>例

サンプル文字列 `Trace` で `Duration` の定義を検索します。 一致は `real` に変換された後、時間定数 (`1s`) で乗算されて、`Duration` は `timespan` 型になります。 この例では、123.45 秒と等しくなります。

```kusto
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

次の例は、 `substring(Text, 2, 4)`に相当します。

```kusto
extract("^.{2,2}(.{4,4})", 1, Text)
```