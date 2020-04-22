---
title: parse_path() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで parse_path() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2267efb4e47a6d8e45733ad48dd9f7f4019c1fa8
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744668"
---
# <a name="parse_path"></a>parse_path()

ファイル パス`string`を解析し、パス[`dynamic`](./scalar-data-types/dynamic.md)の次の部分を含むオブジェクトを返します。
両方のスラッシュを持つ単純なパスに加えて、スキーマ(例えば「file://..」)、共有パス(例えば、共有パス)、長い\\パス(例えば"?\C:.")、\\代替データストリーム(例えば「file1.exe:file2.exe」)をサポートしています。

**構文**

`parse_path(`*パス*`)`

**引数**

* *path*: ファイル パスを表す文字列。

**戻り値**

上記のようにパス`dynamic`コンポーネントを指定した型のオブジェクト。

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
|C:\temp\ファイル.txt|{"スキーム":"""""""ルートパス":"C:","ディレクトリ\\パス":"C: temp","ディレクトリ名":"temp"、ファイル名":"ファイル.txt"、拡張子":"txt"、"代替データストリーム名"}
|一時\ファイル.txt|{"スキーム":""""""""""""""ディレクトリパス":"一時","ディレクトリ名":"temp,"ファイル名":"ファイル.txt"、拡張子":"txt,"代替データストリーム名":"}
|file://C:/temp/file.txt:some.exe|{"スキーム":"ファイル","ルートパス":"C:"""ディレクトリパス":"C:/temp","ディレクトリ名":"一時","ファイル":"ファイル.txt"、拡張子":""、"、"代替データストリーム名":"some.exe"}
|\\共有\ユーザー\temp\ファイル.txt.gz|{"スキーム":""""""""""""""""ディレクトリ\\\\パス":"共有\\ユーザー\\の一時"、ディレクトリ名":"temp"、ファイル":"ファイル.txt.gz"、拡張子":"gz,"代替データストリーム名":"}
|/usr/lib/temp/ファイル.txt|{"スキーム":""""""""""""""""""ディレクトリパス":"ディレクトリ名":"temp","ファイル":"ファイル.txt"、拡張子":"txt"、"、"代替データストリーム名"}
