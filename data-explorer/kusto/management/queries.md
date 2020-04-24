---
title: クエリ管理 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクエリ管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: b888aa004b4c8b02cd4e1d130bb0b5319af018b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520521"
---
# <a name="queries-management"></a>クエリ管理

## <a name="show-queries"></a>.show クエリ

この`.show``queries`コマンドは、最終状態に達したクエリのリストを返し、コマンドを呼び出すユーザーが次の情報を参照できます。


* [データベース管理者またはデータベース・モニター](../management/access-control/role-based-authorization.md)は、データベースで呼び出されたコマンドを表示できます。
* 他のユーザーは、そのユーザーが呼び出したクエリのみを表示できます。

**構文**

`.show` `queries`

## <a name="show-running-queries"></a>実行中のクエリを表示する .show

この`.show``running``queries`コマンドは、ユーザー、別のユーザー、またはすべてのユーザーが現在実行中のクエリの一覧を返します。

**構文**

```kusto
.show running queries
```

* (1) 呼び出し元のユーザーが現在実行中のクエリを返します (読み取りアクセスが必要です)。

## <a name="cancel-query"></a>.cancel クエリ

この`.cancel``query`コマンドは、同じユーザーが以前に開始した特定の照会を取り消すベスト・エフォートの試行を開始します。

* クラスター管理者は、実行中のクエリをキャンセルできます。
* データベース管理者は、管理者アクセス権を持つデータベースで呼び出された実行中のクエリをキャンセルできます。
* 他のユーザーは、自分が開始したクエリのみをキャンセルできます。 

**構文**

`.cancel``query`*クライアント要求 ID*

* *は*、リテラルとして、元のクエリの ClientRequestId フィールドの値です`string`。

**例**

```kusto
.cancel query "KE.RunQuery;8f70e9ab-958f-4955-99df-d2a288b32b2c"
```

