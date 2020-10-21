---
title: Alias ステートメント-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Alias ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 822c8eccf50dc30fd3f56f4402c10a9fafb34084
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248289"
---
# <a name="alias-statement"></a>alias ステートメント

::: zone pivot="azuredataexplorer"

別名ステートメントを使用すると、データベースの別名を定義できます。この別名は、後で同じクエリで使用できます。

これは、複数のクラスターを操作しているときに、より少数のクラスターで作業しているかのように表示する場合に便利です。
エイリアスは、次の構文に従って定義する必要があります。 *clustername* と *databasename* は、既存のエンティティと有効なエンティティです。

## <a name="syntax"></a>構文

`alias` database [*' DatabaseAliasName '*] `=` cluster ("https://*clustername*: 443"). database ("*databasename*") です。

`alias` database *DatabaseAliasName* `=` cluster ("https://*clustername*: 443"). database ("*databasename*") を実行します。

* *' DatabaseAliasName '* には、既存の名前または新しい名前を指定できます。
* マップされたクラスター uri とマップされたデータベース名は、二重引用符 (") または単一引用符 (') で囲む必要があります。

## <a name="examples"></a>例

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
