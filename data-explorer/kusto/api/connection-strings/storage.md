---
title: ストレージ接続文字列-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのストレージ接続文字列について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 0456ad7115c51bcdc51b0db82bc9f9b88953be32
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226212"
---
# <a name="storage-connection-strings"></a>ストレージの接続文字列

いくつかの Kusto コマンドにより、Kusto が外部ストレージサービスと対話するように指示します。 たとえば、Kusto は Azure Storage Blob にデータをエクスポートするように指示できます。この場合、特定のパラメーター (ストレージアカウント名や Blob コンテナーなど) を指定する必要があります。

Kusto は、次の記憶域プロバイダーをサポートしています。


* 記憶域プロバイダーの Azure Storage Blob
* 記憶域プロバイダーの Azure Data Lake Storage

記憶域プロバイダーの種類ごとに、ストレージリソースとそのアクセス方法を説明するために使用される接続文字列形式を定義します。
Kusto は、これらのストレージリソースと、それらにアクセスするために必要なプロパティ (セキュリティ資格情報など) を記述するために URI 形式を使用します。


|プロバイダー                   |Scheme    |URI テンプレート                          |
|---------------------------|----------|--------------------------------------|
|Azure Storage BLOB         |`https://`|`https://`*アカウント* `.blob.core.windows.net/`*コンテナー*[ `/` *blobname*] [ `?` *SasKey* \| `;` *AccountKey*]|
|Azure Data Lake Store Gen 2|`abfss://`|`abfss://`*ファイルシステム* `@`*アカウント* `.dfs.core.windows.net/`*PathToDirectoryOrFile*[ `;` *callercredentials*]|
|Azure Data Lake Store Gen 1|`adl://`  |`adl://`*Account*Azuredatalakestore.net/*PathToDirectoryOrFile*[ `;` *callercredentials*]|

## <a name="azure-storage-blob"></a>Azure Storage BLOB

このプロバイダーは最も一般的に使用され、すべてのシナリオでサポートされています。
リソースにアクセスするときは、プロバイダーに資格情報を指定する必要があります。 資格情報を提供するには、次の2つのメカニズムがサポートされています。

* Azure Storage Blob の標準クエリ () を使用して、共有アクセス (SAS) キーを指定し `?sig=...` ます。 このメソッドは、Kusto がリソースにアクセスする必要がある時間に制限されている場合に使用します。
* ストレージアカウントキー () を指定 `;ljkAkl...==` します。 このメソッドは、Kusto が継続的にリソースにアクセスする必要がある場合に使用します。

例 (これは難読化された文字列リテラルを示しているため、アカウントキーまたは SAS を公開しないことに注意してください)。

`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv;<storage_account_key_text, ends with '=='>"`
`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv?sv=...&sp=rwd"` 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="azure-data-lake-store-gen-2"></a>Azure Data Lake Store Gen 2

このプロバイダーは Azure Data Lake Store Gen 2 のデータへのアクセスをサポートしています。

URI の形式は次のとおりです。

`abfss://`*ファイルシステム* `@`*Storageaccountname* `.dfs.core.windows.net/`*パス* `;`*Callercredentials*

各値の説明:

* _Filesystem_は、adls ファイルシステムオブジェクトの名前です (Blob コンテナーとほぼ同等)。
* _Storageaccountname_はストレージアカウントの名前です
* _パス_は、スラッシュ ( `/` ) 文字を区切り記号として使用されるディレクトリまたはファイルへのパスです。
* _Callercredentials_は、次に示すように、サービスへのアクセスに使用される資格情報を示します。

Azure Data Lake Store Gen 2 にアクセスする場合、呼び出し元はサービスにアクセスするための有効な資格情報を提供する必要があります。 資格情報を提供する次の方法がサポートされています。

* `;sharedkey=` *ACCOUNTKEY*を URI に追加し、 _AccountKey_をストレージアカウントキーにします。
* `;impersonate`を URI に追加します。 Kusto は、要求元のプリンシパル id を使用して、リソースにアクセスするために偽装します。 プリンシパルは、[ここ](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)に記載されているように、読み取り/書き込み操作を実行できるように、適切な RBAC ロールの割り当てを持っている必要があります。 (たとえば、読み取り操作の最小ロールは `Storage Blob Data Reader` ロールです)。
* `;token=` *AADTOKEN*を URI に追加します。 _AadToken_は base 64 でエンコードされた AAD アクセストークンです (リソースのトークンであることを確認してください `https://storage.azure.com/` )。
* `;prompt`を URI に追加します。 Kusto は、リソースにアクセスする必要があるときにユーザー資格情報を要求します。 (ユーザーへのクラウド展開は無効になっており、テスト環境でのみ有効になっています)。
* Azure Data Lake Storage Gen 2 の標準クエリ () を使用して、共有アクセス (SAS) キーを指定し `?sig=...` ます。 このメソッドは、Kusto がリソースにアクセスする必要がある時間に制限されている場合に使用します。



### <a name="azure-data-lake-store-gen-1"></a>Azure Data Lake Store Gen 1

このプロバイダーは、Azure Data Lake Store 内のファイルとディレクトリへのアクセスをサポートしています。
資格情報を使用して指定する必要があります (Kusto は、Azure Data Lake にアクセスするために独自の AAD プリンシパルを使用しません)。資格情報を提供する次の方法がサポートされています。

* `;impersonate`を URI に追加します。 Kusto は、要求元のプリンシパル id を使用し、それを偽装してリソースにアクセスします。
* `;token=` *AADTOKEN*を URI に追加します。 *AadToken*は base 64 でエンコードされた AAD アクセストークンです (リソースのトークンであることを確認してください `https://management.azure.com/` )。
* `;prompt`を URI に追加します。 Kusto は、リソースにアクセスする必要があるときにユーザー資格情報を要求します。 (ユーザーへのクラウド展開は無効になっており、テスト環境でのみ有効になっています)。



