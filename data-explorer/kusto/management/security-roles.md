---
title: セキュリティロールの管理-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのセキュリティロールの管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4bda122b589e3ba297b3e7c350d15687da6ee123
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057004"
---
# <a name="security-roles-management"></a>セキュリティロールの管理

> [!IMPORTANT]
> Kusto クラスターで承認規則を変更する前に、 [kusto access control の概要](../management/access-control/index.md)  
>  [ロールベースの承認](../management/access-control/role-based-authorization.md)に関するトピックをお読みください。 

この記事では、セキュリティロールの管理に使用する制御コマンドについて説明します。
セキュリティロールでは、データベースやテーブルなどのセキュリティで保護されたリソースを操作する権限を持つセキュリティプリンシパル (ユーザーおよびアプリケーション) と、許可する操作を定義します。 たとえば、特定のデータベースのセキュリティロールを持つプリンシパルは、 `database viewer` そのデータベースのすべてのエンティティを照会して表示できます (ただし、制限されたテーブルは除きます)。

セキュリティロールは、セキュリティプリンシパルまたはセキュリティグループに関連付けることができます (他のセキュリティプリンシパルまたは他のセキュリティグループを持つことができます)。 セキュリティプリンシパルがセキュリティで保護されたリソースに対して操作を実行しようとすると、システムは、そのプリンシパルが少なくとも1つのセキュリティロールに関連付けられているかどうかを確認し、リソースに対してこの操作を実行するアクセス許可を付与します。 これは、 **承認チェック**と呼ばれます。 承認チェックに失敗すると、操作が中止されます。

**構文**

セキュリティロール管理コマンドの構文は次のとおりです。

*動詞**セキュリティー ableobjecttype* \ *ableobjectname* *Role* [ `(` *listofprincipals* `)` [*Description*]]

* *動詞* は、実行するアクションの種類を示します。、、 `.show` `.add` `.drop` 、および `.set` です。

    |*動詞* |説明                                  |
    |-------|---------------------------------------------|
    |`.show`|現在の値を返します。         |
    |`.add` |ロールに1つまたは複数のプリンシパルを追加します。     |
    |`.drop`|ロールから1つまたは複数のプリンシパルを削除します。|
    |`.set` |ロールをプリンシパルの特定のリストに設定し、以前のものをすべて削除します。|

* *セキュリティー Ableobjecttype* は、ロールが指定されているオブジェクトの種類です。

    |*セキュリティー Ableobjecttype*|説明|
    |---------------------|-----------|
    |`database`|指定されたデータベース|
    |`table`|指定されたテーブル|
    |`materialized-view`| 指定した具体化された [ビュー](materialized-views/materialized-view-overview.md)| 

* *セキュリティー Ableobjectname* は、オブジェクトの名前です。

* *Role* は、関連するロールの名前です。

    |*Role*      |説明|
    |------------|-----------|
    |`principals`|は動詞の一部としてのみ使用できます。は、 `.show` セキュリティ保護可能なオブジェクトに影響を与える可能性があるプリンシパルの一覧を返します。|
    |`admins`    |オブジェクトとすべてのサブオブジェクトを表示、変更、および削除する機能を含む、セキュリティ保護可能なオブジェクトを制御できます。|
    |`users`     |セキュリティ保護可能なオブジェクトを表示し、その下に新しいオブジェクトを作成できます。|
    |`viewers`   |セキュリティ保護可能なオブジェクトを表示できます。|
    |`unrestrictedviewers`|データベースレベルでのみ、で制限付きテーブルの表示が許可されます (これは "normal" とには公開されません `viewers` `users` )。|
    |`ingestors` |データベースレベルでのみ、すべてのテーブルへのデータインジェストを許可します。|
    |`monitors`  ||

* *Listofprincipals* は省略可能な、コンマで区切られたセキュリティプリンシパル識別子 (型の値) の一覧です `string` 。

* *説明* は、 `string` 将来の監査のために、アソシエーションと共に保存される型のオプションの値です。

## <a name="example"></a>例

次のコントロールコマンドは、データベース内のテーブルへのアクセス権を持つすべてのセキュリティプリンシパルを一覧表示し `StormEvents` ます。

```kusto
.show table StormEvents principals
```

このコマンドを実行すると、次のような結果になる可能性があります。

|Role |PrincipalType |PrincipalDisplayName |PrincipalObjectId |Principal(n) 
|---|---|---|---|---
|データベース Apsty 管理者 |Azure AD ユーザー |Smith のマーク |cd709aed-a26c-e3953dec735e |aaduser =msmith@fabrikam.com|





## <a name="managing-database-security-roles"></a>データベースセキュリティロールの管理

`.set``database` *DatabaseName* *ロール* `none` [ `skip-results` ]

`.set``database` *DatabaseName* *ロール* `(` *プリンシパル*[ `,` *プリンシパル*...] `)` [ `skip-results` ] [*説明*]

`.add``database` *DatabaseName* *ロール* `(` *プリンシパル*[ `,` *プリンシパル*...] `)` [ `skip-results` ] [*説明*]

`.drop``database` *DatabaseName* *ロール* `(` *プリンシパル*[ `,` *プリンシパル*...] `)` [ `skip-results` ] [*説明*]

最初のコマンドは、ロールからすべてのプリンシパルを削除します。 2つ目は、ロールからすべてのプリンシパルを削除し、新しいプリンシパルのセットを設定します。 3番目の方法では、既存のプリンシパルを削除せずに、新しいプリンシパルをロールに追加します。 最後に、指定されたプリンシパルをロールから削除し、他のプリンシパルを保持します。

各値の説明:

* *DatabaseName* セキュリティロールを変更するデータベースの名前を指定します。

* *Role* : `admins` 、、 `ingestors` 、 `monitors` 、 `unrestrictedviewers` `users` 、または `viewers` 。

* *プリンシパル* は、1つまたは複数のプリンシパルです。 これらのプリンシパルを指定する方法については [、「プリンシパルと id プロバイダー](./access-control/principals-and-identity-providers.md) 」を参照してください。

* `skip-results`が指定されている場合、コマンドが更新されたデータベースプリンシパルの一覧を返さないことを要求します。

* *説明*(指定されている場合) は、変更に関連付けられ、対応するコマンドによって取得されるテキストです `.show` 。

<!-- TODO: Need more examples for the public syntax. Until then we're keeping this internal -->


## <a name="managing-table-security-roles"></a>テーブルのセキュリティロールの管理

`.set``table` *TableName* *ロール* `none` [ `skip-results` ]

`.set``table` *TableName* *ロール* `(` *プリンシパル*[ `,` *プリンシパル*...] `)` [ `skip-results` ] [*説明*]

`.add``table` *TableName* *ロール* `(` *プリンシパル*[ `,` *プリンシパル*...] `)` [ `skip-results` ] [*説明*]

`.drop``table` *TableName* *ロール* `(` *プリンシパル*[ `,` *プリンシパル*...] `)` [ `skip-results` ] [*説明*]

最初のコマンドは、ロールからすべてのプリンシパルを削除します。 2つ目は、ロールからすべてのプリンシパルを削除し、新しいプリンシパルのセットを設定します。 3番目の方法では、既存のプリンシパルを削除せずに、新しいプリンシパルをロールに追加します。 最後に、指定されたプリンシパルをロールから削除し、他のプリンシパルを保持します。

各値の説明:

* *TableName* は、セキュリティロールが変更されているテーブルの名前です。

* *Role* : `admins` または `ingestors` 。

* *プリンシパル* は、1つまたは複数のプリンシパルです。 これらのプリンシパルを指定する方法については [、「プリンシパルと id プロバイダー](./access-control/principals-and-identity-providers.md) 」を参照してください。

* `skip-results`が指定されている場合、コマンドがテーブルプリンシパルの更新された一覧を返さないことを要求します。

* *説明*(指定されている場合) は、変更に関連付けられ、対応するコマンドによって取得されるテキストです `.show` 。

**例**

```kusto
.add table Test admins ('aaduser=imike@fabrikam.com ')
```

## <a name="managing-materialized-view-security-roles"></a>具体化したビューのセキュリティロールの管理

`.show``materialized-view` *MaterializedViewName*`principals`

`.set``materialized-view` *MaterializedViewName* `admins` MaterializedViewName `(`*プリンシパル* `,[`*プリンシパル...*`])`

`.add``materialized-view` *MaterializedViewName* `admins` MaterializedViewName `(`*プリンシパル* `,[`*プリンシパル...*`])`

`.drop``materialized-view` *MaterializedViewName* `admins` MaterializedViewName `(`*プリンシパル* `,[`*プリンシパル...*`])`

各値の説明:

* *MaterializedViewName* は、セキュリティロールが変更されている具体化されたビューの名前です。
* *プリンシパル* は、1つまたは複数のプリンシパルです。 「[プリンシパルと id プロバイダー」を](./access-control/principals-and-identity-providers.md)参照してください。

## <a name="managing-function-security-roles"></a>関数のセキュリティロールの管理

`.set``function` *FunctionName* *ロール* `none` [ `skip-results` ]

`.set``function` *FunctionName* *ロール* `(` *プリンシパル*[ `,` *プリンシパル*...] `)` [ `skip-results` ] [*説明*]

`.add``function` *FunctionName* *ロール* `(` *プリンシパル*[ `,` *プリンシパル*...] `)` [ `skip-results` ] [*説明*]

`.drop``function` *FunctionName* *ロール* `(` *プリンシパル*[ `,` *プリンシパル*...] `)` [ `skip-results` ] [*説明*]

最初のコマンドは、ロールからすべてのプリンシパルを削除します。 2つ目は、ロールからすべてのプリンシパルを削除し、新しいプリンシパルのセットを設定します。 3番目の方法では、既存のプリンシパルを削除せずに、新しいプリンシパルをロールに追加します。 最後に、指定されたプリンシパルをロールから削除し、他のプリンシパルを保持します。

各値の説明:

* *FunctionName* は、セキュリティロールが変更されている関数の名前です。

* *ロール* は常に `admin` です。

* *プリンシパル* は、1つまたは複数のプリンシパルです。 これらのプリンシパルを指定する方法については [、「プリンシパルと id プロバイダー](./access-control/principals-and-identity-providers.md) 」を参照してください。

* `skip-results`が指定されている場合、コマンドが更新された関数プリンシパルの一覧を返さないことを要求します。

* *説明*(指定されている場合) は、変更に関連付けられ、対応するコマンドによって取得されるテキストです `.show` 。

**例**

```kusto
.add function MyFunction admins ('aaduser=imike@fabrikam.com') 'This user should have access'
```

