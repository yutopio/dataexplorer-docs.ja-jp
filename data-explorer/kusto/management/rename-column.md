---
title: 列名の変更-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの列名の変更について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 25d0ff106d406c59c24d26542a8dad1e4992e311
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967385"
---
# <a name="rename-column"></a>. 列名の変更

既存のテーブル列の名前を変更します。
複数の列の名前を変更するには、[以下](#rename-columns)を参照してください。

**構文**

`.rename``column`[*DatabaseName* `.` ] *TableName* `.` *columnexistingname* `to` *columnnewname*

*DatabaseName*、 *TableName*、 *Columnexistingname*、および*columnnewname*はそれぞれのエンティティの名前であり、識別子の[名前付け規則](../query/schema-entities/entity-names.md)に従います。

## <a name="rename-columns"></a>列名の変更

同じテーブル内の複数の既存の列の名前を変更します。

**構文**

`.rename``columns` *Col1* `=` [*DatabaseName* `.` [*TableName* `.` *Col2*]] `,` ...

このコマンドを使用すると、2つの列の名前を入れ替えることができます (それぞれの列名は他の名前に変更されます)。