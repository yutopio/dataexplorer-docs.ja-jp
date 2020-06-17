---
title: 。データベースを表示します-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのデータベースの表示について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e5ff64db05bb85d88ed3da3347ea95cf02b0234b
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818543"
---
# <a name="show-databases"></a>.show databases

すべてのレコードが、ユーザーがアクセスできるクラスター内のデータベースに対応するテーブルを返します。

コンテキストデータベースのプロパティを示す表については、「」を参照してください [`.show database`](show-database.md) 。

**構文**

`.show` `databases`

**出力スキーマ**

|列名       |列の型|説明                                                                  |
|------------------|-----------|-----------------------------------------------------------------------------|
|DatabaseName      |`string`   |クラスターに接続されているデータベースの名前                    |
|PersistentStorage |`string`   |データベースの永続的なストレージ "ルート"。 内部使用のみ          |
|バージョン           |`string`   |データベースのバージョンです。 内部使用のみ                       |
|IsCurrent         |`bool`     |このデータベースが要求のデータベースコンテキストであるかどうか                    |
|DatabaseAccessMode|`string`   |、、 `ReadWrite` `ReadOnly` `ReadOnlyFollowing` 、またはのいずれか`ReadWriteEphemeral`    |
|"この名前"        |`string`   |データベースの愛称 (存在する場合)                        |
|ReservedSlot1     |`bool`     |予約済み。 内部使用のみ              |
|DatabaseId        |`guid`     |データベースのグローバル一意識別子。 内部使用のみ          |
