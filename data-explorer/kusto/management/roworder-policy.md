---
title: RowOrder ポリシー-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの RowOrder ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: bf8844a72d2b4fc3d9dec4f24fdfa6ac36ee84d7
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967504"
---
# <a name="roworder-policy-command"></a>roworder policy コマンド

この記事では、[行の順序ポリシー](../management/roworderpolicy.md)を作成および変更するために使用する制御コマンドについて説明します。

## <a name="show-roworder-policy"></a>RowOrder ポリシーの表示

```kusto
.show table <table_name> policy roworder

.show table * policy roworder
```

## <a name="delete-roworder-policy"></a>RowOrder ポリシーの削除

```kusto
.delete table <table_name> policy roworder
```

## <a name="alter-roworder-policy"></a>Alter RowOrder policy

```kusto
.alter table <table_name> policy roworder (<row_order_policy>)

.alter tables (<table_name> [, ...]) policy roworder (<row_order_policy>)

.alter-merge table <table_name> policy roworder (<row_order_policy>)
```

**使用例** 

次の例では、列 (昇順) の行順序ポリシーを主キーとして設定 `TenantId` し、 `Timestamp` 列 (昇順) をセカンダリキーとして設定します。 その後、ポリシーが照会されます。

```kusto
.alter table events policy roworder (TenantId asc, Timestamp desc)

.alter tables (events1, events2, events3) policy roworder (TenantId asc, Timestamp desc)

.show table events policy roworder 
```

|TableName|RowOrderPolicy| 
|---|---|
|events|(TenantId asc、Timestamp desc)|
