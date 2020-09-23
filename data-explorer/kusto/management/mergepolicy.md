---
title: エクステントのマージポリシー-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのエクステントのマージポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 87980046e6f0ebbbdd17a9037aa1206779d2a61e
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057038"
---
# <a name="merge-policy"></a>Merge ポリシー

マージポリシーでは、Kusto クラスターの [エクステント (データシャード)](../management/extents-overview.md) をマージするかどうか、およびその方法を定義します。

マージ操作には `Merge` 、インデックスを再構築すると、データを `Rebuild` 完全に reingests するという2種類があります。

どちらの種類の操作でも、ソースエクステントを置き換える1つのエクステントが生成されます。

既定では、リビルド操作が優先されます。 再構築の条件に適合しないエクステントがある場合は、それらをマージしようとします。  

> [!NOTE]
> * *異なる*タグを使用してエクステントにタグを付けると `drop-by` 、マージポリシーが設定されている場合でも、そのエクステントはマージされません。 詳細については、「 [エクステントのタグ付け](../management/extents-overview.md#extent-tagging)」を参照してください。
> * タグの共用体が1M 文字の長さを超えるエクステントはマージされません。
> * データベースまたはテーブルの [シャーディングポリシー](./shardingpolicy.md) は、エクステントのマージ方法にも影響を与えます。

## <a name="merge-policy-properties"></a>マージポリシーのプロパティ

マージポリシーには、次のプロパティが含まれています。

* **RowCountUpperBoundForMerge**:
    * 既定値: 
      * 2020年6月より前に設定されたポリシーの場合は 0 (無制限)。
      * 1600万6月2020開始時に設定されたポリシーの場合。
    * マージされたエクステントの最大許容行数。
    * 再構築ではなく、マージ操作に適用されます。  
* **OriginalSizeMBUpperBoundForMerge**:
    * 既定値は 0 (無制限) です。
    * マージされたエクステントの元の最大許容サイズ (Mb 単位)。
    * 再構築ではなく、マージ操作に適用されます。  
* **MaxExtentsToMerge**:
    * 既定値は100です。
    * 1回の操作でマージするエクステントの最大許容数。
    * マージ操作に適用されます。
* **LoopPeriod**:
    * 既定値は 01:00:00 (1 時間) です。
    * データ管理サービスによってマージ操作または再構築操作が連続して開始されるまでの最大待機時間。
    * マージ操作と再構築操作の両方に適用されます。
* **Allowrebuild**:
    * 既定値は ' true ' です。
    * `Rebuild`操作が有効かどうかを定義します (この場合、操作よりも優先され `Merge` ます)。
* **Allowmerge**:
    * 既定値は ' true ' です。
    * 操作を有効にするかどうかを定義し `Merge` ます。この場合、操作より優先されることは `Rebuild` ありません。
* **Maxrangeinhours**:
    * 既定値は8です。
        * 具体化されたビューでは、具体化されたビューの有効な[保持ポリシー](retentionpolicy.md)で復旧が無効になっていない[限り、既定](materialized-views/materialized-view-overview.md)値は14日です。
    * 2つの異なるエクステントの作成時に許容される最大許容差 (時間単位)。ただし、マージできるようになります。
    * タイムスタンプは、エクステントの作成であり、エクステントに含まれる実際のデータには関係ありません。
    * マージ操作と再構築操作の両方に適用されます。
    * この値は、有効な [保持ポリシー](./retentionpolicy.md)の *ソフト deleteperiod*または [キャッシュポリシー](./cachepolicy.md)の *dataホットスパン* の値に従って設定する必要があります。 *Softdeleteperiod*と*dataホットスパン*の価値を低くします。 *Maxrangeinhours*値を2-3% の範囲で設定します。 [例](#maxrangeinhours-examples)を参照してください。

## <a name="maxrangeinhours-examples"></a>MaxRangeInHours の例

|最小 (SoftDeletePeriod (保持ポリシー)、Dataホットスパン (キャッシュポリシー))|最大範囲 (時間) (マージポリシー)|
|--------------------------------------------------------------------|---------------------------------|
|7日 (168 時間)                                                  | 4                               |
|14日 (336 時間)                                                 | 8                               |
|30日 (720 時間)                                                 | 18                              |
|60日 (1440 時間)                                               | 36                              |
|90日 (2160 時間)                                               | 60                              |
|180日 (4320 時間)                                              | 120                             |
|365日 (8760 時間)                                              | 250                             |

> [!WARNING]
> エクステントのマージポリシーを変更する前に、Azure データエクスプローラーチームに問い合わせてください。

データベースを作成すると、上記の既定のマージポリシー値が設定されます。 ポリシーは、テーブルレベルで明示的にオーバーライドされない限り、データベースに作成されたすべてのテーブルによって既定で継承されます。

詳細については、「 [データベースまたはテーブルのマージポリシーを管理できるよう](../management/merge-policy.md)にするための制御コマンド」を参照してください。
