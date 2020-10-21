---
title: parse_command_line ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの parse_command_line () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 06/28/2020
ms.openlocfilehash: a13e3def33ea7098a48db7ffefa4406097863c2a
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342724"
---
# <a name="parse_command_line"></a>parse_command_line()

Unicode コマンドライン文字列を解析し、コマンドライン引数の動的配列を返します。

## <a name="syntax"></a>構文

`parse_command_line(`*command_line*、*parser_type*`)`

## <a name="arguments"></a>引数

* *command_line*: 解析するコマンドライン。
* *parser_type*: 現在サポートされている唯一の値は `"Windows"` 、 [CommandLineToArgvW](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw)と同じようにコマンドラインを解析するです。

## <a name="returns"></a>戻り値

コマンドライン引数の動的配列。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|結果|
|---|
|["echo", "hello world!"]|