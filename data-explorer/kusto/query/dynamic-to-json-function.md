---
title: dynamic_to_json ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの dynamic_to_json () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 08/05/2020
ms.openlocfilehash: 010610eb8cf6727c752f04564e8b3d1f71a405ab
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502209"
---
# <a name="dynamic_to_json"></a>dynamic_to_json ()

`dynamic`入力を文字列形式に変換します。
入力がプロパティバッグの場合、出力文字列はキーによって並べ替えられた内容を再帰的に出力します。 それ以外の場合、出力は関数の出力に似てい `tostring` ます。

## <a name="syntax"></a>構文

`dynamic_to_json(Expr)`

## <a name="arguments"></a>引数

* *Expr*: `dynamic` 入力。 関数は、1つの引数を受け取ります。

## <a name="returns"></a>戻り値

入力の文字列形式を返し `dynamic` ます。 入力がプロパティバッグの場合、出力文字列はキーによって並べ替えられた内容を再帰的に出力します。

## <a name="examples"></a>例

式:

  let bag1 = dynamic_to_json (動的 ({' Y10 ':d ynamic ({}), ' X8 ': 動的 ({' c3 ': 1, ' {0} ': 5, ' a4 ': 6}), d 1 ': 114, ' A1 ':12, ' B1 ': 2, ' C1 ': 3, ' A14 ': [15, 13, 18]});印刷 bag1
  
結果:

"{" "A1" ":12," "A14" ": [15, 13, 18]" "" B1 "": "" "(" "Y10" ": 114}" のように "") "と" "" を "" "と" ""、"" "と" "" "と" "" ("" ")" {}

式:

 let bag2 = dynamic_to_json (動的 ({' X8 ': 動的 ({' a4 ': 6, ' c3 ': 1,% ': 5}), ' A14 ': [15, 13, 18], ' C1 ': 3, ' B1 ': 2, ' Y10 ': dynamic ({}), ' A1 ':12, d 1 ': 114});印刷 bag2
 
結果:

{"A1":12, "A14": [15, 13, 18], "B1": 2, "C1": 3, "D1": 114, "X8": {"a4": 6, "c3": 1, "d8": 5}, "Y10": {} }
