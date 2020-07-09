---
title: . drop エクステント-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの [エクステントの削除] コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: dfa462ca82cd5e94adff77b3893b3b02d60c6cdc
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060726"
---
# <a name="drop-extents"></a>.drop extents

指定されたデータベースまたはテーブルからエクステントを削除します。

このコマンドにはいくつかのバリエーションがあります。1つは、削除されるエクステントが Kusto クエリによって指定されていることです。 その他のバリアントでは、以下に説明するミニ言語を使用してエクステントが指定されています。

指定されたクエリによって返されるエクステントを持つ各テーブルに対する[table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

> [!NOTE]
> データシャードは Kusto では**エクステント**と呼ばれ、すべてのコマンドはシノニムとして "エクステント" または "エクステント" を使用します。
> エクステントの詳細については、「[エクステント (データシャード) の概要](extents-overview.md)」を参照してください。

## <a name="drop-extents-with-a-query"></a>クエリを使用したエクステントの削除

Kusto クエリを使用して指定されたエクステントを削除します。
"ExtentId" という名前の列を含むレコードセットが返されます。

が使用されている場合 `whatif` 、は実際には削除せずに報告します。

### <a name="syntax"></a>構文

`.drop``extents`[ `whatif` ] <|*クエリ*

## <a name="drop-a-specific-extent"></a>特定のエクステントを削除する

テーブル名が指定されている場合、 [table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

テーブル名が指定されていない場合は、 [Database admin 権限](../management/access-control/role-based-authorization.md)が必要です。

### <a name="syntax"></a>構文

`.drop``extent` *ExtentId* [ `from` *TableName*]

## <a name="drop-specific-multiple-extents"></a>特定の複数のエクステントを削除する

テーブル名が指定されている場合、 [table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

テーブル名が指定されていない場合は、 [Database admin 権限](../management/access-control/role-based-authorization.md)が必要です。

### <a name="syntax"></a>構文

`.drop``extents` `(` *ExtentId1* `,` ...*Extentidn* `)`[ `from` *TableName*]

## <a name="drop-extents-by-specified-properties"></a>指定されたプロパティによるエクステントの削除

コマンドは、コマンドが実行されたかのように出力を生成するエミュレーションモードをサポートしますが、実際には実行されません。 `.drop-pretend` の代わりに `.drop`を使用します。

テーブル名が指定されている場合、 [table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

テーブル名が指定されていない場合は、 [Database admin 権限](../management/access-control/role-based-authorization.md)が必要です。

### <a name="syntax"></a>構文

`.drop``extents`[ `older` *N* ( `days`  |  `hours` )] `from` (*TableName*  |  `all` `tables` ) [ `trim` `by` ( `extentsize`  |  `datasize` ) *N* ( `MB`  |  `GB`  |  `bytes` )] [ `limit` *limitcount*]

* `older`: *N*日/時間よりも古いエクステントのみが削除されます。
* `trim`: エクステントの合計が必要なサイズ (MaxSize) と一致するまで、データベース内のデータがトリミングされます。
* `limit`: 操作は、最初の*Limitcount*エクステントに適用されます。

## <a name="examples"></a>例

### <a name="remove-all-extents-by-time-created"></a>作成された時間によってすべてのエクステントを削除する

10日前より前に作成されたすべてのエクステントを、データベース内のすべてのテーブルから削除します。`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

### <a name="remove-some-extents-by-time-created"></a>作成された時間によって一部のエクステントを削除する

テーブル内のすべてのエクステント `Table1` を削除し、その `Table2` 作成時間が10日前を超えた場合

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

### <a name="emulation-mode-show-which-extents-would-be-removed-by-the-command"></a>エミュレーションモード: コマンドによって削除されるエクステントを表示します

>[!NOTE]
>エクステント ID パラメーターは、このコマンドには適用できません。

```kusto
.drop-pretend extents older 10 days from all tables
```

### <a name="remove-all-extents-from-testtable"></a>' TestTable ' からすべてのエクステントを削除します

```kusto
.drop extents from TestTable
```

## <a name="return-output"></a>出力を返す

|出力パラメーター |Type |説明 
|---|---|---
|ExtentId |String |コマンドのために削除された ExtentId
|TableName |String |エクステントが属しているテーブル名  
|Event.manualintervention.createdon |DateTime |エクステントが最初に作成された日時に関する情報を保持するタイムスタンプ
 
## <a name="sample-output"></a>サンプル出力

|エクステント ID |テーブル名 |作成日時 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48: 49.4298178
