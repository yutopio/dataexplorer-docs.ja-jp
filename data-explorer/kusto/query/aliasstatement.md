---
title: エイリアス ステートメント - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのエイリアス ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c6c689ab6daacebe1cd20742b199c8b9cc299245
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766096"
---
# <a name="alias-statement"></a>alias ステートメント

::: zone pivot="azuredataexplorer"

Alias ステートメントを使用すると、同じクエリで後で使用できるデータベースの別名を定義できます。

これは、いくつかのクラスターを操作しているときに、より少ないクラスターで作業している、または 1 つのクラスターでのみ動作するように見せようとする場合に役立ちます。
エイリアスは、*クラスタ名*と*データベース名*が既存の有効なエンティティである必要がある次の構文に従って定義する必要があります。

**構文**

`alias`データベース]*'データベースエイリアス名'*]`=`クラスター ("https://*クラスター名*.kusto.windows.net:443") データベース("*データベース名*")

`alias`*データベース データベースエイリアス名*`=`クラスタ("https://*クラスター名*.kusto.windows.net:443")データベース("*データベース名*")

* *'データベースエイリアス名'* は、軸方向の名前または新しい名前のいずれかです。
* マップされたクラスター URI とマップされたデータベース名は、二重引用符(")または単一引用符(') 内になければなりません。

**使用例**

```kusto
alias database["wiki"] = cluster("https://somecluster.kusto.windows.net:443").database("somedatabase");
database("wiki").PageViews | count 
```

```kusto
alias database Logs = cluster("https://othercluster.kusto.windows.net:443").database("otherdatabase");
database("Logs").Traces | count 
```

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
