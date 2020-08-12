---
title: 行レベルセキュリティ (プレビュー)-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの行レベルセキュリティ (プレビュー) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: a82c4b48358a90460f917f181b73b718f6c5e455
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88148117"
---
# <a name="row-level-security-preview"></a>行レベルセキュリティ (プレビュー)

グループメンバーシップまたは実行コンテキストを使用して、データベーステーブル内の行へのアクセスを制御します。

行レベルセキュリティ (RLS) は、セキュリティの設計とコーディングを簡略化します。 これにより、アプリケーションのデータ行へのアクセスに制限を適用できます。 たとえば、部門に関連する行へのユーザーのアクセスを制限したり、顧客のアクセスを会社に関連するデータのみに制限したりすることができます。

アクセス制限のロジックは、別のアプリケーション層のデータから離れた場所ではなく、データベース層に配置されます。 データベースシステムは、任意のレベルからデータアクセスが試行されるたびにアクセス制限を適用します。 このロジックにより、セキュリティシステムのセキュリティを強化することで、セキュリティシステムの信頼性と堅牢性が向上します。

RLS を使用すると、テーブルの特定の部分にのみ、他のアプリケーションやユーザーへのアクセスを提供できます。 たとえば、次の操作を行います。

* 条件を満たす行にのみアクセス権を付与する
* 一部の列のデータを匿名化
* 上記のすべて

詳細については、「[行レベルセキュリティポリシーを管理するための制御コマンド](../management/row-level-security-policy.md)」を参照してください。

> [!NOTE]
> 運用データベースで構成する RLS ポリシーも、フォロワーデータベースで有効になります。 運用データベースとフォロワーデータベースで異なる RLS ポリシーを構成することはできません。

> [!TIP]
> これらの関数は、多くの場合、row_level_security クエリに役立ちます。
> * [current_principal()](../query/current-principalfunction.md)
> * [current_principal_details()](../query/current-principal-detailsfunction.md)
> * [current_principal_is_member_of()](../query/current-principal-ismemberoffunction.md)

## <a name="limitations"></a>制限事項

行レベルセキュリティポリシーを構成できるテーブルの数に制限はありません。

テーブルで RLS ポリシーを有効にすることはできません。
* [連続データのエクスポート](../management/data-export/continuous-data-export.md)が構成されている。
* [更新ポリシー](./updatepolicy.md)のクエリによって参照されます。
* [制限付きビューアクセスポリシー](./restrictedviewaccesspolicy.md)が構成されている。

## <a name="examples"></a>例

### <a name="limit-access-to-sales-table"></a>Sales テーブルへのアクセスを制限する

という名前のテーブルでは、各行に `Sales` 売上に関する詳細が含まれています。 列の1つに、販売員の名前が含まれています。 販売員にのすべてのレコードへのアクセス権を付与するのではなく `Sales` 、このテーブルの行レベルセキュリティポリシーを有効にして、販売員が現在のユーザーであるレコードのみを返すようにします。

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

マネージャーが含まれているグループがある場合は、すべての行へのアクセス権を付与することができます。 行レベルセキュリティポリシーに対してクエリを実行します。

```kusto
let IsManager = current_principal_is_member_of('aadgroup=sales_managers@domain.com');
let AllData = Sales | where IsManager;
let PartialData = Sales | where not(IsManager) and (SalesPersonAadUser == current_principal());
union AllData, PartialData
| extend CreditCardNumber = "****"
```

### <a name="expose-different-data-to-members-of-different-azure-ad-groups"></a>異なる Azure AD グループのメンバーにさまざまなデータを公開する

複数の Azure AD グループがあり、各グループのメンバーに異なるデータのサブセットが表示されるようにする場合は、この構造を RLS クエリに使用します。 1人のユーザーが所属できるのは1つの Azure AD グループのみであると仮定します。

```kusto
let IsInGroup1 = current_principal_is_member_of('aadgroup=group1@domain.com');
let IsInGroup2 = current_principal_is_member_of('aadgroup=group2@domain.com');
let IsInGroup3 = current_principal_is_member_of('aadgroup=group3@domain.com');
let DataForGroup1 = Customers | where IsInGroup1 and <filtering specific for group1>;
let DataForGroup2 = Customers | where IsInGroup2 and <filtering specific for group2>;
let DataForGroup3 = Customers | where IsInGroup3 and <filtering specific for group3>;
union DataForGroup1, DataForGroup2, DataForGroup3
```

### <a name="apply-the-same-rls-function-on-multiple-tables"></a>複数のテーブルに同じ RLS 関数を適用する

まず、テーブル名を文字列パラメーターとして受け取り、演算子を使用してテーブルを参照する関数を定義し `table()` ます。 

次に例を示します。

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

### <a name="produce-an-error-upon-unauthorized-access"></a>未承認のアクセス時にエラーを生成する

許可されていないテーブルユーザーが空のテーブルを返す代わりにエラーを受信するようにするには、関数を使用し [`assert()`](../query/assert-function.md) ます。 次の例は、RLS 関数でこのエラーを生成する方法を示しています。

```
.create-or-alter function RLSForCustomersTables() {
    MyTable
    | where assert(current_principal_is_member_of('aadgroup=mygroup@mycompany.com') == true, "You don't have access")
}
```

この方法を他の例と組み合わせることができます。 たとえば、異なる AAD グループのユーザーに異なる結果を表示し、他のすべてのユーザーに対してエラーを生成することができます。

## <a name="more-use-cases"></a>その他のユースケース

* コールセンターのサポート担当者は、社会保障番号またはクレジットカード番号のいくつかの数字によって呼び出し元を識別できます。 これらの番号は、サポート担当者に完全に公開しないでください。 RLS ポリシーをテーブルに適用すると、任意のクエリの結果セット内の任意の社会保障番号またはクレジットカード番号の最後の4桁以外のすべての数字をマスクすることができます。
* 個人を特定できる情報 (PII) をマスクする RLS ポリシーを設定します。これにより、開発者は、コンプライアンス規制に違反することなく、運用環境にクエリを実行してトラブルシューティングを行うことができます。
* 病院は、看護師が患者のデータ行のみを表示できるようにする RLS ポリシーを設定できます。
* 銀行では、従業員の事業部門またはロールに基づいて、財務データの行へのアクセスを制限する RLS ポリシーを設定できます。
* マルチテナントアプリケーションでは、多くのテナントのデータを1つの tableset (効率的) に格納できます。 RLS ポリシーを使用して、他のすべてのテナントの行から各テナントのデータ行を論理的に分離することにより、各テナントはそのデータ行のみを参照できます。

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

ユーザーがの一部でない場合 *some_group@domain.com* 、 `IsRestrictedUser` はに評価され `false` ます。 評価されるクエリは次のようになります。

```kusto
let AllData = MyTable;           // the condition evaluates to `true`, so the filter is dropped
let PartialData = <empty table>; // the condition evaluates to `false`, so the whole expression is replaced with an empty table
union AllData, PartialData       // this will just return AllData, as PartialData is empty
```

同様に、がに評価される場合は、 `IsRestrictedUser` `true` のクエリのみ `PartialData` が評価されます。

### <a name="improve-query-performance-when-rls-is-used"></a>RLS が使用されたときのクエリパフォーマンスの向上

* フィルターが高カーディナリティ列 (DeviceID など) に適用されている場合は、[パーティション分割ポリシー](./partitioningpolicy.md)または[行順序ポリシー](./roworderpolicy.md)の使用を検討してください。
* 低中のカーディナリティ列にフィルターが適用されている場合は、[行順序ポリシー](./roworderpolicy.md)を使用することを検討してください。

## <a name="performance-impact-on-ingestion"></a>インジェストのパフォーマンスへの影響

インジェストによるパフォーマンスへの影響はありません。
