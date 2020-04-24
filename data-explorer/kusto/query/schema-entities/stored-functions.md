---
title: ストアドファンクション - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの格納された関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2b26fc443558724f84e76d13bf9e255a65838a2c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509352"
---
# <a name="stored-functions"></a>ストアド関数

**関数**は、再利用可能なクエリまたはクエリ部分です。 関数は、**ストアドファンクション**と呼ばれるデータベース エンティティ (テーブルなど) として格納できます。 関数は、**ラムダ式**と呼ばれるメカニズムを通じて[let ステートメント](../letstatement.md)を使用して、クエリ内でアドホック形式で作成することもできます。

関数呼び出しの構文と規則の詳細については、[ユーザー定義](../functions/user-defined-functions.md)関数を参照してください。
ストア機能の作成と管理の詳細については、[管理機能を参照](../../management/functions.md)してください。