---
title: クラスターのフォロワーコマンド-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのクラスターのフォロワーコマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 2ca15e1970ab785bfd5da8623f3dcc569576f1d9
ms.sourcegitcommit: 2ee2901cb82e1655b7f0d960d3427da084230731
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94520566"
---
# <a name="cluster-follower-commands"></a>クラスターのフォロワーコマンド

フォロワークラスター構成を管理するための制御コマンドを以下に示します。 これらのコマンドは同期的に実行されますが、次回の定期的なスキーマ更新に適用されます。 そのため、新しい構成が適用されるまで数分の遅延が発生する場合があります。

フォロワーコマンドには、 [データベースレベルのコマンド](#database-level-commands) と [テーブルレベルのコマンド](#table-level-commands)が含まれます。

## <a name="database-level-commands"></a>データベースレベルのコマンド

### <a name="show-follower-database"></a>。フォロワーデータベースを表示します

データベース (またはデータベース) の後に、1つまたは複数のデータベースレベルの上書きが構成されている他のリーダークラスターを表示します。

**構文**

`.show``follower` `database` *DatabaseName*

`.show``follower` `databases` `(`*DatabaseName1* `,`...`,`*DatabaseNameN*`)`

**出力** 

| 出力パラメーター                     | Type    | 説明                                                                                                        |
|--------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| DatabaseName                         | String  | フォローされているデータベースの名前。                                                                           |
| LeaderClusterMetadataPath            | String  | リーダークラスターのメタデータコンテナーへのパス。                                                               |
| CachingPolicyOverride                | String  | JSON または null としてシリアル化された、データベースのオーバーライドキャッシュポリシー。                                         |
| AuthorizedPrincipalsOverride         | String  | JSON または null としてシリアル化された、データベースの承認されたプリンシパルの上書きコレクション。                    |
| AuthorizedPrincipalsModificationKind | String  | AuthorizedPrincipalsOverride ( `none` 、 `union` または) を使用して適用する変更の種類 `replace` 。                  |
| CachingPoliciesModificationKind      | String  | データベースまたはテーブルレベルのキャッシュポリシーオーバーライド ( `none` 、 `union` または) を使用して適用する変更の種類 `replace` 。 |
| IsAutoPrefetchEnabled                | Boolean | スキーマの更新のたびに、新しいデータが事前フェッチされるかどうか。        |
| TableMetadataOverrides               | String  | テーブルレベルのプロパティのオーバーライドを JSON でシリアル化します (定義されている場合)。                                      |

### <a name="alter-follower-database-policy-caching"></a>。 alter フォロワーデータベースポリシーのキャッシュ

フォロワーデータベースキャッシュポリシーを変更して、リーダークラスター内のソースデータベースで設定されているものを上書きします。 [Databaseadmin のアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**メモ**

* `modification kind`キャッシュポリシーの既定値は `union` です。 を変更するには、使用して、変更、 `modification kind` [データベースのキャッシュ-ポリシー-](#alter-follower-database-caching-policies-modification-kind) 変更-種類コマンド。
* 次のコマンドを使用して、変更後のポリシーまたは有効なポリシーを表示でき `.show` ます。
    * [。データベースポリシーの保有期間を表示する](../management/retention-policy.md#show-retention-policy)
    * [。データベースの詳細を表示します](../management/show-databases.md)
    * [.show テーブルの詳細](show-tables-command.md)
* 変更後にフォロワーデータベースの上書き設定を表示するには、を使用し[ます。フォロワーデータベースを表示](#show-follower-database)する

**構文**

`.alter``follower` `database` *DatabaseName* `policy` `caching` `hot` `=` *ホット dataspan*

**例**

```kusto
.alter follower database MyDb policy caching hot = 7d
```

### <a name="delete-follower-database-policy-caching"></a>。フォロワーデータベースポリシーのキャッシュを削除します。

フォロワーデータベースオーバーライドキャッシュポリシーを削除します。 これにより、リーダークラスターのソースデータベースで設定されたポリシーが有効になります。
[Databaseadmin のアクセス許可](../management/access-control/role-based-authorization.md)が必要です。 

**メモ**

* 次のコマンドを使用して、変更後のポリシーまたは有効なポリシーを表示でき `.show` ます。
    * [。データベースポリシーの保有期間を表示する](../management/retention-policy.md#show-retention-policy)
    * [。データベースの詳細を表示します](../management/show-databases.md)
    * [.show テーブルの詳細](show-tables-command.md)
* 変更後にフォロワーデータベースの上書き設定を表示するには、を使用し[ます。フォロワーデータベースを表示](#show-follower-database)する

**構文**

`.delete``follower` `database` *DatabaseName* DatabaseName `policy``caching`

**例**

```kusto
.delete follower database MyDB policy caching
```

### <a name="add-follower-database-principals"></a>。フォロワーデータベースプリンシパルを追加します

承認されたプリンシパルのフォロワーデータベースコレクションに承認されたプリンシパルを追加します。 [Databaseadmin 権限](../management/access-control/role-based-authorization.md)が必要です。

**メモ**

* `modification kind`このような承認されたプリンシパルの既定値は `none` です。 `modification kind` [Alter フォロワーデータベースプリンシパル](#alter-follower-database-principals-modification-kind)の使用を変更するには、「変更-種類」を使用します。
* 次のコマンドを使用して、変更後のプリンシパルの有効なコレクションを表示でき `.show` ます。
    * [。データベースプリンシパルを表示します。](../management/security-roles.md#managing-database-security-roles)
    * [。データベースの詳細を表示します](../management/show-databases.md)
* 変更後にフォロワーデータベースの上書き設定を表示するには、を使用し[ます。フォロワーデータベースを表示](#show-follower-database)する

**構文**

`.add``follower` `database` *DatabaseName* ( `admins`  |  `users`  |  `viewers`  |  `monitors` ) Role `(` *principal1* `,` ... `,`*Principaln* `)`[ `'` *メモ* `'` ]

**例**

```kusto
.add follower database MyDB viewers ('aadgroup=mygroup@microsoft.com') 'My Group'
```

### <a name="drop-follower-database-principals"></a>。フォロワーデータベースプリンシパルを削除します。

承認されたプリンシパルのフォロワーデータベースコレクションから承認されたプリンシパルを削除します。
[Databaseadmin のアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**メモ**

* 次のコマンドを使用して、変更後のプリンシパルの有効なコレクションを表示でき `.show` ます。
    * [。データベースプリンシパルを表示します。](../management/security-roles.md#managing-database-security-roles)
    * [。データベースの詳細を表示します](../management/show-databases.md)
* 変更後にフォロワーデータベースの上書き設定を表示するには、を使用し[ます。フォロワーデータベースを表示](#show-follower-database)する

**構文**

`.drop``follower` `database` *DatabaseName* ( `admins`  |  `users`  |  `viewers`  |  `monitors` ) `(` *principal1* `,` ... `,`*Principaln*`)`

**例**

```kusto
.drop follower database MyDB viewers ('aadgroup=mygroup@microsoft.com')
```

### <a name="alter-follower-database-principals-modification-kind"></a>. alter フォロワーデータベースプリンシパル-変更-kind

フォロワーデータベースの承認されたプリンシパルの変更の種類を変更します。 [Databaseadmin のアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**メモ**

* 次のコマンドを使用して、変更後のプリンシパルの有効なコレクションを表示でき `.show` ます。
    * [。データベースプリンシパルを表示します。](../management/security-roles.md#managing-database-security-roles)
    * [。データベースの詳細を表示します](../management/show-databases.md)
* 変更後にフォロワーデータベースの上書き設定を表示するには、を使用し[ます。フォロワーデータベースを表示](#show-follower-database)する

**構文**

`.alter``follower` `database` *DatabaseName* 
 `principals-modification-kind` = ( `none`  |  `union`  |  `replace` )

**例**

```kusto
.alter follower database MyDB principals-modification-kind = union
```

### <a name="alter-follower-database-caching-policies-modification-kind"></a>. alter フォロワー database のキャッシュポリシー-変更-種類

フォロワーデータベースおよびテーブルキャッシュポリシーの変更の種類を変更します。 [Databaseadmin のアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**メモ**

* 変更後のデータベース/テーブルレベルのキャッシュポリシーの有効なコレクションを表示するには、次の標準コマンドを使用し `.show` ます。
    * [。テーブルの詳細を表示します](show-tables-command.md)
    * [。データベースの詳細を表示します](../management/show-databases.md)
* 変更後にフォロワーデータベースの上書き設定を表示するには、を使用し[ます。フォロワーデータベースを表示](#show-follower-database)する

**構文**

`.alter``follower` `database` *DatabaseName* `caching-policies-modification-kind` = ( `none`  |  `union`  |  `replace` )

**例**

```kusto
.alter follower database MyDB caching-policies-modification-kind = union
```

### <a name="alter-follower-database-prefetch-extents"></a>. alter フォロワー database プリフェッチ-エクステント

フォロワークラスターは、基になるストレージからノード ' SSD (キャッシュ) に新しいデータがフェッチされるのを待機してから、このデータをクエリ可能にします。

次のコマンドは、各スキーマ更新時に新しいエクステントをプリフェッチする、フォロワーデータベース構成を変更します。 このコマンドには、 [Databaseadmin のアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

> [!WARNING]
> * この設定により、フォロワーデータベースのデータの鮮度が低下する可能性があります。
> * 既定の構成はです `false` 。既定の構成を使用することをお勧めします。
> * 設定をに変更することを選択した場合は `true` 、構成の変更後、ある期間の鮮度への影響を厳密に評価します。

**構文**

`.alter``follower` `database` *DatabaseName* `prefetch-extents` = ( `true`  |  `false` )

`.alter``follower` `database` *DatabaseName* [ `from` `h@'` *リーダークラスターのメタデータコンテナーへのパス* `'` ] `prefetch-extents` = ( `true`  |  `false` )

**例**

<!-- csl -->
```
.alter follower database MyDB prefetch-extents = false
```

## <a name="table-level-commands"></a>テーブルレベルのコマンド

### <a name="alter-follower-table-policy-caching"></a>。テーブルポリシーのキャッシュを変更します。

フォロワーデータベースのテーブルレベルのキャッシュポリシーを変更して、リーダークラスターのソースデータベースで設定されているポリシーを上書きします。
[Databaseadmin のアクセス許可](../management/access-control/role-based-authorization.md)が必要です。 

**メモ**

* 次のコマンドを使用して、変更後のポリシーまたは有効なポリシーを表示でき `.show` ます。
    * [。データベースポリシーの保有期間を表示する](../management/retention-policy.md#show-retention-policy)
    * [。データベースの詳細を表示します](../management/show-databases.md)
    * [.show テーブルの詳細](show-tables-command.md)
* 変更後にフォロワーデータベースの上書き設定を表示するには、を使用し[ます。フォロワーデータベースを表示](#show-follower-database)する

**構文**

`.alter``follower` `database` *DatabaseName* テーブル *TableName* `policy` `caching` `hot` `=` *ホット dataspan*

`.alter``follower` `database` *DatabaseName* テーブル `(` *TableName1* `,` ... `,`*TableNameN* `)``policy` `caching` `hot``=`*ホット dataspan*

**例**

```kusto
.alter follower database MyDb tables (Table1, Table2) policy caching hot = 7d
```

### <a name="delete-follower-table-policy-caching"></a>。フォロワーテーブルポリシーのキャッシュを削除します。

フォロワーデータベースの上書きテーブルレベルキャッシュポリシーを削除して、リーダークラスターのソースデータベースでポリシーを設定します。 [Databaseadmin のアクセス許可](../management/access-control/role-based-authorization.md)が必要です。 

**メモ**

* 次のコマンドを使用して、変更後のポリシーまたは有効なポリシーを表示でき `.show` ます。
    * [。データベースポリシーの保有期間を表示する](../management/retention-policy.md#show-retention-policy)
    * [。データベースの詳細を表示します](../management/show-databases.md)
    * [.show テーブルの詳細](show-tables-command.md)
* 変更後にフォロワーデータベースの上書き設定を表示するには、を使用し[ます。フォロワーデータベースを表示](#show-follower-database)する

**構文**

`.delete``follower` `database` *DatabaseName* `table` *TableName* TableName `policy``caching`

`.delete``follower` `database` *DatabaseName* `tables` `(` *TableName1* `,` ... `,`*TableNameN* `)``policy``caching`

**例**

```kusto
.delete follower database MyDB tables (Table1, Table2) policy caching
```

## <a name="sample-configuration"></a>サンプル構成

次に、フォロワーデータベースを構成する手順の例を示します。

次の点に注意してください。

* フォロワークラスターは、 `MyFollowerCluster` リーダークラスターのデータベース `MyDatabase` に従って `MyLeaderCluster` います。
    * `MyDatabase``N`テーブル: `MyTable1` 、、 `MyTable2` `MyTable3` 、... `MyTableN` ( `N` > 3)。
    * `MyLeaderCluster`の場合:
    
    | `MyTable1` キャッシュポリシー | `MyTable2` キャッシュポリシー | `MyTable3`...`MyTableN` キャッシュポリシー   | `MyDatabase` 承認されたプリンシパル                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | ホットデータスパン = `7d`      | ホットデータスパン = `30d`     | ホットデータスパン = `365d`                   | *ビューアー*  =  `aadgroup=scubadivers@contoso.com` ; *管理者* = `aaduser=jack@contoso.com` |
     
    * `MyFollowerCluster`必要なのは次のとおりです。
    
    | `MyTable1` キャッシュポリシー | `MyTable2` キャッシュポリシー | `MyTable3`...`MyTableN` キャッシュポリシー   | `MyDatabase` 承認されたプリンシパル                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | ホットデータスパン = `1d`      | ホットデータスパン = `3d`      | ホットデータスパン = `0d` (キャッシュされているものはありません) | *管理者*  =  `aaduser=jack@contoso.com` 、 *ビューアー* = `aaduser=jill@contoso.com`         |

> [!IMPORTANT] 
> `MyFollowerCluster`とは両方とも `MyLeaderCluster` 同じリージョンに存在する必要があります。

### <a name="steps-to-execute"></a>実行する手順

*前提条件:* クラスター `MyFollowerCluster` のデータベースをフォローするようにクラスターをセットアップ `MyDatabase` `MyLeaderCluster` します。

> [!NOTE]
> 制御コマンドを実行するプリンシパルは、データベース上のであることが必要です `DatabaseAdmin` `MyDatabase` 。

#### <a name="show-the-current-configuration"></a>現在の構成を表示します

に従って現在の構成を確認し `MyDatabase` `MyFollowerCluster` ます。

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| 列                              | 値                                                    |
|-------------------------------------|----------------------------------------------------------|
|DatabaseName                         | MyDatabase                                               |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster` |
|CachingPolicyOverride                | null                                                     |
|AuthorizedPrincipalsOverride         | []                                                       |
|AuthorizedPrincipalsModificationKind | なし                                                     |
|IsAutoPrefetchEnabled                | False                                                    |
|TableMetadataOverrides               |                                                          |
|CachingPoliciesModificationKind      | Union                                                    |                                                                                                                      |

#### <a name="override-authorized-principals"></a>承認されたプリンシパルのオーバーライド

の承認されたプリンシパルのコレクションを、 `MyDatabase` `MyFollowerCluster` データベース管理者として aad ユーザーを1人だけ含むコレクションに置き換え、1つの aad ユーザーをデータベースビューアーとして置き換えます。

```kusto
.add follower database MyDatabase admins ('aaduser=jack@contoso.com')

.add follower database MyDatabase viewers ('aaduser=jill@contoso.com')

.alter follower database MyDatabase principals-modification-kind = replace
```

次の2つ `MyDatabase` の特定のプリンシパルに対してのみアクセスが許可されています `MyFollowerCluster`

```kusto
.show database MyDatabase principals
```

| Role                       | PrincipalType | PrincipalDisplayName                        | PrincipalObjectId                    | Principal(n)                                                                      | Notes |
|----------------------------|---------------|---------------------------------------------|--------------------------------------|-----------------------------------------------------------------------------------|-------|
| データベース MyDatabase 管理者  | AAD ユーザー      | ジャック Kusto (upn: jack@contoso.com )       | 12345678-abcd-efef-1234-350bf486087b | aaduser = 87654321-efef1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |
| データベース MyDatabase ビューアー | AAD ユーザー      | Jill Kusto (upn: jack@contoso.com )       | abcdefab-abcd-efef-1234-350bf486087b | aaduser = 54321789氏 efef1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(AuthorizedPrincipalsOverride)
| project AuthorizedPrincipalsOverride.Principal.FullyQualifiedName
```

| AuthorizedPrincipalsOverride_Principal_FullyQualifiedName                         |
|-----------------------------------------------------------------------------------|
| aaduser = 87654321-efef1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |
| aaduser = 54321789氏 efef1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |

#### <a name="override-caching-policies"></a>キャッシュポリシーのオーバーライド

に対するデータベースおよびテーブルレベルのキャッシュポリシーのコレクションを `MyDatabase` 、 `MyFollowerCluster` すべてのテーブル (2 つの特定のテーブルを除く) を除いて、データがキャッシュされ *ない* ように設定します。これにより、 `MyTable1` `MyTable2` との期間にわたってデータがキャッシュされ `1d` `3d` ます。

```kusto
.alter follower database MyDatabase policy caching hot = 0d

.alter follower database MyDatabase table MyTable1 policy caching hot = 1d

.alter follower database MyDatabase table MyTable2 policy caching hot = 3d

.alter follower database MyDatabase caching-policies-modification-kind = replace
```

データがキャッシュされているのはこれら2つの特定のテーブルだけで、残りのテーブルのホットデータ期間は次のようになり `0d` ます。

```kusto
.show tables details
| summarize TableNames = make_list(TableName) by CachingPolicy
```
| CachingPolicy                                                                | TableNames 場合                  |
|------------------------------------------------------------------------------|-----------------------------|
| {"Dataホットスパン": {"Value": "1.00:00:00"}, "Indexホットスパン": {"Value": "1.00:00:00"}} | ["MyTable1"]                |
| {"Dataホットスパン": {"Value": "3.00:00:00"}, "Indexホットスパン": {"Value": "3.00:00:00"}} | ["MyTable2"]                |
| {"Dataホットスパン": {"Value": "0.00:00:00"}, "Indexホットスパン": {"Value": "0.00:00:00"}} | ["MyTable3",..., "MyTableN"] |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(TableMetadataOverrides)
| project TableMetadataOverrides
```

| TableMetadataOverrides                                                                                              |
|---------------------------------------------------------------------------------------------------------------------|
| {"MyTable1": {"CachingPolicyOverride": {"Dataホットスパン": {"Value": "1.00:00:00"}, "Indexホットスパン": {"Value": "1.00:00:00"}}}} |
| {"MyTable2": {"CachingPolicyOverride": {"Dataホットスパン": {"Value": "3.00:00:00"}, "Indexホットスパン": {"Value": "3.00:00:00"}}}} |

#### <a name="summary"></a>まとめ

をフォローしている現在の構成を確認し `MyDatabase` `MyFollowerCluster` ます。

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| 列                              | 値                                                                                                                                                                           |
|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|DatabaseName                         | MyDatabase                                                                                                                                                                      |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster`                                                                                                                        |
|CachingPolicyOverride                | {"Dataホットスパン": {"Value": "00:00:00"}, "Indexホットスパン": {"Value": "00:00:00"}}                                                                                                        |
|AuthorizedPrincipalsOverride         | [{"Principal": {"FullyQualifiedName": "aaduser = 87654321234-350bf486087b",...}、{"Principal": {"FullyQualifiedName": "aaduser = 54321789 efef1234-350bf486087b",...}] |
|AuthorizedPrincipalsModificationKind | Replace                                                                                                                                                                         |
|IsAutoPrefetchEnabled                | False                                                                                                                                                                           |
|TableMetadataOverrides               | {"MyTargetTable": {"CachingPolicyOverride": {"" MySourceTable ": {" CachingPolicyOverride ": {" Dataホットスパン ": {" Value ":" 1.00:00:00 "},...}}} のように、{" MyTargetTable ": {" CachingPolicyOverride ": {" Dataホットスパン ": {       |
|CachingPoliciesModificationKind      | Replace                                                                                                                                                                         |
