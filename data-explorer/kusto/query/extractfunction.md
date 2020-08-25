---
title: extract ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの extract () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 98b0f30c968279fcc757ab49bfda982612379026
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793765"
---
# <a name="extract"></a>extract()

テキスト文字列から [正規表現](./re2.md) との一致を取得します。 

必要に応じて、抽出された部分文字列を指定された型に変換します。

```kusto
extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"
```

## <a name="syntax"></a>構文

`extract(`*regex* `,`*captureGroup* `,`*テキスト*[ `,` *typelの*場合]`)`

## <a name="arguments"></a>引数

* *regex*: [正規表現](./re2.md)。
* *captureGroup*: `int` 抽出するキャプチャグループを示す正の定数。 0 は一致全体、1 は正規表現の最初のかっこで囲まれた部分と一致した値、2 以上は後続のかっこを示します。
* *text*: `string` 検索対象の。
* *Typelo al*: 省略可能な型リテラル (例: `typeof(long)` )。 指定した場合、抽出された部分文字列はこの型に変換されます。 

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