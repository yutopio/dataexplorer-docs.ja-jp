---
title: ストリーミング取り込み用のキャッシュされたスキーマのクリア-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでキャッシュされたデータベーススキーマをクリアするための管理コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/20/2020
ms.openlocfilehash: 23d86e528dfe687b79ce225b16712184a9bfcde4
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784499"
---
# <a name="clear-schema-cache-for-streaming-ingestion"></a>ストリーミング インジェスト用のスキーマ キャッシュのクリア

クラスターノードは、ストリーミングインジェストを介してデータを受信するデータベースのスキーマをキャッシュします。 このプロセスでは、クラスターリソースのパフォーマンスと使用率が最適化されますが、スキーマが変更されたときに反映の遅延が発生する可能性があります。
キャッシュをクリアして、後続のストリーミングインジェスト要求にデータベースまたはテーブルのスキーマの変更が組み込まれることを保証します。
詳細については、「[ストリーミングインジェストとスキーマの変更](streaming-ingestion-schema-changes.md)」を参照してください。

**スキーマキャッシュのクリア**

この `.clear cache streamingingestion schema` コマンドは、すべてのクラスターノードからキャッシュされたスキーマをフラッシュします。

**構文**

`.clear``table` &lt; &gt; テーブル `cache` 名 `streamingingestion``schema`

`.clear` `database` `cache` `streamingingestion` `schema`

**戻り値**

このコマンドは、次の列を含むテーブルを返します。

|Column    |種類    |説明
|---|---|---
|NodeId|`string`|クラスターノードの識別子
|Status|`string`|成功/失敗

**例**

```kusto
.clear database cache streamingingestion schema

.show table T1 cache streamingingestion schema
```

|NodeId|Status|
|---|---|
|Node1|成功
|Node2|失敗

> [!NOTE]
> コマンドが失敗した場合、または返されたテーブル内のいずれかの行に*Status = Failed*が含まれている場合は、コマンドを安全に再試行できます。
