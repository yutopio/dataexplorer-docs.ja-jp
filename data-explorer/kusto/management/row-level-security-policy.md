---
title: RowLevel セキュリティ ポリシー (プレビュー) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの RowLevelSecurity ポリシー (プレビュー) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: bf8ee8bc4c43c4ed3bcd320ce5b4778f33c3cc64
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520300"
---
# <a name="rowlevelsecurity-policy-preview"></a>ポリシー (プレビュー)

この記事では、データベース テーブルのrow_level_security[ポリシー](rowlevelsecuritypolicy.md)を構成するためのコマンドについて説明します。

## <a name="displaying-the-policy"></a>ポリシーの表示

ポリシーを表示するには、次のコマンドを使用します。

```kusto
.show table <table_name> policy row_level_security
```

## <a name="configuring-the-policy"></a>ポリシーの構成

テーブルrow_level_securityポリシーを有効にします。

```kusto
.alter table <table_name> policy row_level_security enable "<query>"
```

テーブルrow_level_securityポリシーを無効にします。

```kusto
.alter table <table_name> policy row_level_security disable "<query>"
```

ポリシーが無効になっている場合でも、1 つのクエリに適用するように強制できます。 クエリの前に次の行を入力します。

`set query_force_row_level_security;`

これは、row_level_securityに対してさまざまなクエリを試すが、実際にはユーザーに影響を与えないようにする場合に便利です。 クエリに自信が持ったら、ポリシーを有効にします。

> [!NOTE]
> には、次の制限が適用`query`されます。
>
> * クエリは、ポリシーが定義されているテーブルとまったく同じスキーマを生成する必要があります。 つまり、クエリの結果は、元のテーブルとまったく同じ列を同じ順序で、同じ名前と型で返す必要があります。
> * クエリ`extend`では、次の演算子、 、 `where`、 `project` `project-away`、 `project-rename` `project-reorder` 、`union`および 、 を使用できます。
> * クエリは、ポリシーが定義されているテーブル以外のテーブルを参照できません。
> * クエリは、次のいずれか、またはそれらの組み合わせのいずれかです。
>    * クエリ (たとえば) `<table_name> | extend CreditCardNumber = "****"`
>    * 機能 (例えば`AnonymizeSensitiveData`、 )
>    * データテーブル (例: `datatable(Col1:datetime, Col2:string) [...]`)

> [!TIP]
> これらの関数は、row_level_securityクエリに役立ちます。
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
**パフォーマンスに関する注意**:`UserCanSeeFullNumbers`最初に評価`AllData`され`PartialData`、次に評価されるか、または両方ではなく、期待される結果になります。
RLS のパフォーマンスへの影響の詳細については、 を[参照してください](rowlevelsecuritypolicy.md#performance-impact-on-queries)。

## <a name="deleting-the-policy"></a>ポリシーの削除

```kusto
.delete table <table_name> policy row_level_security
```