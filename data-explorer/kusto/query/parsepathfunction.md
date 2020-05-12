---
title: parse_path ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの parse_path () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 16b80c86f526cb05514577359603e9e21de80064
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224886"
---
# <a name="parse_path"></a>parse_path()

ファイルパスを解析 `string` し、 [`dynamic`](./scalar-data-types/dynamic.md) path の次の部分を含むオブジェクトを返します。 Scheme、Rootpath、DirectoryPath、DirectoryName、FileName、Extension、AlternateDataStreamName。
両方の種類のスラッシュを持つ単純なパスに加えて、スキーマを使用したパス (例: "file://")、共有パス (例: " \\ shareddrive\users...")、長いパス (例 \\ : "? \c:...")、代替データストリーム (例: "file1: file2") をサポートします。

**構文**

`parse_path(`*道*`)`

**引数**

* *パス*: ファイルパスを表す文字列。

**戻り値**

`dynamic`上に示したパスコンポーネントを含む型のオブジェクト。

**例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(p:string) 
[
    @"C:\temp\file.txt",
    @"temp\file.txt",
    "file://C:/temp/file.txt:some.exe",
    @"\\shared\users\temp\file.txt.gz",
    "/usr/lib/temp/file.txt"
]
| extend path_parts = parse_path(p)

```

|p|path_parts
|---|---
|C:\temp\file.txt|{"Scheme": "", "RootPath": "C:", "DirectoryPath": "C: \\ temp", "DirectoryName": "temp", "Filename": "file.txt", "Extension": "txt", "AlternateDataStreamName": ""}
|temp\file.txt|{"Scheme": "", "RootPath": "", "DirectoryPath": "temp", "DirectoryName": "temp", "Filename": "file.txt", "Extension": "txt", "AlternateDataStreamName": ""}
|file://C:/temp/file.txt:some.exe|{"Scheme": "file", "RootPath": "C:", "DirectoryPath": "C:/temp", "DirectoryName": "temp", "Filename": "file.txt", "Extension": "txt", "AlternateDataStreamName": "some .exe"}
|\\shared\users\temp\file.txt.gz|{"Scheme": "", "RootPath": "", "DirectoryPath": " \\ \\shared \\ users \\ temp "," DirectoryName ":" temp "," Filename ":" file.txt "," Extension ":" gz "," AlternateDataStreamName ":" "}
|/usr/lib/temp/file.txt|{"Scheme": "", "RootPath": "", "DirectoryPath": "/usr/lib/temp", "DirectoryName": "temp", "Filename": "file.txt", "Extension": "txt", "AlternateDataStreamName": ""}
