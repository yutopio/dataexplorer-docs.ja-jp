---
title: Alias ステートメント-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Alias ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 63c639fb95322c537c5e069aa7e8ef7037371c88
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742021"
---
# <a name="alias-statement"></a>alias ステートメント

::: zone pivot="azuredataexplorer"

別名ステートメントを使用すると、データベースの別名を定義できます。この別名は、後で同じクエリで使用できます。

これは、複数のクラスターを操作しているときに、より少数のクラスターで作業しているかのように表示する場合に便利です。
エイリアスは、次の構文に従って定義する必要があります。 *clustername*と*databasename*は、既存のエンティティと有効なエンティティです。

**構文**

`alias`database [*' DatabaseAliasName '*] `=` cluster ("https://*clustername*: 443"). database ("*databasename*") です。

`alias`database *DatabaseAliasName* `=` cluster ("https://*clustername*: 443"). database ("*databasename*") を実行します。

* *' DatabaseAliasName '* には、既存の名前または新しい名前を指定できます。
* マップされたクラスター uri とマップされたデータベース名は、二重引用符 (") または単一引用符 (') で囲む必要があります。

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

この機能は、ではサポートされていません Azure Monitor

::: zone-end
