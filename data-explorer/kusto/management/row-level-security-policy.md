---
title: RowLevelSecurity policy (プレビュー)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの RowLevelSecurity policy (プレビュー) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: f8f6f820090bde91b9ed6479e0677a893a682983
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617376"
---
# <a name="rowlevelsecurity-policy-preview"></a>RowLevelSecurity ポリシー (プレビュー)

この記事では、データベーステーブルの[row_level_security ポリシー](rowlevelsecuritypolicy.md)を構成するために使用されるコマンドについて説明します。

## <a name="displaying-the-policy"></a>ポリシーを表示しています

ポリシーを表示するには、次のコマンドを使用します。

```kusto
.show table <table_name> policy row_level_security
```

## <a name="configuring-the-policy"></a>ポリシーの構成

テーブルの row_level_security ポリシーを有効にします。

```kusto
.alter table <table_name> policy row_level_security enable "<query>"
```

テーブルの row_level_security ポリシーを無効にします。

```kusto
.alter table <table_name> policy row_level_security disable "<query>"
```

ポリシーが無効になっている場合でも、強制的に1つのクエリに適用することができます。 クエリの前に次の行を入力します。

`set query_force_row_level_security;`

これは、row_level_security に対してさまざまなクエリを実行するが、実際にユーザーに対して有効にする必要がない場合に便利です。 クエリが確実に完了したら、ポリシーを有効にします。

> [!NOTE]
> には、 `query`次の制限が適用されます。
>
> * このクエリでは、ポリシーが定義されているテーブルとまったく同じスキーマが生成されます。 つまり、クエリの結果は、元のテーブルと同じ順序で同じ名前と型の列を返す必要があります。
> * クエリで使用できる演算子は`extend`、 `where` `project` `project-away` `project-rename`、、、、、 `project-reorder`および`union`だけです。
> * このクエリでは、ポリシーが定義されているテーブル以外のテーブルを参照することはできません。
> * クエリには、次のいずれか、またはその組み合わせを使用できます。
>    * クエリ (たとえば、 `<table_name> | extend CreditCardNumber = "****"`)
>    * 関数 (など`AnonymizeSensitiveData`)
>    * Datatable (例、 `datatable(Col1:datetime, Col2:string) [...]`)

> [!TIP]
> これらの関数は、多くの場合、row_level_security クエリに役立ちます。
> * [current_principal()](../query/current-principalfunction.md)
> * [current_principal_details()](../query/current-principal-detailsfunction.md)
> * [current_principal_is_member_of()](../query/current-principal-ismemberoffunction.md)

**例**

```kusto
.create-or-alter function with () TrimCreditCardNumbers() {
    let UserCanSeeFullNumbers = current_principal_is_member_of('aadgroup=super_group@domain.com');
    let AllData = Customers | where UserCanSeeFullNumbers;
    let PartialData = Customers | where not(UserCanSeeFullNumbers) | extend CreditCardNumber = "****";
    union AllData, PartialData
}

.alter table Customers policy row_level_security enable "TrimCreditCardNumbers"
```

**パフォーマンス**に関する`UserCanSeeFullNumbers`注意: が最初に評価され`AllData` 、 `PartialData`またはのいずれかが評価されますが、両方ではなく、期待される結果になります。
RLS のパフォーマンスへの影響の詳細については、[こちら](rowlevelsecuritypolicy.md#performance-impact-on-queries)を参照してください。

## <a name="deleting-the-policy"></a>ポリシーを削除しています

```kusto
.delete table <table_name> policy row_level_security
```
