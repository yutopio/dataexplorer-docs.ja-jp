---
title: .show データベース - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .show データベースについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1827c3ea20d984c6846ef9feb603398020efe275
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610185"
---
# <a name="show-databases"></a>.show データベース

ユーザーがアクセスできるクラスター内のデータベースに対応する、すべてのレコードが含まれるテーブルを返します。

コンテキスト データベースのプロパティを示すテーブルを返す方法については[、「.show データベース](show-database.md)」を参照してください。

**構文**

`.show` `databases`

**出力スキーマ**

|列名       |列の型|説明                                                                  |
|------------------|-----------|-----------------------------------------------------------------------------|
|DatabaseName      |`string`   |クラスターにアタッチされたデータベースの名前。                         |
|永続ストレージ |`string`   |データベースの永続ストレージ 「ルート」。 (内部使用のみ。)      |
|Version           |`string`   |データベースのバージョンです。 (内部使用のみ。)                        |
|IsCurrent         |`bool`     |このデータベースが要求のデータベース コンテキストであるかどうか。                |
|データベース アクセス モード|`string`   |`ReadWrite`、 、 `ReadOnly` `ReadOnlyFollowing`、または`ReadWriteEphemeral`の 1 つ。|
|プリティネーム        |`string`   |データベースの名前がかなり存在する場合。                                    |
|リザーブドスロット1     |`bool`     |予約済み。 (内部使用のみ。)                                           |
|DatabaseId        |`guid`     |データベースのグローバル一意識別子。 (内部使用のみ。)      |
