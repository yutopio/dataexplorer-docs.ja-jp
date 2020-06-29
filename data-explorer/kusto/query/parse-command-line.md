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
ms.openlocfilehash: 0296b41dc10092f0b274491c3fab3355fc82a2d9
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2020
ms.locfileid: "85518142"
---
# <a name="parse_command_line"></a>parse_command_line ()

Unicode コマンドライン文字列を解析し、コマンドライン引数の動的配列を返します。

**構文**

`parse_command_line(`*command_line*、*parser_type*`)`

**引数**

* *command_line*: 解析するコマンドライン。
* *parser_type*: 現在サポートされている唯一の値は `"Windows"` 、 [CommandLineToArgvW](https://docs.microsoft.com/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw)と同じようにコマンドラインを解析するです。

**戻り値**

コマンドライン引数の動的配列。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|結果|
|---|
|["echo", "hello world!"]|
