---
title: 行レベルのセキュリティ (プレビュー) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの行レベル セキュリティ (プレビュー) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: 548b0e9e40cfd709b40e548fe7e0a8ad42aa4abc
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520283"
---
# <a name="row-level-security-preview"></a>行レベルのセキュリティ (プレビュー)

グループ メンバーシップまたは実行コンテキストを使用して、データベース テーブル内の行へのアクセスを制御します。

行レベル セキュリティ (RLS) では、データ行アクセスに制限を適用できるため、アプリケーションのセキュリティの設計とコーディングが簡略化されます。 たとえば、部門に関連する行へのユーザー アクセスを制限したり、顧客のアクセスを自社に関連するデータのみに制限したりできます。

アクセス制限ロジックは、別のアプリケーション層のデータから離れるのではなく、データベース層に配置されます。 データベース システムは、データ アクセスが任意の層から試行されるたびにアクセス制限を適用します。 これによりセキュリティ システムの外部からのアクセスが減り、そのシステムの信頼性と堅牢性が向上します。

RLS では、テーブルの特定の部分に対してのみ、他のアプリケーションやユーザーにアクセスを提供できます。 たとえば、次の場合です。

* 一部の条件を満たす行にのみアクセスを許可する
* 一部の列のデータを匿名化する
* 上記の両方

> [!NOTE]
> テーブルで行レベル セキュリティ ポリシーを有効にすることはできません。
> * [連続データ エクスポート](../management/data-export/continuous-data-export.md)が構成されている対象。
> * これは、更新[ポリシー](./updatepolicy.md)のクエリによって参照されます。
> * [制限付きビュー アクセス ポリシー](./restrictedviewaccesspolicy.md)が構成されている。

行レベルセキュリティ ポリシーを管理するための制御コマンドの詳細については、 ここ を[参照してください](../management/row-level-security-policy.md)。

## <a name="examples"></a>例

という名前`Sales`のテーブルには、各行に販売に関する詳細が含まれています。 列の 1 つに、営業担当者の名前が表示されます。
営業担当者に ですべてのレコードへのアクセス権を`Sales`与える代わりに、このテーブルの行レベル セキュリティ ポリシーを有効にして、営業担当者が現在のユーザーであるレコードのみを返すようにできます。

```kusto
Sales | where SalesPersonAadUser == current_principal()
```

クレジットカード番号をマスクすることもできます。

```kusto
Sales | where SalesPersonAadUser == current_principal() | extend CreditCardNumber = "****"
```

すべての営業担当者に特定の国のすべての売上を表示する場合は、次のようなクエリを定義できます。

```kusto
let UserToCountryMapping = datatable(User:string, Country:string)
[
  "john@domain.com", "USA",
  "anna@domain.com", "France"
];
Sales
| where Country in (UserToCountryMapping | where User == current_principal_details()["UserPrincipalName"] | project Country)
```

営業担当者のマネージャを含む AAD グループがある場合は、すべての行にアクセスできます。 これは、行レベル セキュリティ ポリシーで次のクエリによって実現できます。

```kusto
let IsManager = current_principal_is_member_of('aadgroup=sales_managers@domain.com');
let AllData = Sales | where IsManager;
let PartialData = Sales | where not(IsManager) and (SalesPersonAadUser == current_principal());
union AllData, PartialData
| extend CreditCardNumber = "****"
```

一般に、複数の AAD グループがあり、各グループのメンバーに異なるデータのサブセットを表示する場合は、RLS クエリに対してこの構造に従うことができます (ユーザーが属できるのは 1 つの AAD グループにしか含まれることができないと仮定します)。

```kusto
let IsInGroup1 = current_principal_is_member_of('aadgroup=group1@domain.com');
let IsInGroup2 = current_principal_is_member_of('aadgroup=group2@domain.com');
let IsInGroup3 = current_principal_is_member_of('aadgroup=group3@domain.com');
let DataForGroup1 = Customers | where IsInGroup1 and <filtering specific for group1>;
let DataForGroup2 = Customers | where IsInGroup2 and <filtering specific for group2>;
let DataForGroup3 = Customers | where IsInGroup3 and <filtering specific for group3>;
union DataForGroup1, DataForGroup2, DataForGroup3
```

## <a name="more-use-cases"></a>その他のユースケース

* コールセンターサポート担当者は、ソーシャル セキュリティ番号またはクレジット カード番号の数桁で発信者を識別できます。 これらの番号は、サポート担当者に完全に公開しないでください。 RLS ポリシーをテーブルに適用すると、クエリの結果セット内の社会保障番号またはクレジット カード番号の最後の 4 桁を除くすべての数字をマスクできます。
* 個人を特定できる情報 (PII) を隠す RLS ポリシーを設定し、開発者がコンプライアンス規制に違反することなくトラブルシューティングを目的として運用環境にクエリを実行できるようにします。
* 病院は、看護師が患者のみのデータ行を表示することを許可するRLSポリシーを設定できます。
* 銀行は、従業員の事業部または役割に基づいて財務データ行へのアクセスを制限する RLS ポリシーを設定できます。
* マルチテナント アプリケーションでは、多くのテナントのデータを 1 つのテーブルセットに格納できます (これは非常に効率的です)。 RLS ポリシーを使用して、各テナントのデータ行を他のすべてのテナント行から論理的に分離し、各テナントはデータ行のみを表示できるようにします。

## <a name="performance-impact-on-queries"></a>クエリに対するパフォーマンスへの影響

RLS ポリシーがテーブルで有効になっている場合、そのテーブルにアクセスするクエリにパフォーマンス上の影響が生じます。 テーブルへのアクセスは、実際には、そのテーブルに定義されている RLS クエリに置き換えられます。 RLS クエリのパフォーマンスへの影響は、通常、次の 2 つの部分で構成されます。

* Azure アクティブ ディレクトリでのメンバーシップチェック
* データに適用されるフィルター

次に例を示します。

```kusto
let IsRestrictedUser = current_principal_is_member_of('aadgroup=some_group@domain.com');
let AllData = MyTable | where not(IsRestrictedUser);
let PartialData = MyTable | where IsRestrictedUser and (...);
union AllData, PartialData
```

ユーザーがsome_group@domain.comの一部でない場合`IsRestrictedUser`は、 に`false`評価されるため、評価されるクエリは次のようになります。

```kusto
let AllData = MyTable;           // the condition evaluates to `true`, so the filter is dropped
let PartialData = <empty table>; // the condition evaluates to `false`, so the whole expression is replaced with an empty table
union AllData, PartialData       // this will just return AllData, as PartialData is empty
```

同様に、`IsRestrictedUser`が`true`評価されると、 の`PartialData`クエリのみが評価されます。

## <a name="performance-impact-on-ingestion"></a>インジェスに対するパフォーマンスへの影響

なし。