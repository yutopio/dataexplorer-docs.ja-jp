---
title: ストレージ接続文字列 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのストレージ接続文字列について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 909198e42786666d3a26874e18b5cfd57b27ab1f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502960"
---
# <a name="storage-connection-strings"></a>ストレージの接続文字列

いくつかの Kusto コマンドは、外部ストレージ サービスと対話するように Kusto に指示します。 たとえば、Kusto は Azure Storage BLOB にデータをエクスポートするように指定できます。

Kusto は、次のストレージ プロバイダーをサポートします。


* Azure ストレージ BLOB ストレージ プロバイダー
* Azure データ レイク ストレージ プロバイダー

ストレージ プロバイダーの種類ごとに、ストレージ リソースとそのアクセス方法を記述するために使用される接続文字列の形式が定義されます。
Kusto は URI 形式を使用して、これらのストレージ リソースと、アクセスに必要なプロパティ (セキュリティ資格情報など) を記述します。


|プロバイダー                   |Scheme    |URI テンプレート                          |
|---------------------------|----------|--------------------------------------|
|Azure Storage BLOB         |`https://`|`https://`*アカウント*`.blob.core.windows.net/`*Container*コンテナー`/`[ BLOB`?`*名*] [*SasKey* \| `;`*アカウントキー*]|
|Azure Data Lake Store Gen 2|`abfss://`|`abfss://`*ファイル システム*`@`*アカウント*`.dfs.core.windows.net/`*パスディレクトリまたはファイル*[`;`*呼び出し元の資格情報*]|
|Azure Data Lake Store Gen 1|`adl://`  |`adl://`*アカウント*.azuredatalakestore.net/*パスディレクトリまたはファイル*[`;`*呼び出し元の資格情報*]|

## <a name="azure-storage-blob"></a>Azure Storage BLOB

このプロバイダーは最も一般的に使用され、すべてのシナリオでサポートされています。
リソースにアクセスする際には、プロバイダーに資格情報を指定する必要があります。 資格情報を提供するために、2 つのサポートされているメカニズムがあります。

* Azure ストレージ BLOB の標準クエリ ( )`?sig=...`を使用して、共有アクセス (SAS) キーを指定します。 Kusto がリソースに制限された時間だけアクセスする必要がある場合は、このメソッドを使用します。
* ストレージ アカウント キー`;ljkAkl...==`( ) を指定します。 Kusto が継続的にリソースにアクセスする必要がある場合は、このメソッドを使用します。

例 (これは、アカウント キーまたは SAS を公開しないように、難読化された文字列リテラルを示しています)。

`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv;<storage_account_key_text, ends with '=='>"`
`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv?sv=...&sp=rwd"` 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="azure-data-lake-store-gen-2"></a>Azure Data Lake Store Gen 2

このプロバイダーは、Azure データ 湖ストア Gen 2 のデータへのアクセスをサポートします。

URI の形式は次のとおりです。

`abfss://`*ファイル システム*`@`*ストレージ アカウント名*`.dfs.core.windows.net/`*パス*`;`*呼び出し元資格情報*

各値の説明:

* _ファイルシステム_は ADLS ファイルシステムオブジェクトの名前です (BLOB コンテナーとほぼ同じです)
* _ストレージ アカウント名_はストレージ アカウントの名前です。
* _パス_は、アクセスされるディレクトリーまたはファイルへのパススラッシュ ()`/`文字が区切り文字として使用されます。
* _呼び出し元の資格情報は_、次に説明するように、サービスにアクセスするために使用される資格情報を示します。

Azure Data Lake Store Gen 2 にアクセスする場合、呼び出し元はサービスにアクセスするための有効な資格情報を提供する必要があります。 資格情報を提供する次の方法がサポートされています。

* `;sharedkey=`*アカウントキー*をストレージ アカウント キー_として_URI に追加する
* URI`;impersonate`に追加します。 Kusto は、要求元のプリンシパル ID を使用し、リソースにアクセスするためにそれを偽装します。 読み取り/書き込み操作を実行するには、プリンシパルに適切な RBAC ロールの割り当てが必要[です](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 (たとえば、読み取り操作の最小ロールは`Storage Blob Data Reader`ロールです)。
* AadToken が Base 64 でエンコードされた AAD アクセス トークンである URI に`;token=`*AadToken*を追加`https://storage.azure.com/`します (トークンがリソース用であることを確認してください)。 _AadToken_
* URI`;prompt`に追加します。 Kusto は、リソースにアクセスする必要がある場合に、ユーザーの資格情報を要求します。 (ユーザーへの確認はクラウド展開では無効にされ、テスト環境でのみ有効になります)。
* Azure データ レイク ストレージ Gen 2 の標準クエリ ( ) を使用して`?sig=...`、共有アクセス (SAS) キーを提供します。 Kusto がリソースに制限された時間だけアクセスする必要がある場合は、このメソッドを使用します。



### <a name="azure-data-lake-store-gen-1"></a>Azure Data Lake Store Gen 1

このプロバイダーは、Azure Data Lake Store のファイルとディレクトリへのアクセスをサポートしています。
資格情報を指定する必要があります (Kusto は Azure データ レイクにアクセスするために独自の AAD プリンシパルを使用しません)。資格情報を提供する次の方法がサポートされています。

* URI`;impersonate`に追加します。 Kusto は、要求元のプリンシパル ID を使用し、リソースにアクセスするためにそれを偽装します。
* _AadToken `;token=`URI _to追加し _、AadToken_が Base 64 でエンコードされた AAD アクセス トークン (`https://management.azure.com/`トークンがリソース用であることを確認します) にします。
* URI`;prompt`に追加します。 Kusto は、リソースにアクセスする必要がある場合に、ユーザーの資格情報を要求します。 (ユーザーへの確認はクラウド展開では無効にされ、テスト環境でのみ有効になります)。



