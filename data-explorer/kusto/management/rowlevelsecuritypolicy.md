---
title: 行レベルセキュリティ (プレビュー)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの行レベルセキュリティ (プレビュー) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: 2d535e47f5b05c1c45ef2cf1993681aa8aa2133d
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863304"
---
# <a name="row-level-security-preview"></a>行レベルセキュリティ (プレビュー)

グループメンバーシップまたは実行コンテキストを使用して、データベーステーブル内の行へのアクセスを制御します。

行レベルセキュリティ (RLS) を使用すると、データ行へのアクセスに制限を適用できるため、アプリケーションのセキュリティの設計とコーディングが簡単になります。 たとえば、部門に関連する行へのユーザーのアクセスを制限したり、顧客のアクセスを会社に関連するデータのみに制限したりすることができます。

アクセス制限のロジックは、別のアプリケーション層のデータから離れた場所ではなく、データベース層に配置されます。 データベースシステムは、任意のレベルからデータアクセスが試行されるたびにアクセス制限を適用します。 これによりセキュリティ システムの外部からのアクセスが減り、そのシステムの信頼性と堅牢性が向上します。

RLS を使用すると、テーブルの特定の部分にのみ、他のアプリケーションやユーザーへのアクセスを提供できます。 たとえば、次の場合です。

* 条件を満たす行にのみアクセス権を付与する
* 一部の列のデータを匿名化
* 上記の両方

詳細については、「[行レベルセキュリティポリシーを管理するための制御コマンド](../management/row-level-security-policy.md)」を参照してください。

> [!Note]
> 運用データベースで構成する RLS ポリシーも、フォロワーデータベースで有効になります。 運用データベースとフォロワーデータベースで異なる RLS ポリシーを構成することはできません。

## <a name="limitations"></a>制限事項

行レベルセキュリティポリシーを構成できるテーブルの数に制限はありません。

テーブルで RLS ポリシーを有効にすることはできません。
* [連続データのエクスポート](../management/data-export/continuous-data-export.md)が構成されている。
* これは、[更新ポリシー](./updatepolicy.md)のクエリによって参照されます。
* [制限付きビューアクセスポリシー](./restrictedviewaccesspolicy.md)が構成されている。

## <a name="examples"></a>例

### <a name="limiting-access-to-sales-table"></a>Sales テーブルへのアクセスの制限

という名前のテーブルでは、各行に `Sales` 売上に関する詳細が含まれています。 列の1つに、営業担当者の名前が含まれています。 販売員にのすべてのレコードへのアクセス権を付与するのではなく `Sales` 、このテーブルの行レベルセキュリティポリシーを有効にして、営業担当者が現在のユーザーであるレコードのみを返すことができます。

```kusto
Sales | where SalesPersonAadUser == current_principal()
```

クレジットカード番号をマスクすることもできます。

```kusto
Sales | where SalesPersonAadUser == current_principal() | extend CreditCardNumber = "****"
```

すべての販売員が特定の国の売上をすべて確認できるようにするには、次のようなクエリを定義します。

```kusto
let UserToCountryMapping = datatable(User:string, Country:string)
[
  "john@domain.com", "USA",
  "anna@domain.com", "France"
];
Sales
| where Country in (UserToCountryMapping | where User == current_principal_details()["UserPrincipalName"] | project Country)
```

営業担当者の上司が含まれている AAD グループがある場合は、すべての行にアクセスできるようにすることができます。 これは、行レベルセキュリティポリシーで次のクエリを実行することで実現できます。

```kusto
let IsManager = current_principal_is_member_of('aadgroup=sales_managers@domain.com');
let AllData = Sales | where IsManager;
let PartialData = Sales | where not(IsManager) and (SalesPersonAadUser == current_principal());
union AllData, PartialData
| extend CreditCardNumber = "****"
```

### <a name="exposing-different-data-to-members-of-different-aad-groups"></a>異なる AAD グループのメンバーにさまざまなデータを公開する

複数の AAD グループがあり、各グループのメンバーに異なるデータのサブセットが表示されるようにするには、この構造に従って RLS クエリを実行します (ユーザーが属することができるのは1つの AAD グループのみであると仮定します)。

```kusto
let IsInGroup1 = current_principal_is_member_of('aadgroup=group1@domain.com');
let IsInGroup2 = current_principal_is_member_of('aadgroup=group2@domain.com');
let IsInGroup3 = current_principal_is_member_of('aadgroup=group3@domain.com');
let DataForGroup1 = Customers | where IsInGroup1 and <filtering specific for group1>;
let DataForGroup2 = Customers | where IsInGroup2 and <filtering specific for group2>;
let DataForGroup3 = Customers | where IsInGroup3 and <filtering specific for group3>;
union DataForGroup1, DataForGroup2, DataForGroup3
```

### <a name="applying-the-same-rls-function-on-multiple-tables"></a>複数のテーブルに同じ RLS 関数を適用する

まず、テーブル名を文字列パラメーターとして受け取り、演算子を使用してテーブルを参照する関数を定義し `table()` ます。 次に例を示します。

```
.create-or-alter function RLSForCustomersTables(TableName: string) {
    table(TableName)
    | ...
}
```

次に、次のようにして、複数のテーブルで RLS を構成します。

```
.alter table Customers1 policy row_level_security enable "RLSForCustomersTables('Customers1')"
.alter table Customers2 policy row_level_security enable "RLSForCustomersTables('Customers2')"
.alter table Customers3 policy row_level_security enable "RLSForCustomersTables('Customers3')"
```

## <a name="more-use-cases"></a>その他のユースケース

* コールセンターのサポート担当者は、社会保障番号またはクレジットカード番号のいくつかの数字によって呼び出し元を識別できます。 これらの数値は、サポート担当者には完全に公開されません。 RLS ポリシーをテーブルに適用すると、任意のクエリの結果セット内の任意の社会保障番号またはクレジットカード番号の最後の4桁以外のすべての数字をマスクすることができます。
* 個人を特定できる情報 (PII) をマスクする RLS ポリシーを設定します。これにより、開発者は、コンプライアンス規制に違反することなく、運用環境にクエリを実行してトラブルシューティングを実行できます。
* 病院は、看護師が患者のデータ行のみを表示できるようにする RLS ポリシーを設定できます。
* 銀行では、従業員の事業部門またはロールに基づいて、財務データの行へのアクセスを制限する RLS ポリシーを設定できます。
* マルチテナントアプリケーションでは、多数のテナントのデータを1つの tableset (非常に効率的) に格納できます。 RLS ポリシーを使用して、他のすべてのテナントの行から各テナントのデータ行を論理的に分離することにより、各テナントはそのデータ行のみを参照できます。

## <a name="performance-impact-on-queries"></a>クエリのパフォーマンスへの影響

テーブルで RLS ポリシーが有効になっている場合、そのテーブルにアクセスするクエリのパフォーマンスに影響が生じる可能性があります。 テーブルへのアクセスは、実際には、そのテーブルで定義されている RLS クエリに置き換えられます。 RLS クエリのパフォーマンスへの影響は、通常、次の2つの部分で構成されます。

* メンバーシップのチェックイン Azure Active Directory
* データに適用されるフィルター

次に例を示します。

```kusto
let IsRestrictedUser = current_principal_is_member_of('aadgroup=some_group@domain.com');
let AllData = MyTable | where not(IsRestrictedUser);
let PartialData = MyTable | where IsRestrictedUser and (...);
union AllData, PartialData
```

ユーザーがの一部ではない場合、 some_group@domain.com `IsRestrictedUser` はに評価される `false` ため、評価されるクエリは次のようになります。

```kusto
let AllData = MyTable;           // the condition evaluates to `true`, so the filter is dropped
let PartialData = <empty table>; // the condition evaluates to `false`, so the whole expression is replaced with an empty table
union AllData, PartialData       // this will just return AllData, as PartialData is empty
```

同様に、がに評価される場合は、 `IsRestrictedUser` `true` のクエリのみ `PartialData` が評価されます。

### <a name="improve-query-performance-when-rls-is-used"></a>RLS が使用されたときのクエリパフォーマンスの向上

* 高カーディナリティ列 (DeviceID など) にフィルターが適用されている場合は、[パーティション分割ポリシー](./partitioningpolicy.md)または[行順序ポリシー](./roworderpolicy.md)の使用を検討してください。
* 低中のカーディナリティ列にフィルターが適用されている場合は、[行順序ポリシー](./roworderpolicy.md)を使用することを検討してください。

## <a name="performance-impact-on-ingestion"></a>インジェストのパフォーマンスへの影響

インジェストによるパフォーマンスへの影響はありません。
