---
title: クエリ パラメーター宣言ステートメント - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクエリ パラメーター宣言ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b7193ada6967882306d9a977b6c90af8b247036d
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765499"
---
# <a name="query-parameters-declaration-statement"></a>クエリ パラメーター宣言ステートメント

::: zone pivot="azuredataexplorer"

Kusto に送信されるクエリには、クエリ テキスト自体に加えて、**クエリ パラメータ**と呼ばれる名前と値のペアのセットが含まれる場合があります。 クエリ テキストは、クエリ パラメーター**宣言ステートメント**を使用して名前と型を指定することで、1 つ以上のクエリ パラメーター値を参照できます。

クエリ パラメーターには、主に次の 2 つの用途があります。

1. **注射攻撃**に対する防御機構として.
2. クエリをパラメーター化する方法として。

特に、Kusto に送信するクエリでユーザーが入力した入力を組み合わせたクライアント アプリケーションは、このメカニズムを使用して、KUsto と同等の[SQL インジェクション](https://en.wikipedia.org/wiki/SQL_injection)攻撃から保護する必要があります。

## <a name="declaring-query-parameters"></a>クエリ パラメーターの宣言

クエリ パラメーターを参照できるようにするには、クエリ テキスト (またはクエリ を使用する関数) が、使用するクエリ パラメーターを最初に宣言する必要があります。 各パラメーターに対して、宣言は名前とスカラー型を提供します。 必要に応じて、要求がパラメーターの具象値を提供しない場合に使用される既定値をパラメーターに設定することもできます。 Kusto は、その型の通常の解析規則に従ってクエリ パラメーターの値を解析します。

**構文**

`declare``query_parameters`*Name1*`:``,``=`*DefaultValue1**Type1*名前 1 タイプ1 [ デフォルト値1 ] [ ..] `(``);`

* *Name1*: クエリで使用されるクエリ パラメータの名前。
* *タイプ1*: 対応するタイプ(例えば`string`、、、`datetime`など)ユーザーが提供する値は文字列としてエンコードされ、Kusto は、厳密に型指定された値を取得するためにクエリ パラメーターに適切な解析メソッドを適用します。
* *既定値 1*: パラメーターの省略可能な既定値。 これは、適切なスカラー型のリテラルである必要があります。

> [!NOTE]
> [ユーザー定義関数](functions/user-defined-functions.md)と同様に、型`dynamic`のクエリ パラメーターに既定値を設定することはできません。

**使用例**

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>クライアント アプリケーションでのクエリ パラメーターの指定

クエリ パラメーターの名前と値は、クエリ`string`を作成するアプリケーションによって値として提供されます。 名前を繰り返す必要はありません。

値の解釈は、クエリ パラメーター宣言ステートメントに従って行われます。 すべての値は、クエリ パラメーター宣言ステートメントで指定された型に従って、クエリ本体のリテラルであるかのように解析されます。

### <a name="rest-api"></a>REST API

クエリ パラメータは、クライアント アプリケーションが`properties`要求本文の JSON オブジェクトのスロットを通じて、 というネストされた`Parameters`プロパティ バッグ内で提供されます。 たとえば、一部のユーザーの年齢を計算する KUsto への REST API 呼び出しの本文を次に示します (アプリケーションからユーザーに誕生日を求める場合があります)。

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>クスト .NET SDK

Kusto .NET クライアント ライブラリを使用するときにクエリ パラメータの名前と値を指定するには、オブジェクトの新`ClientRequestProperties`しいインスタンスを作成し`HasParameter`、 `SetParameter`、`ClearParameter`および メソッドを使用してクエリ パラメータを操作します。 このクラスには、厳密に型指定されたオーバーロードが多数用意されています`SetParameter`。内部的には、クエリ言語の適切なリテラルを生成し、上記の説明に`string`従って REST API を介して、それをとして送信します。 クエリ テキスト自体は、[クエリ パラメータを宣言する](#declaring-query-parameters)必要があります。

### <a name="kustoexplorer"></a>Kusto.Explorer

サービスへのリクエストを行うときに送信されるクエリパラメータを設定するには、**クエリパラメータ**の"レンチ"アイコン`ALT` + `P`()を使用します。

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
