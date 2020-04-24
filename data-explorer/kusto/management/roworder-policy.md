---
title: 行順序ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの行順序ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: cda4c9a6017071878832fab376a0376d250f3ed6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520249"
---
# <a name="roworder-policy"></a>行順序ポリシー

この資料では、[行順序ポリシー](../management/roworderpolicy.md)の作成および変更に使用する制御コマンドについて説明します。

## <a name="show-roworder-policy"></a>行順序ポリシーの表示

```kusto
.show table <table_name> policy roworder

.show table * policy roworder
```

## <a name="delete-roworder-policy"></a>行順序の削除ポリシー

```kusto
.delete table <table_name> policy roworder
```

## <a name="alter-roworder-policy"></a>行順序の変更ポリシー

```kusto
.alter table <table_name> policy roworder (<row_order_policy>)

.alter tables (<table_name> [, ...]) policy roworder (<row_order_policy>)

.alter-merge table <table_name> policy roworder (<row_order_policy>)
```

**使用例**

次の例では、`TenantId`行の順序ポリシーを、列 (昇順) を主キーとして、`Timestamp`列 (昇順) をセカンダリ キーとして設定します。次に、ポリシーを照会します。

```kusto
.alter table events policy roworder (TenantId asc, Timestamp desc)

.alter tables (events1, events2, events3) policy roworder (TenantId asc, Timestamp desc)

.show table events policy roworder 
```

|TableName|行順序ポリシー| 
|---|---|
|events|(テナントID asc、タイムスタンプdesc)| 