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
ms.openlocfilehash: 5254f2daee767f51111f2ac3d1be07b7f2bb09f4
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617393"
---
# <a name="retention-policy"></a>Retention ポリシー

保持ポリシーは、データがテーブルから自動的に削除されるメカニズムを制御します。
このような削除は、一般に、年齢ベースの関連性があるテーブルに連続して流れるデータに役立ちます。 たとえば、2週間後には無意味になる可能性のある診断イベントを保持するテーブルに対して保持ポリシーを使用できます。

保持ポリシーは、特定のテーブルまたはデータベース全体に対して構成できます (この場合、ポリシーを上書きしないデータベース内のすべてのテーブルに適用されます)。

保持ポリシーの設定は、データを継続的に取り込みするクラスターにとって重要です。これにより、コストを抑えることができます。

"外部" のデータは、削除の対象となります。 Kusto では、削除が発生したかどうかは保証されません (保持ポリシーがトリガーされた場合でも、データが "継続" される可能性があります)。

保持ポリシーは、インジェスト以降にデータの有効期間を制限するために最も一般的に設定されます (以下の「 [Softdeleteperiod](#the-policy-object)」を参照してください)。

> [!NOTE]
> * 削除時間が不正確です。 制限を超える前にデータが削除されないことがシステムによって保証されますが、その時点以降の削除はすぐには行われません。
> * 論理的な削除期間0は、テーブルレベルの保持ポリシーの一部として設定できます (ただし、データベースレベルの保持ポリシーの一部としては設定できません)。
>   * この処理が完了すると、取り込まれたデータがソーステーブルにコミットされず、データを保持する必要がなくなります。
>   * このような構成は、主にデータをテーブルに取り込まれたときに便利です。
>   トランザクション[更新ポリシー](updatepolicy.md)を使用して変換を行い、別のテーブルに出力をリダイレクトします。

## <a name="the-policy-object"></a>ポリシーオブジェクト

保持ポリシーには、次のプロパティが含まれます。

* **ソフト Deleteperiod**:
    * データがクエリに使用可能な状態であることが保証される期間。これは、取り込まれたした時間から測定されます。
    * 既定値は `100 years` です。
    * テーブルまたはデータベースの論理的な削除期間を変更すると、新しい値が既存のデータと新しいデータの両方に適用されます。
* **回復性**:
    * データが削除された後のデータの回復性 (有効/無効)
    * 既定値は `enabled` です
    * に`enabled`設定した場合、データは削除してから14日間回復できます。

## <a name="control-commands"></a>管理コマンド

* を使用[します。 [ポリシーの保持] を表示](../management/retention-policy.md)して、データベースまたはテーブルの現在の保持ポリシーを表示します。
* [Alter policy retention](../management/retention-policy.md)を使用して、データベースまたはテーブルの現在の保持ポリシーを変更します。

## <a name="defaults"></a>デフォルト

既定では、データベースまたはテーブルを作成するときに、保持ポリシーが定義されていません。
一般的なケースでは、データベースが作成され、その後すぐに、既知の要件に従って作成者がその保持ポリシーを設定します。
ポリシーが設定されていないデータベースまたはテーブルの保持ポリシーに対して[show コマンド](../management/retention-policy.md)を`Policy`実行する`null`と、がとして表示されます。

既定の保持ポリシー (前述の既定値を使用) は、次のコマンドを使用して適用できます。

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
```

この結果、次のポリシーオブジェクトがデータベースまたはテーブルに適用されます。

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

クラスターにという名前`MyDatabase`のデータベースがあり、 `MyTable1`テーブル`MyTable2`と`MySpecialTable`

**1. データベース内のすべてのテーブルに対して、論理削除期間を7日に設定し、復旧を無効にするように設定します。**

* *オプション 1 (推奨)*: 論理的な削除期間を7日、回復性を無効にしてデータベースレベルの保持ポリシーを設定し、テーブルレベルのポリシーが設定されていないことを確認します。

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
```

* *オプション 2*: 各テーブルについて、論理的な削除期間が7日、回復性が無効になっているテーブルレベルの保持ポリシーを設定します。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

**2. `MyTable1`テーブルの設定`MyTable2`で、論理的な削除期間が7日、回復性が有効になっ`MySpecialTable`ていること、およびの論理削除期間が14日、回復不可能**であるように設定されている。

* *オプション 1 (推奨)*: データベースレベルの保持ポリシーを設定します。このポリシーには、論理的な削除期間が7日、回復性が有効になっていることを示します。また、の場合は`MySpecialTable`、の論理的な削除期間が14日、回復性が無効になっているテーブルレベルの保持ポリシーを設定します。

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *オプション 2*: テーブルごとに、必要な論理削除期間と回復性を使用してテーブルレベルの保持ポリシーを設定します。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

**3. テーブル`MyTable1`を設定`MyTable2`し、論理削除期間を7日間にして、データ`MySpecialTable`を無期限に保持します**。

* *オプション 1*: データベースレベルの保持ポリシーに7日間の論理的な削除の期間を設定し、テーブルレベルの保持ポリシーをに設定します。これには、の`MySpecialTable`ソフト削除期間として100年 (既定の保持ポリシー) を設定します。

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *オプション 2*: テーブル`MyTable1`の場合`MyTable2`は、必要な論理削除期間を7日間にしてテーブルレベルの保持ポリシーを設定し、の`MySpecialTable`データベースレベルとテーブルレベルのポリシーが設定されていないことを確認します。

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *オプション 3*: テーブル`MyTable1`の場合`MyTable2`は、目的の論理削除期間7日を使用してテーブルレベルの保持ポリシーを設定します。 テーブル`MySpecialTable`については、論理的な削除期間が100年 (既定の保持ポリシー) になっているテーブルレベルの保持ポリシーを設定します。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```
