---
title: クエリパラメーター宣言ステートメント-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのクエリパラメーターの宣言ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: a76755d04179b3d311e79798162c61db764455d7
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737812"
---
# <a name="query-parameters-declaration-statement"></a>クエリパラメーター宣言ステートメント

::: zone pivot="azuredataexplorer"

Kusto に送信されるクエリには、クエリテキスト自体に加えて、**クエリパラメーター**と呼ばれる一連の名前と値のペアが含まれる場合があります。 クエリテキストは、クエリ**パラメーター宣言ステートメント**を使用して名前と型を指定することで、1つ以上のクエリパラメーターの値を参照できます。

クエリパラメーターには、主に次の2つの用途があります。

1. **インジェクション攻撃**に対する保護メカニズムとして。
2. クエリをパラメーター化する方法。

特に、ユーザーが入力した入力をクエリで組み合わせて Kusto に送信するクライアントアプリケーションは、このメカニズムを使用して、Kusto と同等の[SQL インジェクション](https://en.wikipedia.org/wiki/SQL_injection)攻撃から保護する必要があります。

## <a name="declaring-query-parameters"></a>クエリパラメーターの宣言

クエリパラメーターを参照できるようにするには、クエリテキスト (またはそれが使用する関数) で、最初に使用するクエリパラメーターを宣言する必要があります。 パラメーターごとに、宣言によって名前とスカラー型が提供されます。 必要に応じて、パラメーターに具体的な値が指定されていない場合に、パラメーターに既定値を使用することもできます。 その後、kusto は、その型の通常の解析規則に従って、クエリパラメーターの値を解析します。

**構文**

`declare``query_parameters` `:` *Type1* `,` *Name1* `=` Name1 Type1 [DefaultValue1] [...] *DefaultValue1* `(``);`

* *Name1*: クエリで使用されるクエリパラメーターの名前。
* *Type1*: 対応する型 ( `string` `datetime`たとえば、、など)ユーザーによって指定された値は文字列としてエンコードされます。 Kusto は、適切な解析メソッドをクエリパラメーターに適用して、厳密に型指定された値を取得します。
* *DefaultValue1*: パラメーターの省略可能な既定値。 これは、適切なスカラー型のリテラルである必要があります。

> [!NOTE]
> [ユーザー定義関数](functions/user-defined-functions.md)と同様に、型`dynamic`のクエリパラメーターに既定値を指定することはできません。

**使用例**

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>クライアントアプリケーションでのクエリパラメーターの指定

クエリパラメーターの名前と値は、クエリを`string`作成するアプリケーションによって値として指定されます。 名前を繰り返すことはできません。

値の解釈は、クエリパラメーターの宣言ステートメントに従って行われます。 すべての値は、クエリパラメーター宣言ステートメントによって指定された型に基づいて、クエリ本体のリテラルであるかのように解析されます。

### <a name="rest-api"></a>REST API

クエリパラメーターは、という`properties` `Parameters`入れ子になったプロパティバッグで、要求本文の JSON オブジェクトのスロットを通じてクライアントアプリケーションによって提供されます。 たとえば、一部のユーザーの年齢を計算する Kusto への REST API 呼び出しの本文は次のようになります (アプリケーションがユーザーに誕生日を要求した場合など)。

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>Kusto .NET SDK

Kusto .net `ClientRequestProperties`クライアントライブラリを使用するときにクエリパラメーターの名前と値を指定するために、オブジェクトの新しいインスタンスを作成し、 `HasParameter`、 `SetParameter`、の`ClearParameter`各メソッドを使用してクエリパラメーターを操作します。 このクラスは、に対して`SetParameter`厳密に型指定された複数のオーバーロードを提供することに注意してください。内部的には、前に説明したように、クエリ言語の`string`適切なリテラルを生成し、REST API を通じてとして送信します。 クエリテキスト自体は、[クエリパラメーターを宣言](#declaring-query-parameters)する必要があります。

### <a name="kustoexplorer"></a>Kusto.Explorer

サービスへの要求を行うときに送信されるクエリパラメーターを設定するには、**クエリパラメーター** "レンチ`ALT` + `P`" アイコン () を使用します。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
