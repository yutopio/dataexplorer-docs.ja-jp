---
title: . マージエクステント-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの [エクステントのマージ] コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 6b633358713b0ff48d14fc9c5ca2a907bad0afab
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060759"
---
# <a name="merge-extents"></a>。エクステントをマージします。

このコマンドは、指定されたテーブル内の Id で示されるエクステントをマージします。 

> [!NOTE]
> データシャードは Kusto では**エクステント**と呼ばれ、すべてのコマンドはシノニムとして "エクステント" または "エクステント" を使用します。
> エクステントの詳細については、「[エクステント (データシャード) の概要](extents-overview.md)」を参照してください。

## <a name="syntax"></a>構文

`.merge``[async | dryrun]` *TableName* `(` *GUID1* [ `,` *GUID2* ...] `)``[with(rebuild=true)]`

マージ操作には、次の2種類があります。
* インデックスを再構築する*マージ*
* データを完全に reingests する*再構築*

次の3つのオプションを使用できます。
* `async`: コマンドを非同期に実行します。 
    * 操作 ID (Guid) が返されます。
    * 操作の状態を監視できます。 コマンドで操作 ID を使用し `.show operations <operation ID>` ます。
* `dryrun`: この操作では、マージする必要があるエクステントだけが一覧表示されますが、実際にマージされることはありません。
* `with(rebuild=true)`: エクステントは再構築され、マージされるのではなく、データ reingested になります。 インデックスが再構築されます。

## <a name="return-output"></a>出力を返す

出力パラメーター |Type |説明
---|---|---
OriginalExtentId |string |ソーステーブル内の元のエクステントの一意識別子 (GUID)。ターゲットエクステントにマージされています。
ResultExtentId |string |ソースエクステントから作成されたエクステントの一意識別子 (GUID)。 失敗した場合: "Failed" または "放棄"。
Duration |TimeSpan |マージ操作を完了するために要した期間。

## <a name="examples"></a>例

### <a name="rebuild-two-specific-extents-in-mytable-asynchronously"></a>内の2つの特定のエクステントを非同期に再構築 `MyTable` します。

```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

### <a name="merge-two-specific-extents-in-mytable-synchronously"></a>内の2つの特定のエクステント `MyTable` を同期的にマージします。

```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

## <a name="notes"></a>ノート

* 一般に、 `.merge` コマンドを手動で実行することはできません。 これらのコマンドは、テーブルおよびデータベースの[マージポリシー](mergepolicy.md)に従って、継続的に、クラスターのバックグラウンドで自動的に実行されます。  
  * 複数のエクステントを1つにマージするための条件の詳細については、「[マージポリシー](mergepolicy.md)」を参照してください。
* `.merge`操作の最終的な状態は `Abandoned` であり、操作 ID を指定して実行すると表示さ `.show operations` れます。 ソースエクステントの一部がソーステーブルに存在しなくなったため、この状態はソースエクステントがマージされていないことを示します。 `Abandoned`状態は次の場合に発生します。
   * ソースエクステントはテーブルの保有期間の一部として削除されています。
   * ソースエクステントが別のテーブルに移動されました。
   * ソーステーブルが完全に削除されたか、名前が変更されました。
