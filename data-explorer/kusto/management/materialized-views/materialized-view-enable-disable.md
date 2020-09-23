---
title: 具体化されたビューコマンドを有効または無効にする-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーで具体化されたビューコマンドを有効または無効にする方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 1be8f66300b2d605c4004661c68e64f741437dc1
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057204"
---
# <a name="disable--enable-materialized-view"></a>. disable |. 具体化ビューを有効にします。

具体化されたビューは、次のいずれかの方法で無効にすることができます。

* **システムで自動的に無効にする:**  具体化されたビューは、永続的なエラーで具体化に失敗した場合に自動的に無効になります。 このプロセスは、次の場合に発生する可能性があります。 
    * ビュー定義と矛盾するスキーマ変更。  
    * 具体化されたビュークエリが意味的に無効になるソーステーブルに対する変更。 <br>
    詳細については、「具体化した [ビューの作成](materialized-view-create.md)」を参照してください。
* **具体化されたビューを明示的に無効にします。**  具体化されたビューがクラスターの正常性に悪影響を与える場合 (CPU の消費量が多すぎるなど)、次の [コマンド](#syntax) を使用してビューを無効にします。

> [!NOTE]
> * 具体化されたビューが無効になると、具体化は一時停止し、クラスターのリソースを消費しません。 具体化されたビューに対するクエリは、無効になっている場合でも可能ですが、パフォーマンスが低下する可能性があります。 無効な具体化されたビューでのパフォーマンスは、無効になってからソーステーブルに取り込まれたされたレコードの数によって異なります。 
> * 既に無効になっている具体化されたビューを有効にすることができます。 再び有効にすると、具体化されたビューは、中断した時点から具体化を続行し、レコードはスキップされません。 ビューが長時間無効になっている場合は、時間がかかることがあります。

ビューを無効にすることは、ビューがクラスターの正常性に影響を与える可能性がある場合にのみ推奨されます。

## <a name="syntax"></a>構文

`.enable` | `disable``materialized-view` *MaterializedViewName*

## <a name="properties"></a>Properties

|プロパティ|種類|説明
|----------------|-------|---|
|MaterializedViewName|String|具体化されたビューの名前。|

## <a name="example"></a>例

```kusto
.enable materialized-view ViewName

.disable materialized-view ViewName
```