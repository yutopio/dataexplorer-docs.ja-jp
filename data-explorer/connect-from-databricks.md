---
title: Azure Databricks から Azure Data Explorer に接続する
description: このトピックでは、Azure Databricks を使用して Azure Data Explorer からデータにアクセスする方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/21/2020
ms.openlocfilehash: adbf974852f071dde54cc668b213e7b7d6d7cfea
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871946"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks"></a>Azure Databricks から Azure Data Explorer に接続する

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) は、Microsoft Azure プラットフォーム用に最適化された Apache Spark ベースの分析プラットフォームです。 この記事では、Azure Databricks を使用して Azure Data Explorer からデータにアクセスする方法について説明します。 デバイスのログインや Azure Active Directory (Azure AD) アプリを含め、Azure Data Explorer での認証方法はいくつかあります。
 
## <a name="prerequisites"></a>前提条件

- [Azure Data Explorer クラスターとデータベースを作成します](create-cluster-database-portal.md)。
- [Azure Databricks ワークスペースを作成します](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)。 **[Azure Databricks サービス]** の **[価格レベル]** ドロップダウン リストから **[Premium]** を選択します。 この選択により、Azure Databricks のシークレットを使用して資格情報を格納し、それらをノートブックとジョブで参照できます。

- 既定の設定を使用して Azure Databricks で[クラスターを作成](https://docs.azuredatabricks.net/user-guide/clusters/create.html)します。

 ## <a name="install-the-kusto-spark-connector-on-your-azure-databricks-cluster"></a>Azure Databricks クラスターに Kusto Spark コネクタをインストールする

Azure Databricks クラスターに [spark-kusto-connector](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector) をインストールするには、次のようにします。

1. Azure Databricks ワークスペースに移動し、[ライブラリを作成します](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)。
1. Maven Central で *spark-kusto connector* パッケージを検索し、最新バージョンをインストールして、クラスターにアタッチします。 

## <a name="connect-to-azure-data-explorer-by-using-a-device-authentication"></a>デバイス認証を使用して Azure Data Explorer に接続する

[サンプル コード](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/pyKusto.py)。

## <a name="connect-to-azure-data-explorer-by-using-an-azure-ad-app"></a>Azure AD アプリを使用して Azure Data Explorer に接続する

1. [Azure AD アプリをプロビジョニングする](kusto/management/access-control/how-to-provision-aad-app.md)ことによって、Azure AD アプリを作成します。
1. 次のように、Azure Data Explorer データベース上の Azure AD アプリへのアクセスを許可します。

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```

    | パラメーター | 説明 |
    | - | - |
    | `DB Name` | データベース名 |
    | `AAD App ID` | Azure AD アプリ ID |
    | `AAD Tenant ID` | Azure AD テナント ID |

### <a name="find-your-azure-ad-tenant-id"></a>Azure AD テナント ID を見つける

Azure Data Explorer では、アプリケーションを認証するために Azure AD テナント ID が使用されます。 テナント ID を見つけるには、次の URL を使用します。 *YourDomain* をご利用のドメインに置き換えてください。

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

たとえば、ドメインが *contoso.com* の場合、URL は [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/) になります。 結果を表示するには、この URL を選択します。 最初の行は次のようになります。 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

テナント ID は `6babcaad-604b-40ac-a9d7-9fd97c0b779f` です。 

### <a name="store-and-secure-your-azure-ad-app-id-and-key-optional"></a>Azure AD のアプリ ID とキーを格納してセキュリティで保護する (任意)  

次のように、Azure Databricks の[シークレット](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)を使用して、Azure AD アプリの ID とキーを格納し、セキュリティで保護します。

1. [CLI を設定します](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)。
1. [CLI をインストールします](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)。 
1. [認証を設定します](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication)。
1. 次のサンプル コマンドを使用して、[シークレット](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)を構成します。

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="sample-code"></a>サンプル コード

1. [サンプル コード](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/pyKusto.py)。 
1. プレースホルダーの値は、ご利用のクラスター名、データベース名、テーブル名、Azure AD テナント ID、AAD アプリ ID、および AAD アプリ キーで更新してください。 資格情報を Databricks シークレット ストアに格納する場合は、dbutils から値を取得するようにコードを更新してください。
