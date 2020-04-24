---
title: アイテム保持ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのアイテム保持ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 81e08b6e007a6e3c669e7138e1d36ae1e701d442
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520317"
---
# <a name="retention-policy"></a>保持ポリシー

保持ポリシーは、データをテーブルから自動的に削除するメカニズムを制御します。
このような削除は、通常、関連が年齢ベースのテーブルに継続的に流れ込むデータに役立ちます。 たとえば、保持ポリシーは、2 週間後にインターセリングが解除される可能性がある診断イベントを保持するテーブルに使用できます。

保持ポリシーは、特定のテーブルまたはデータベース全体に対して構成できます (この場合、ポリシーを上書きしないデータベース内のすべてのテーブルに適用されます)。

データを継続的に取り込むクラスターでは、保持ポリシーを設定することが重要であり、コストが制限されます。

保持ポリシーの 「外部」のデータは削除の対象となります。 Kusto は、削除が発生した場合には保証しません (保持ポリシーがトリガーされた場合でも、データが 「残る」可能性があります)。

保持ポリシーは、最も一般的に、取り込みからのデータの保存期間を制限するように設定されています (以下の[SoftDeletePeriod](#the-policy-object)を参照)。

> [!NOTE]
> * 削除時間が不正確です。 システムは、制限を超える前にデータが削除されないことを保証しますが、その時点に続いて削除は即時ではありません。
> * ソフト・削除期間 0 は、テーブル・レベルの保持ポリシーの一部として設定できます (ただし、データベース・レベルの保持ポリシーの一部としては設定できません)。
>   * この処理を行うと、取り込まれたデータはソース テーブルにコミットされず、データを保持する必要がなくなります。
>   * このような構成は、主にデータがテーブルに取り込まれるときに役立ちます。
>   トランザクション[更新ポリシー](updatepolicy.md)は、変換して出力を別のテーブルにリダイレクトするために使用されます。

## <a name="the-policy-object"></a>ポリシー オブジェクト

アイテム保持ポリシーには、次のプロパティが含まれます。

* **ソフト削除期間**:
    * データが取得された時点以降に測定された、クエリでデータが使用できることを保証する期間。
    * 既定値は `100 years` です。
    * 表またはデータベースのソフト・削除期間を変更する場合、新しい値は既存のデータと新規データの両方に適用されます。
* **回復可能性**:
    * データが削除された後のデータの回復性 (有効/無効)
    * 既定値は `enabled` です
    * に`enabled`設定すると、削除後 14 日間データが回復可能になります。

## <a name="control-commands"></a>管理コマンド

* show[ポリシーの保持を](../management/retention-policy.md)使用して、データベースまたはテーブルの現在の保持ポリシーを表示します。
* データベースまたはテーブルの現在の保持ポリシーを変更するには[、.alter ポリシーの保持](../management/retention-policy.md)を使用します。

## <a name="defaults"></a>デフォルト

既定では、データベースまたはテーブルを作成するときに、保持ポリシーが定義されていません。
一般的なケースでは、データベースが作成され、その後、すぐに既知の要件に従って、その作成者によって設定された保持ポリシーがあります。
ポリシーが設定されていないデータベースまたはテーブルの保持ポリシーに対して[show コマンド](../management/retention-policy.md)を実行すると、`Policy`が表示`null`されます。

既定の保持ポリシー (上記の既定値) は、次のコマンドを使用して適用できます。

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
```

これらの結果は、次のポリシー オブジェクトがデータベースまたはテーブルに適用されます。

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

クラスターに という名前`MyDatabase`のデータベースが含まれ`MyTable1``MyTable2`、テーブルが含まれ、`MySpecialTable`

**1. データベース内のすべてのテーブルを、7 日間のソフト削除期間と無効な回復可能性を設定**します。

* *オプション 1 (推奨)*: データベース レベルの保持ポリシーを 7 日間の論理的な削除期間で設定し、回復可能性を無効にし、テーブル レベルのポリシーが設定されていないか確認します。

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
```

* *オプション 2*: テーブルごとに、7 日間のソフト削除期間と回復可能性を無効にして、テーブルレベルの保持ポリシーを設定します。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

**2. テーブル`MyTable1`を`MyTable2`設定し、7日間のソフト削除期間と回復可能を有効にし、14日のソフト削除期間と回復可能性を無効にするように設定`MySpecialTable`** する:

* *オプション 1 (推奨)*: データベース レベルの保持ポリシーを 7 日間の論理的な削除期間と回復可能性を有効にして設定し、テーブル レベルの保持ポリシーを 14 日間のソフト`MySpecialTable`削除期間で設定し、回復可能性を無効にします。

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *オプション 2*: テーブルごとに、必要なソフト削除期間と回復可能性を持つテーブルレベルの保持ポリシーを設定します。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

**3. テーブル`MyTable1`を`MyTable2`設定して、7 日間のソフト削除期間を持ち`MySpecialTable`、そのデータを無期限に保持します**。

* *オプション 1*: データベース レベルの保持ポリシーを 7 日間の論理的な削除期間に設定し、テーブル レベルの保持ポリシーを 100 年 (既定の保持ポリシー)`MySpecialTable`のソフト削除期間で設定します。

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *オプション 2*: `MyTable1``MyTable2`テーブルの場合は、テーブルレベルの保持ポリシーを、必要なソフト削除期間 7 日間に設定し、データベースレベルおよびテーブルレベルのポリシー`MySpecialTable`が設定されていないかどうかを確認します。

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *オプション 3*: `MyTable1``MyTable2`テーブル の 場合は、テーブルレベルの保持ポリシーを、必要なソフト削除期間を 7 日間に設定します。 table`MySpecialTable`の場合、ソフト削除期間が 100 年 (デフォルトの保持ポリシー) のテーブルレベルの保持ポリシーを設定します。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```