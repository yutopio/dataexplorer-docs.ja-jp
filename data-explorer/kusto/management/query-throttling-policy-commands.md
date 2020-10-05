---
title: クエリ調整ポリシーのコマンド-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのクエリ調整ポリシーコマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 85408aadfd15fc48f1e3e86ab01afce5dca15bce
ms.sourcegitcommit: acd8aba5ed957bee4a7361057387145f9d3ef4e3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91734016"
---
# <a name="query-throttling-policy-commands"></a>クエリ調整ポリシーのコマンド

[クエリの調整ポリシー](query-throttling-policy.md)はクラスターレベルのポリシーであり、クラスター内のクエリの同時実行を制限します。 これらのクエリ調整ポリシーコマンドには、 [Alldatabasesadmin](../management/access-control/role-based-authorization.md) アクセス許可が必要です。

## <a name="query-throttling-policy-object"></a>クエリ調整ポリシーオブジェクト

クラスターには、0個または1個のクエリ調整ポリシーが定義されている場合があります。

クラスターにクエリ制限ポリシーが定義されていない場合、既定のポリシーが適用されます。 既定のポリシーの詳細については、「 [クエリの制限](../concepts/querylimits.md)」を参照してください。

|プロパティ  |Type    |説明                                                       |
|----------|--------|------------------------------------------------------------------|
|IsEnabled |`bool`  |クエリ調整ポリシーが有効 (true) か無効 (false) かを示します。     |
|MaxQuantity|`int`|クラスターが実行できる同時実行クエリの数。 数値は正の値である必要があります。 |

## `.show cluster policy querythrottling`

クラスターの [クエリ制限ポリシー](query-throttling-policy.md) を返します。

### <a name="syntax"></a>構文

`.show` `cluster` `policy` `querythrottling`

### <a name="returns"></a>戻り値

次の列を含むテーブルを返します。

|Column    |種類    |説明
|---|---|---
|PolicyName| string |ポリシー名-QueryThrottlingPolicy
|EntityName| string |Empty
|ポリシー    | string |クエリ調整ポリシーを定義する JSON オブジェクト。クエリ[調整ポリシーオブジェクト](#query-throttling-policy-object)として書式設定されています。

### <a name="example"></a>例

<!-- csl -->
```
.show cluster policy querythrottling 
```

戻り値:

|PolicyName|EntityName|ポリシー|ChildEntities|EntityType|
|---|---|---|---|---|
|QueryThrottlingPolicy||{"IsEnabled": true、"MaxQuantity":25}

## `.alter cluster policy querythrottling`

指定されたテーブルの [クエリ制限ポリシー](query-throttling-policy.md) を設定します。 

### <a name="syntax"></a>構文

`.alter``cluster` `policy` `querythrottling`*QueryThrottlingPolicyObject*

### <a name="arguments"></a>引数

*QueryThrottlingPolicyObject* は、クエリ調整ポリシーオブジェクトが定義されている JSON オブジェクトです。

### <a name="returns"></a>戻り値

クラスタークエリの調整ポリシーオブジェクトを設定し、定義されている現在のポリシーを上書きしてから、対応する[. クラスターポリシー `querythrottling` の表示](#show-cluster-policy-querythrottling)コマンドの出力を返します。

### <a name="example"></a>例

<!-- csl -->
```
.alter cluster policy querythrottling '{"IsEnabled": true, "MaxQuantity": 25}'
```

|PolicyName|EntityName|ポリシー|ChildEntities|EntityType|
|---|---|---|---|---|
|QueryThrottlingPolicy||{"IsEnabled": true、"MaxQuantity":25}

## `.delete cluster policy querythrottling`

クラスタークエリの [調整ポリシー](query-throttling-policy.md) オブジェクトを削除します。

### <a name="syntax"></a>構文

`.delete` `cluster` `policy` `querythrottling`
