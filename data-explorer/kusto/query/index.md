---
title: 概要 - Azure Data Explorer
description: この記事では、Azure Data Explorer の概要について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/07/2019
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: d0f71416410812b8160354781111f4a6f46350fd
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359694"
---
# <a name="overview"></a>概要

Kusto クエリは、データを処理して結果を返すための、読み取り専用の要求です。
要求は、構文を読みやすく、作りやすく、自動化しやすくするように設計されたデータフロー モデルを利用してプレーンテキストで述べられます。 クエリでは、データベース、テーブル、列など、SQL に似た階層に編成されたスキーマ エンティティが使用されます。

クエリは、セミコロン (`;`) で区切られた一連のクエリ ステートメントで構成されます。少なくとも 1 つのステートメントが[表形式のステートメント](tabularexpressionstatements.md)になります。これは、表のような列と行からなる網目に配置されたデータを生成するステートメントです。 クエリの表形式ステートメントからクエリの結果が生成されます。

表形式ステートメントの構文には、ある表形式クエリ演算子から別の表形式クエリ演算子への表形式のデータ フローがあり、データ ソース (データベース内のテーブルや、データを生成する演算子など) から始まり、パイプ (`|`) 区切り記号を使用して結合された一連のデータ変換演算子を通過します。

たとえば、次の Kusto クエリにはステートメントが 1 つありますが、それは表形式ステートメントです。 このステートメントは `StormEvents` という名前のテーブルの参照から始まっています (このテーブルをホストするデータベースはここでは接続情報の一部として暗に示されるだけです)。 そのテーブルのデータ (行) が `StartTime` 列の値でフィルター処理され、さらに `State` 列の値でフィルター処理されます。 "最後まで残った" 行の数がクエリにより返されます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count 
```

このクエリを実行するには、[ここをクリック](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAwsuyS/KdS1LzSspVuDlqlEoz0gtSlUILkksKgnJzE1VsLNVSEksSS0BsjWMDAzMdQ0NdQ0MNRUS81KQVNmgKzICKUIxryRVwdZWQcnNxz/I08VRSQFsW3J+aV6JAgAwMx4+hAAAAA==)してください。
この場合、結果は次のようになります。

|Count|
|-----|
|   23|
