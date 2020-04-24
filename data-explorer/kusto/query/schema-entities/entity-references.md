---
title: エンティティ参照 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのエンティティ参照について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abbf63de632ff2ff5fb721dddc256c5ee62d4966
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509403"
---
# <a name="entity-references"></a>エンティティ参照

名前付き Kusto スキーマ エンティティ (データベース、テーブル、列、および格納された関数ではなく、クラスター) は、名前を使用してクエリ内で参照されます。 エンティティのコンテナーが現在のコンテキストで明確でない場合、エンティティ名は追加の条件なしで使用できます。 たとえば、 というデータベースに対してクエリを実行`DB`する場合、そのデータベースで呼`T`び出されたテーブルを、その名前`T`を使用して参照できます。

一方、エンティティのコンテナがコンテキストから利用できない場合、またはコンテキスト内のコンテナとは異なるコンテナからエンティティを参照する場合は、エンティティの**修飾名**を使用する必要があります。`DB`したがって、データベースに対して実行されるクエリは、`T1`を使用`DB1``database("DB1").T1`して同じクラスターの別のデータベース内のテーブルを参照する場合があり、別のクラスターからテーブルを参照する場合は、 を`cluster("https://C2.kusto.windows.net/").database("DB2").T2`使用して参照できます。

エンティティ参照は、エンティティのコンテナーのコンテキストで一意である限り、エンティティの名前もかなり使用できます。 [エンティティのかなり名前](./entity-names.md#entity-pretty-names)を参照してください。

## <a name="wildcard-matching-for-entity-names"></a>エンティティ名のワイルドカード一致

コンテキストによっては、ワイルドカード ( )`*`を使用してエンティティ名の全体または一部を照合する場合があります。 たとえば、次のクエリは、現在のデータベースのすべてのテーブルと、名前が で始まるデータベース`DB`内のすべてのテーブルを参照`T`します。

```kusto
union *, database("DB1").T*
```

注: ワイルドカードの一致は、ドル記号 ( )`$`で始まるエンティティ名と一致しません。
このような名前はシステム予約です。



