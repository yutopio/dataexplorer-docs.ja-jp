---
title: Kusto 保持ポリシーは、データの削除方法を制御します-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの保持ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 871ad751105ba6a3f6ce5dcba55b3a0fd1c17789
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91056987"
---
# <a name="retention-policy"></a>Retention ポリシー

保持ポリシーは、テーブルまたは具体化された [ビュー](materialized-views/materialized-view-overview.md)から自動的にデータを削除するメカニズムを制御します。 テーブルに継続的にフローするデータを削除し、その関連性が年齢に基づいている場合に便利です。 たとえば、2週間後には無意味になる可能性のある診断イベントを保持するテーブルに対して、ポリシーを使用できます。

保持ポリシーは、特定のテーブルまたは具体化されたビュー、またはデータベース全体に対して構成できます。 その後、上書きしないデータベース内のすべてのテーブルに、ポリシーが適用されます。

保持ポリシーの設定は、データを継続的に取り込みするクラスターにとって重要です。これにより、コストを抑えることができます。

"外部" のデータは、削除の対象となります。 削除が発生した場合、特定の保証はありません。 保持ポリシーがトリガーされた場合でも、データは "待機中" になることがあります。

保持ポリシーは、インジェスト以降にデータの有効期間を制限するために最も一般的に設定されます。 詳細については、「 [Softdeleteperiod](#the-policy-object)」を参照してください。

> [!NOTE]
> * 削除時間が不正確です。 制限を超える前にデータが削除されることはありませんが、削除はすぐには行われません。
> * 論理的な削除期間0は、テーブルレベルの保持ポリシーの一部として設定できますが、データベースレベルの保持ポリシーの一部として設定することはできません。
>   * この処理が完了すると、取り込まれたデータがソーステーブルにコミットされず、データを保持する必要がなくなります。 このため、は `Recoverability` にのみ設定でき `Disabled` ます。
>   * このような構成は、主にデータをテーブルに取り込まれたときに便利です。
> トランザクション [更新ポリシー](updatepolicy.md) を使用して変換を行い、別のテーブルに出力をリダイレクトします。

## <a name="the-policy-object"></a>ポリシーオブジェクト

保持ポリシーには、次のプロパティが含まれます。

* **ソフト Deleteperiod**:
    * データがクエリに使用できることが保証される期間。 期間は、データが取り込まれたされた時点から計測されます。
    * 既定値は `100 years` です。
    * テーブルまたはデータベースの論理的な削除期間を変更すると、新しい値が既存のデータと新しいデータの両方に適用されます。
* **回復性**:
    * データが削除された後のデータの回復性 (有効/無効)。
    * 既定値は `Enabled` です。
    * に設定した場合 `Enabled` 、データは論理的に削除されてから14日間回復できます。

## <a name="control-commands"></a>管理コマンド

* を使用 [します。 [ポリシーの保持] を表示](../management/retention-policy.md) して、データベース、テーブル、または具体化された [ビュー](materialized-views/materialized-view-overview.md)の現在の保持ポリシーを表示します。
* [Alter policy retention](../management/retention-policy.md)を使用して、データベース、テーブル、または具体化された[ビュー](materialized-views/materialized-view-overview.md)の現在の保持ポリシーを変更します。

## <a name="defaults"></a>デフォルト

既定では、データベースまたはテーブルを作成するときに、保持ポリシーが定義されていません。 通常、データベースが作成され、その後すぐに、既知の要件に従って作成者によって設定された保持ポリシーが設定されます。
ポリシーが設定されていないデータベースまたはテーブルの保持ポリシーに対して [show コマンド](../management/retention-policy.md) を実行すると、 `Policy` がとして表示され `null` ます。

既定の保持ポリシーは、上記の既定値を使用して、次のコマンドを使用して適用できます。

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
.alter materialized-view ViewName policy retention "{}"
```

コマンドを実行すると、次のポリシーオブジェクトがデータベースまたはテーブルに適用されます。

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

データベースまたはテーブルの保持ポリシーをクリアするには、次のコマンドを使用します。

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>例

という名前のデータベースがあり、 `MyDatabase` テーブル `MyTable1` 、、およびを含むクラスターの場合 `MyTable2` `MySpecialTable` 。

### <a name="soft-delete-period-of-seven-days-and-recoverability-disabled"></a>論理的な削除の期間は7日間、回復不可能

データベース内のすべてのテーブルに対して、論理的な削除の期間を7日間に設定し、回復性を無効にするように設定します。

* *オプション 1 (推奨)*: データベースレベルの保持ポリシーを設定し、テーブルレベルのポリシーが設定されていないことを確認します。

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge materialized-view ViewName policy retention softdelete = 7d 
```

* *オプション 2*: 各テーブルについて、論理的な削除の期間が7日、回復性が無効になっているテーブルレベルの保持ポリシーを設定します。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

### <a name="soft-delete-period-of-seven-days-and-recoverability-enabled"></a>論理削除期間の7日と回復可能

* テーブルを設定し、 `MyTable1` `MyTable2` 復元可能な状態が7日間の論理的な削除期間を有効にします。
* `MySpecialTable`を設定すると、論理削除期間は14日、回復性は無効になります。

* *オプション 1 (推奨)*: データベースレベルの保持ポリシーを設定し、テーブルレベルの保持ポリシーを設定します。

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *オプション 2*: テーブルごとに、関連する論理的な削除期間と回復性を持つテーブルレベルの保持ポリシーを設定します。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

### <a name="soft-delete-period-of-seven-days-and-myspecialtable-keeps-its-data-indefinitely"></a>論理削除期間は7日間で、データは無期限に保持されます。 `MySpecialTable`

テーブルを設定し、 `MyTable1` `MyTable2` 論理削除期間を7日間にして、 `MySpecialTable` データを無期限に保持します。

* *オプション 1*: データベースレベルの保持ポリシーを設定し、テーブルレベルの保持ポリシーを設定します。このポリシーには、の既定の保持ポリシーである100年の論理的な削除期間が設定さ `MySpecialTable` れています。

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *オプション 2*: テーブル `MyTable1` および `MyTable2` では、テーブルレベルの保持ポリシーを設定し、のデータベースレベルとテーブルレベルのポリシーが設定されていないことを確認し `MySpecialTable` ます。

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *オプション 3*: テーブル `MyTable1` および `MyTable2` では、テーブルレベルの保持ポリシーを設定します。 テーブルの場合は `MySpecialTable` 、既定の保持ポリシーである100年の論理的な削除期間を含むテーブルレベルの保持ポリシーを設定します。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```
