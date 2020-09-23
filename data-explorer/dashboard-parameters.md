---
title: Azure Data Explorer ダッシュボードのパラメーター
description: ダッシュボード フィルターの構成要素としてパラメーターを使用します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 4e11ccd1c775fa2cae2e25f7cf1bd8b802a11567
ms.sourcegitcommit: 95527c793eb873f0135c4f0e9a2f661ca55305e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534002"
---
# <a name="use-parameters-in-azure-data-explorer-dashboards"></a>Azure Data Explorer ダッシュボードでパラメーターを使用する

パラメーターは、Azure Data Explorer ダッシュボードのダッシュボード フィルターの構成要素として使用されます。 これらはダッシュボード スコープで管理されます。これらをクエリに追加して、基になるビジュアルによって表示されるデータをフィルター処理することができます。 クエリでは、1 つ以上のパラメーターを使用できます。 このドキュメントでは、Azure Data Explorer ダッシュボードでパラメーターおよびリンクされたフィルターを作成および使用する方法について説明します。 

> [!NOTE]
> パラメーター管理は、ダッシュボード エディターで編集モードの状態で行えます。

## <a name="prerequisites"></a>前提条件

[Azure Data Explorer ダッシュボードを使用してデータを視覚化する](azure-data-explorer-dashboards.md)

## <a name="view-parameters-list"></a>パラメーターの一覧を表示する

ダッシュボードの上部にある **[パラメーター]** ボタンを選択すると、すべてのダッシュボード パラメーターの一覧が表示されます。

:::image type="content" source="media/dashboard-parameters/dashboard-icons.png" alt-text="ダッシュボードの上部にある [パラメーター] ボタン":::

## <a name="create-a-parameter"></a>パラメーターを作成する

パラメーターを作成するには、右側のペインの上部にある **[新しいパラメーター]** ボタンを選択します。

:::image type="content" source="media/dashboard-parameters/new-parameter-button.png" alt-text="[新しいパラメーター] ボタン":::

### <a name="properties"></a>Properties

1. **[パラメーターの追加]** ペインで、以下に詳細が示されているプロパティを構成します。

:::image type="content" source="media/dashboard-parameters/properties.png" alt-text="パラメーターのプロパティの追加":::

|フィールド  |説明 |
|---------|---------|
|**Parameter display name (パラメーター表示名)**    |   ダッシュボードまたは編集カードに表示されるパラメーターの名前。      |
|**パラメーターの種類**    |次のいずれかのパラメーター:<ul><li>**Single selection (単一選択)** :パラメーターの入力としてフィルターで値を 1 つのみ選択できます。</li><li>**Multiple selection (複数選択)** :パラメーターの入力としてフィルターで値を 1 つまたは複数選択できます。</li><li>**[時間範囲]** : 時間に基づいてクエリとダッシュボードをフィルター処理するための追加パラメーターを作成できます。 既定では、すべてのダッシュボードに時間範囲のピッカーがあります。</li><li>**Free text (自由記載)** : フィルターには値が設定されていません。 ユーザーは、テキスト フィールドに値を入力するか、値をコピーして貼り付けることができます。 フィルターでは、使用された最新の値が保持されます。</li></ul>    |
|**[変数名]**     |   クエリで使用されるパラメーターの名前。      |
|**データの種類**    |    パラメーター値のデータ型。     |
|**Pin as dashboard filter (ダッシュボード フィルターとしてピン留め)**   |   パラメーターベースのフィルターをダッシュボードにピン留めするか、ダッシュボードからピン留めを外します。       |
|**ソース**     |    パラメーター値のソース。 <ul><li>**Fixed values (固定値)** :手動で導入される静的なフィルター値。 </li><li>**Query**: KQL クエリを使用して動的に導入される値。  </li></ul>    |
|**Add a “Select all” value (「すべて選択」の値を追加)**    |   単一選択と複数選択のパラメーターの種類にのみ適用されます。 すべてのパラメーター値のデータを取得するために使用します。 この値は、その機能を提供するためにクエリに組み込む必要があります。 そのようなクエリ作成に関するその他の例については、「[複数選択のクエリベースのパラメーターを使用する](#use-the-multiple-selection-query-based-parameter)」を参照してください。     |

## <a name="manage-parameters-in-parameter-card"></a>パラメーター カードでパラメーターを管理する

パラメーター カードの 3 つのドット メニューで、 **[編集]** 、 **[Duplicate parameter]\(パラメーターを複製する\)** 、 **[削除]** 、または **[ダッシュボードへのピン留めを外す]** / **[ダッシュボードにピン留めする]** を選択します。

パラメーター カードでは、以下の表示を確認することができます。
* パラメーターの表示名 
* 変数名 
* パラメーターが使用されたクエリの数
* ダッシュボードでのピン留め/ピン留め解除の状態

:::image type="content" source="media/dashboard-parameters/modify-parameter.png" alt-text="パラメーターの変更":::

## <a name="use-parameters-in-your-query"></a>クエリでパラメーターを使用する

フィルターを対象のクエリ ビジュアルに適用できるようにするには、クエリでパラメーターを使用する必要があります。 定義が完了すると、 **[クエリ ]** ページのフィルターの上部バーとクエリの intellisense にパラメーターが表示されます。

:::image type="content" source="media/dashboard-parameters/query-intellisense.png" alt-text="上部バーと intellisense でのパラメーターの表示":::

> [!NOTE]
> クエリでパラメーターが使用されていない場合、フィルターは無効のままです。 クエリにパラメーターを追加すると、フィルターがアクティブになります。

## <a name="use-different-parameter-types"></a>異なるパラメーターの種類を使用する

いくつかのダッシュボード パラメーターの種類がサポートされています。 次の例では、さまざまなパラメーターの種類に対してクエリでパラメーターを使用する方法について説明します。 

### <a name="use-the-default-time-range-parameter"></a>既定の時間の範囲パラメーターを使用する

既定では、すべてのダッシュボードに "*時間の範囲*" パラメーターがあります。 これは、クエリで使用されている場合にのみ、ダッシュボードにフィルターとして表示されます。 次の例に示すように、クエリで既定の時間の範囲パラメーターを使用するには、パラメーター キーワード `_startTime` および `__endTime` を使用します。

```kusto
EventsAll
| where Repo.name has 'Microsoft'
| where CreatedAt between (_startTime.._endTime)
| summarize TotalEvents = count() by RepoName=tostring(Repo.name)
| top 5 by TotalEvents
```

保存すると、時間の範囲フィルターがダッシュボードに表示されます。 これで、カードでのデータのフィルター処理に使用できるようになります。 次のようにドロップダウンから選択して、ダッシュボードをフィルター処理できます。**時間の範囲** (過去 x 分/時間/日) または **カスタムの時間の範囲**。

:::image type="content" source="media/dashboard-parameters/time-range.png" alt-text="カスタムの時間の範囲を使用したフィルター処理":::

### <a name="use-the-single-selection-fixed-value-parameter"></a>単一選択の固定値パラメーターを使用する

固定値パラメーターは、ユーザーによって指定された定義済みの値に基づいています。 次の例では、単一選択の固定値パラメーターを作成する方法を示します。

#### <a name="create-the-parameter"></a>パラメーターを作成する

1. **[パラメーター]** を選択して **[パラメーター]** ペインを開き、 **[新しいパラメーター]** を選択します。

1. 詳細を次のように入力します。

    * **パラメーター表示名**:[会社]
    * **パラメーターの種類**:Single selection (単一選択)
    * **変数名**: `_company`
    * **データ型**:String
    * **Pin as dashboard filter (ダッシュボード フィルターとしてピン留め)** : オン
    * **ソース**:Fixed values (固定値)
    
    この例では、次の値を使用します。
    
    | 値      | パラメーターの表示名 |
    |------------|------------------------|
    | google/    | Google                 |
    | microsoft/ | Microsoft              |
    | facebook/  | Facebook               |
    | aws/       | AWS                    |
    | uber/      | Uber                   |
    
    * Add a **Select all** value (「すべて選択」の値を追加):オフ
    * 既定値:Microsoft

1. **[完了]** を選択してパラメーターを作成します。

パラメーターは **[パラメーター]** のサイド ペインに表示されますが、現在はどのビジュアルでも使用されていません。

:::image type="content" source="media/dashboard-parameters/start-end-side-pane.png" alt-text="サイド ペインの開始時刻と終了時刻のパラメーター":::

#### <a name="use-the-parameter"></a>パラメーターを使用する

1. `_company` 変数名を使用して、新しい *Company* パラメーターを用いてサンプル クエリを実行します。

    ```kusto
    EventsAll
    | where CreatedAt > ago(7d)
    | where Type == "WatchEvent"
    | where Repo.name has _company
    | summarize WatchEvents=count() by RepoName = tolower(tostring(Repo.name))
    | top 5 by WatchEvents
    ```

    新しいパラメーターは、ダッシュボードの上部にあるパラメーターの一覧に表示されます。

1. さまざまな値を選択して、ビジュアルを更新します。

    :::image type="content" source="media/dashboard-parameters/top-five-repos.png" alt-text="上位 5 つのリポジトリの結果":::

### <a name="use-the-multiple-selection-fixed-value-parameters"></a>複数選択の固定値パラメーターを使用する

固定値パラメーターは、ユーザーによって指定された定義済みの値に基づいています。 次の例では、複数選択の固定値パラメーターを作成して使用する方法を示します。

#### <a name="create-the-parameters"></a>パラメーターを作成する

1. **[パラメーター]** を選択して **[パラメーター]** ペインを開き、 **[新しいパラメーター]** を選択します。

1. 次の変更を加えて、「[単一選択の固定値パラメーターを使用する](#use-the-single-selection-fixed-value-parameter)」で説明されているように詳細を入力します。

    * **パラメーター表示名**:会社
    * **パラメーターの種類**:複数選択
    * **変数名**: `_companies`

1. **[完了]** をクリックしてパラメーターを作成します。

新しいパラメーターは **[パラメーター]** のサイド ペインに表示されますが、現在はどのビジュアルでも使用されていません。

:::image type="content" source="media/dashboard-parameters/companies-side-pane.png" alt-text="会社のサイド ペイン":::

#### <a name="use-the-parameters"></a>パラメーターを使用する 

1. `_companies` 変数を使用して、新しい *companies* パラメーターを用いてサンプル クエリを実行します。

    ```kusto
    EventsAll
    | where CreatedAt > ago(7d)
    | extend RepoName = tostring(Repo.name)
    | where Type == "WatchEvent"
    | where RepoName has_any (_companies)
    | summarize WatchEvents=count() by RepoName
    | top 5 by WatchEvents
    ```

    新しいパラメーターは、ダッシュボードの上部にあるパラメーターの一覧に表示されます。  

1. 1 つまたは複数の異なる値を選択して、ビジュアルを更新します。

    :::image type="content" source="media/dashboard-parameters/select-companies.png" alt-text="会社の選択":::

### <a name="use-the-single-selection-query-based-parameter"></a>単一選択のクエリベース パラメーターを使用する

クエリベースのパラメーター値は、パラメーターのクエリを実行することで、ダッシュボードの読み込み時に取得されます。 次の例では、単一選択のクエリベース パラメーターを作成して使用する方法を示します。

#### <a name="create-the-parameter"></a>パラメーターを作成する

1. **[パラメーター]** を選択して **[パラメーター]** ペインを開き、 **[新しいパラメーター]** を選択します。

2. 次の変更を加えて、「[単一選択の固定値パラメーターを使用する](#use-the-single-selection-fixed-value-parameter)」で説明されているように詳細を入力します。

    * **パラメーター表示名**:Event
    * **変数名**: `_event`
    * **ソース**:クエリ
    * **データ ソース**:GitHub
    * **[クエリの追加]** 選択し、次のクエリを入力します。 **[Done]** を選択します。

    ```kusto
    EventsAll
    | distinct Type
    | order by Type asc
    ```

    * **値**: Type
    * **表示名**:Type
    * **既定値**:WatchEvent

1. **[完了]** を選択してパラメーターを作成します。

#### <a name="use-a-parameter-in-the-query"></a>クエリでパラメーターを使用する

1. 新しい Event パラメーターを使用した次のサンプルクエリでは、`_ event` 変数が使用されます。

    ``` kusto
    EventsAll
    | where Type has (_event)
    | summarize count(Id) by Type, bin(CreatedAt,7d)
    ```

    新しいパラメーターは、ダッシュボードの上部にあるパラメーターの一覧に表示されます。 

1. さまざまな値を選択して、ビジュアルを更新します。

### <a name="use-the-multiple-selection-query-based-parameter"></a>複数選択のクエリベースのパラメーターを使用する

クエリベースのパラメーター値は、ユーザー指定のクエリを実行することで、ダッシュボードの読み込み時に取得されます。 次の例では、複数選択のクエリベースのパラメーターを作成する方法を示します。

#### <a name="create-a-parameter"></a>パラメーターを作成する

1. **[パラメーター]** を選択して **[パラメーター]** ペインを開き、 **[新しいパラメーター]** を選択します。

1. 次の変更を加えて、「[単一選択の固定値パラメーターを使用する](#use-the-single-selection-fixed-value-parameter)」で説明されているように詳細を入力します。

    * **パラメーター表示名**:events
    * **パラメーターの種類**:複数選択
    * **変数名**: `_events`

1. **[完了]** をクリックしてパラメーターを作成します。

#### <a name="use-parameters-in-the-query"></a>クエリでパラメーターを使用する

1. 次のサンプル クエリでは、`_events` 変数を使用して、新しい *Events* パラメーターを用います。

    ``` kusto
    EventsAll
    | where Type in (_event) or isempty(_events)
    | summarize count(Id) by Type, bin(CreatedAt,7d)
    ```

    > [!NOTE]
    > このサンプルでは、`isempty()` 関数で空の値をチェックすることによって、 **[すべて選択]** オプションを使用します。

    新しいパラメーターは、ダッシュボードの上部にあるパラメーターの一覧に表示されます。 

1. 1 つまたは複数の異なる値を選択して、ビジュアルを更新します。

### <a name="use-the-free-text-parameter"></a>自由記載パラメーターを使用する

自由記載パラメーターには値は含まれていません。 独自の値を取り込むことができます。

#### <a name="create-the-parameter"></a>パラメーターを作成する

1. **[パラメーター]** を選択して **[パラメーター]** ペインを開き、 **[新しいパラメーター]** を選択します。
1. 詳細を次のように入力します。
    * **パラメーター表示名**:[会社]
    * **パラメーターの種類**:自由記載
    * **変数名**: _company
    * **データ型**:String
    * **Pin as dashboard filter (ダッシュボード フィルターとしてピン留め)** : オン
    * **既定値**:既定値なし

#### <a name="use-parameters-in-the-query"></a>クエリでパラメーターを使用する

1. `_company` 変数名を使用して、新しい *Company* パラメーターを用いてサンプル クエリを実行します。

    ```kusto
    EventsAll
    | where CreatedAt > ago(7d)
    | where Type == "WatchEvent"
    | where Repo.name has _company
    | summarize WatchEvents=count() by RepoName = tolower(tostring(Repo.name))
    | top 5 by WatchEvents
    ```

新しいパラメーターが、ダッシュボードの上部にあるパラメーター一覧に表示されます。

## <a name="use-filter-search-for-single-and-multiple-selection-filters"></a>単一および複数選択フィルターにフィルター検索を使用する

単一および複数選択フィルターに、必要な値を入力します。 フィルター検索では、検索用語と一致する最近取得した値のみが表示されます。

## <a name="next-steps"></a>次の手順

* [ダッシュボードのビジュアルをカスタマイズする](dashboard-customize-visuals.md)
* [Azure Data Explorer でデータのクエリを実行する](web-query-data.md) 
