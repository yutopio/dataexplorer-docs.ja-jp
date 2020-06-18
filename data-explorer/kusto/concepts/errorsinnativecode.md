---
title: ネイティブコードのエラー-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのネイティブコードのエラーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: 5446b98283a6533ceacd1c84fa3043732fe79e6a
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2020
ms.locfileid: "84942728"
---
# <a name="errors-in-native-code"></a>ネイティブ コードのエラー

Kusto エンジンはネイティブコードで記述され、負の値を使用してエラーを報告し `HRESULT` ます。 これらのエラーは通常、プログラムの API では認識されませんが、発生する可能性があります。 たとえば、操作エラーによって " `Exception from HRESULT:` *HRESULT-CODE*" という状態が表示される場合があります。

Kusto ネイティブのエラーコードは、Windows マクロを使用して `MAKE-HRESULT` 次のように定義されます。

* 重大度をに設定`1`
* ファシリティをに設定`0xDA`
  
次のエラーコードが定義されています。

|マニフェスト定数                  |コード |値        |意味                                                                                                        |
|-----------------------------------|-----|-------------|---------------------------------------------------------------------------------------------------------------|
|`E_EXTENT_LOAD_FAILED`             | `0`  |`0x80DA0000`|データシャードを読み込めませんでした                                                                                  |
|`E_RUNAWAY_QUERY`                  | `1`  |`0x80DA0001`|許可されたリソースを超えたため、クエリの実行が中止されました                                                   |
|`E_STREAM_FORMAT`                  | `2`  |`0x80DA0002`|データストリームは形式が正しくないため、解析できません                                    |
|`E_QUERY_RESULT_SET_TOO_LARGE`     | `3`  |`0x80DA0003`|このクエリの結果セットは、許可されているレコード/サイズの制限を超えています                                            |
|`E_STREAM_ENCODING_VERSION`        | `4`  |`0x80DA0004`|バージョンが不明なため、結果ストリームを解析できません                                                   |
|`E_KVDB_ERROR`                     | `5`  |`0x80DA0005`|キー/値データベースの操作を実行できませんでした                                                              |
|`E_QUERY_CANCELLED`                | `6`  |`0x80DA0006`|クエリが取り消されました                 |
|`E_LOW_MEMORY_CONDITION`           | `7`  |`0x80DA0007`|使用可能なプロセスメモリが不足しているため、操作が中止されました                                              |
|`E_WRONG_NUMBER_OF_FIELDS`         | `8`  |`0x80DA0008`|インジェスト用に送信された csv ドキュメントに、間違った数のフィールドを持つレコードがあります (他のレコードに対して相対的)|
|`E_INPUT_STREAM_TOO_LARGE`         | `9`  |`0x80DA0009`|インジェストのために送信されたドキュメントが許容される長さを超えています                                           |
|`E_ENTITY_NOT_FOUND`               | `10` |`0x80DA000A`|要求されたエンティティが見つかりませんでした                                                                             |
|`E_CLOSING_QUOTE_MISSING`          | `11` |`0x80DA000B`|インジェストのために送信された csv ドキュメントに、見積もりのないフィールドが含まれています                                        |
|`E_OVERFLOW`                       | `12` |`0x80DA000C`|算術オーバーフローエラーを表します (計算の結果が変換先の型に対して大きすぎます)    |
|`E_RS_BLOCKED_ROWSTOREKEY_ERROR`   | `101`|`0x80DA0065`|ブロックされた行ストアキーにアクセスしようとしました                                                          |
|`E_RS_SHUTTINGDOWN_ERROR`          | `102`|`0x80DA0066`|行ストアをシャットダウンしています                                                                                     |
|`E_RS_LOCAL_STORAGE_FULL_ERROR`    | `103`|`0x80DA0067`|行ストアに割り当てられたローカルディスクストレージがいっぱいです                                                             |
|`E_RS_CANNOT_READ_WRITE_AHEAD_LOG` | `104`|`0x80DA0068`|行ストアストレージからの読み取りに失敗しました                                                                      |
|`E_RS_CANNOT_RETRIEVE_VALUES_ERROR`| `105`|`0x80DA0069`|行ストアストレージから値を取得できませんでした                                                              |
|`E_RS_NOT_READY_ERROR`             | `106`|`0x80DA006A`|行ストアを初期化しています                                                                                      |
|`E_RS_INSERTION_THROTTLED_ERROR`   | `107`|`0x80DA006B`|行ストアへの値の挿入が調整されました                                                                   |
|`E_RS_READONLY_ERROR`              | `108`|`0x80DA006C`|行ストアは読み取り専用状態でアタッチされています                                                                       |
|`E_RS_UNAVAILABLE_ERROR`           | `109`|`0x80DA006D`|行ストアは現在使用できません                                                                             |
|`E_RS_DOES_NOT_EXIST_ERROR`           | `110`|`0x80DA006E`| 行ストアが存在しません。                        |
|`E_SB_QUERY_THROTTLED_ERROR`           | `200`|`0x80DA00C8`|サンドボックスクエリが調整されました                                                                           |
|`E_SB_QUERY_CANCELLED`           | `201`|`0x80DA00C9`|サンドボックスクエリが取り消されました                                                                          |
|`E_UNSUPPORTED_INSTRUCTION_SET`           | `202`|`0x80DA00CA`|エンジンの必須命令セットがこの CPU でサポートされていません                                                                            |
