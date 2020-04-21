---
title: 要求のプロパティ, クライアント要求プロパティ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの要求プロパティ 、クライアント要求プロパティについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/23/2019
ms.openlocfilehash: 0aa496e6011fce98db5a304b9748f27f07590b4f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524329"
---
# <a name="request-properties-clientrequestproperties"></a>要求プロパティ,クライアントリクエストプロパティ

.NET SDK を使用して Kusto から要求を行う場合、通常は次の値が提供されます。

1. 接続先のサービス エンドポイント、認証パラメータ、および類似の接続関連情報を示す接続文字列。 プログラムによって、接続文字列はクラスを`KustoConnectionStringBuilder`通じて表されます。

2. 要求の 「スコープ」を記述するために使用されるデータベースの名前。

3. 要求 (クエリまたはコマンド) 自体のテキスト。

4. クライアントがサービスを提供し、要求に適用される追加のプロパティ。 プログラムによって、これらのプロパティは`ClientRequestProperties`というクラスによって保持されます。

クライアント要求のプロパティには多くの用途があります。 一部のカテゴリは、デバッグを容易にするために使用されます (たとえば、クライアント/サービスの対話を追跡するために使用できる相関文字列を提供する)、その他のプロパティの 3 番目のカテゴリは[クエリ パラメーター](../../query/queryparametersstatement.md)です。 サポートされているプロパティの完全な一覧がこのページに表示されます。

この`Kusto.Data.Common.ClientRequestProperties`クラスには、次の 3 種類のデータが格納されます。

* 名前付きプロパティ。

* オプション (オプション名とオプション値のマッピング)。

* パラメーター (クエリ パラメーター名からクエリ パラメーター値へのマッピング)。

> [!NOTE]
> 名前付きプロパティの中には、「使用しない」とマークされているものもあります。 このようなプロパティは、クライアントによって指定され、サービスに影響を与えるべきではありません。

## <a name="the-clientrequestid-x-ms-client-request-id-named-property"></a>名前付きプロパティ (x-ms クライアント要求 ID)

この名前付きプロパティは、クライアントが指定した要求の ID を保持します。 クライアントが送信する要求ごとに一意の要求ごとの値を指定することを強くお勧めします。

このプロパティのプログラム名は`ClientRequestId`、 で HTTP ヘッダー`x-ms-client-request-id`に変換されます。

クライアントが独自の値を指定しない場合、このプロパティは SDK によって (ランダム) 値に設定されます。

このプロパティの内容は、GUID などの印刷可能な一意の文字列です。
ただし、クライアントは次のテンプレートを使用することをお勧めします。

*アプリケーション名*`.`*アクティビティ名*`;`*一意の ID*

*ApplicationName*は要求を行うクライアント アプリケーションを識別し *、ActivityName*はクライアント アプリケーションがクライアント要求を発行しているアクティビティの種類を識別し *、UniqueId*は特定の要求を識別します。

## <a name="the-application-x-ms-app-named-property"></a>アプリケーション (x-ms-app) という名前のプロパティ

この名前付きプロパティは、トレースのために、要求を行うクライアント アプリケーションの名前を保持します。

このプロパティのプログラム名は`Application`、 で HTTP ヘッダー`x-ms-app`に変換されます。 これは、Kusto 接続文字列で として指定できます`Application Name for Tracing`。

クライアントが独自の値を指定しない場合、このプロパティは SDK をホストするプロセスの名前に設定されます。

## <a name="the-user-x-ms-user-named-property"></a>ユーザー (x-ms-user) という名前のプロパティ

この名前付きプロパティは、トレースのために、要求を行うユーザーの ID を保持します。

このプロパティのプログラム名は`User`、 で HTTP ヘッダー`x-ms-user`に変換されます。 これは、Kusto 接続文字列で として指定できます`User Name for Tracing`。

## <a name="controlling-request-properties-using-the-rest-api"></a>REST API を使用した要求プロパティーの制御

Kusto サービスに HTTP 要求を発行する場合`properties`は、POST 要求本文である JSON ドキュメント内のスロットを使用して、要求プロパティを提供します。 HTTP ヘッダーでは、一部のプロパティ (クライアントが要求を識別するためにサービスに提供する相関 ID である "クライアント要求 ID" など) を指定できるため、HTTP GET を使用する場合にも設定できます。
詳細については[、Kusto REST API リクエストオブジェクト](../rest/request.md)を参照してください。

## <a name="providing-values-for-query-parametrization-as-request-properties"></a>要求プロパティとしてのクエリ パラメータ化の値の提供

Kusto クエリは、クエリ テキスト内の特殊な宣言クエリ パラメーター ステートメントを使用して[、クエリ パラメーター](../../query/queryparametersstatement.md)を参照できます。 これにより、クライアント アプリケーションは、ユーザー入力に基づいて Kusto クエリを安全な方法で (インジェクション攻撃を恐れずに) パラメーター化できます。

プログラムを使用して、 、 、`ClearParameter``SetParameter`および メソッドを`HasParameter`使用してプロパティ値を設定できます。

REST API では、クエリ パラメーターは、他の要求プロパティと同じ JSON エンコードされた文字列に表示されます。

## <a name="sample-code-for-using-request-properties"></a>要求プロパティを使用するサンプル コード

クライアント コードの例を次に示します。

```csharp
public static System.Data.IDataReader QueryKusto(
    Kusto.Data.Common.ICslQueryProvider queryProvider,
    string databaseName,
    string query)
{
    var queryParameters = new Dictionary<String, String>()
    {
        { "xIntValue", "111" },
        { "xStrValue", "abc" },
        { "xDoubleValue", "11.1" }
    };

    // Query parameters (and many other properties) are provided
    // by a ClientRequestProperties object handed alongside
    // the query:
    var clientRequestProperties = new Kusto.Data.Common.ClientRequestProperties(
        principalIdentity: null,
        options: null,
        parameters: queryParameters);

    // Having client code provide its own ClientRequestId is
    // highly recommended. It not only allows the caller to
    // cancel the query, but also makes it possible for the Kusto
    // team to investigate query failures end-to-end:
    clientRequestProperties.ClientRequestId
        = "MyApp.MyActivity;"
        + Guid.NewGuid().ToString();

    // This is an example for setting an option
    // ("notruncation", in this case). In most cases this is not
    // needed, but it's included here for completeness:
    clientRequestProperties.SetOption(
        Kusto.Data.Common.ClientRequestProperties.OptionNoTruncation,
        true);
 
    try
    {
        return queryProvider.ExecuteQuery(query, clientRequestProperties);
    }
    catch (Exception ex)
    {
        Console.WriteLine(
            "Failed invoking query '{0}' against Kusto."
            + " To have the Kusto team investigate this failure,"
            + " please open a ticket @ https://aka.ms/kustosupport,"
            + " and provide: ClientRequestId={1}",
            query, clientRequestProperties.ClientRequestId);
        return null;
    }
}
```

## <a name="list-of-clientrequestproperties"></a>クライアント要求プロパティの一覧

<!-- The following is auto-generated by running the following command: -->
<!-- Kusto.Cli.exe -execute:"#crp -doc"                                -->

<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

* `debug_query_externaldata_projection_fusion_disabled`(*オプションデバッグクエリ無効外部データプロジェクション*): 設定されている場合は、外部データ演算子に投影を融合しないでください。 [ブール値]
* `debug_query_fanout_threads_percent_external_data`( 外部データ*ノード*に対して実行をファンアウトするスレッドの割合。 [Int]
* `deferpartialqueryfailures`(*オプション遅延PartialQueryFailures*): true の場合、結果セットの一部として部分的なクエリ エラーの報告を無効にします。 [ブール値]
* `max_memory_consumption_per_query_per_node`(*オプションマックスメモリ消費PerQueryPerNode*): クエリ全体がノードごとに割り当て可能なメモリの既定の最大量を上書きします。 [UInt64]
* `maxmemoryconsumptionperiterator`(*オプションマックスメモリ消費PerIterator*): クエリ演算子が割り当て可能なメモリの既定の最大量を上書きします。 [UInt64]
* `maxoutputcolumns`(*オプション最大出力列*): クエリで生成できる既定の最大列数をオーバーライドします。 [長い]
* `norequesttimeout`(*オプション NoRequestTimeout*): 要求タイムアウトを最大値に設定できるようにします。 [ブール値]
* `notruncation`(*OptionNoTruncation*): 呼び出し元に返されるクエリ結果の切り捨てを抑制します。 [ブール値]
* `push_selection_through_aggregation`(*オプションプッシュ選択スルーアグリゲーション*): true の場合は、集計を使用して単純な選択をプッシュする [ブール]
* `query_admin_super_slacker_mode`(*オプション管理者スーパースラッカーモード*): true の場合、クエリの実行を別のノードに委任する [ブール]
* `query_bin_auto_at`(*クエリビンオートアット*): bin_auto() 関数を評価する際に、使用する開始値。 [リテラル式]
* `query_bin_auto_size`(*クエリビンオートサイズ*): bin_auto() 関数を評価する際に使用するビン サイズの値。 [リテラル式]
* `query_cursor_after_default`(*オプションクエリカーソル後のデフォルト*): パラメータなしで呼び出された場合の cursor_after() 関数のデフォルトパラメータ値。 [string]
* `query_cursor_before_or_at_default`(*オプションクエリカーソル BeforeOrAtDefault*): パラメータなしで呼び出された場合のcursor_before_or_at() 関数のデフォルトパラメータ値。 [string]
* `query_cursor_current`(*オプションクエリカーソルカレント*): cursor_current() 関数または current_cursor() 関数によって返されるカーソル値をオーバーライドします。 [string]
* `query_cursor_scoped_tables`(*オプションクエリカーソルスコープテーブル*): cursor_after_default にスコープを設定する必要があるテーブル名の一覧。 cursor_before_or_at_default (上限はオプション)。 [ダイナミック]
* `query_datascope`(*OptionQueryDataScope*): クエリのデータスコープを制御します。 ['デフォルト'、'すべて'、または'ホットキャッシュ']
* `query_datetimescope_column`(*オプションクエリ日付時間スコープカラム*): クエリの日時範囲の列名を制御します (query_datetimescope_to / query_datetimescope_from)。 [文字列]
* `query_datetimescope_from`(*オプションQueryDateTimeScopeFrom*): クエリの日時範囲 (最も早い) を制御します -- query_datetimescope_column (定義されている場合) にのみ自動適用フィルタとして使用されます。 [DateTime]
* `query_datetimescope_to`(*オプションQueryDateTimeScopeTo*): クエリの日時範囲 (最新) を制御します -- query_datetimescope_column (定義されている場合) にのみ自動適用フィルタとして使用されます。 [DateTime]
* `query_distribution_nodes_span`(*オプションクエリ分布ノードスパンサイズ*): 設定されている場合、サブクエリのマージの動作を制御します。サブグループのサイズはこのオプションで設定されます。 [Int]
* `query_fanout_nodes_percent`(*オプションクエリファンアウトノードパーセンテージ*): ファンアウト実行のノードの割合。 [Int]
* `query_fanout_threads_percent`(*ファンアウトスレッド数 ):* 実行をファンアウトするスレッドの割合。 [Int]
* `query_language`(*オプションクエリ言語*): クエリ テキストの解釈方法を制御します。 ['csl'、'kql'または'sql']
* `query_max_entities_in_union`(*オプションマックスエンティティToUnion*): クエリで生成できる既定の最大列数をオーバーライドします。 [長い]
* `query_now`(*オプションクエリナウ*): now(0s) 関数によって返される日時値をオーバーライドします。 [DateTime]
* `query_python_debug`(*オプションデバッグPython):* 設定されている場合は、列挙されたPythonノードのPythonデバッグクエリを生成します(デフォルトが最初)。 [ブール値または Int]
* `query_results_apply_getschema`(*オプションクエリ結果適用GetSchema*): 設定されている場合、データ自体ではなく、クエリの結果の各表形式データのスキーマを取得します。 [ブール値]
* `query_results_cache_max_age`(*正*の値の場合は、Kusto が返すキャッシュされたクエリ結果の最大保存期間を制御します。
* `query_results_progressive_row_count`(*オプションプログレッシブクエリ最小実行カウントパー更新*): 各更新で送信するレコードの数に関する Kusto のヒント (オプション結果プログレッシブが設定されている場合にのみ有効になります)
* `query_results_progressive_update_period`(*オプションプログレッシブ進行報告期間*): 進行状況フレームを送信する頻度に関する Kusto のヒント (オプション結果プログレッシブ有効が設定されている場合にのみ有効になります)
* `query_shuffle_broadcast_join`(*シャッフルブロードキャスト参加*): ブロードキャスト参加を使用してシャッフルを有効にします。
* `query_take_max_records`(*オプションテイクマックスレコード*): クエリ結果をこのレコード数に制限できます。 [長い]
* `queryconsistency`(*オプションクエリの整合性*): クエリの一貫性を制御します。 [強い矛盾』または「正常な矛盾」または「弱い矛盾」]
* `request_callout_disabled`(*オプションリクエストコールアウト無効*): 指定した場合、要求がユーザー提供のサービスにコールアウトできないことを示します。 [ブール値]
* `request_description`(*OptionRequestDescription*): 要求の作成者が要求の説明として含める任意のテキスト。 [文字列]
* `request_external_table_disabled`(*オプション要求外部テーブル無効*): 指定されている場合、要求が外部テーブル内のコードを呼び出すことができないことを示します。 [ブール値]
* `request_readonly`(*オプションRequestReadOnly*): 指定した場合、要求が何も書き込むことができないことを示します。 [ブール値]
* `request_remote_entities_disabled`(*オプション要求リモートエンティティ無効*): 指定すると、要求がリモート データベースおよびクラスターにアクセスできないことを示します。 [ブール値]
* `request_sandboxed_execution_disabled`(*オプションリクエストサンドボックス実行無効*): 指定した場合、要求がサンドボックス内のコードを呼び出すことができないことを示します。 [ブール値]
* `results_progressive_enabled`(*オプション結果プログレッシブ有効化*): 設定されている場合は、プログレッシブ クエリ ストリームを有効にします。
* `servertimeout`(*オプションサーバータイムアウト*): デフォルトの要求タイムアウトをオーバーライドします。 [タイムスパン]
* `truncationmaxrecords`(*オプション切り捨てMaxRecords*): クエリが呼び出し元に返すレコードの既定の最大数を上書きします (切り捨て)。 [長い]
* `truncationmaxsize`(*オプションTruncationMaxSize*): クエリが呼び出し元に返す最大データ サイズ (切り捨て) をオーバーライドします。 [長い]
* `validate_permissions`(*オプション検証権限*): クエリを実行するためのユーザーのアクセス許可を検証し、クエリ自体を実行しません。 [ブール値]

