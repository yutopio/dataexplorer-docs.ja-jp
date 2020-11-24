---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: orspod
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: orspodek
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 035435d535a2b67b0de8c4fb9e2af9b1ea1dc5ff
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2020
ms.locfileid: "95394713"
---
このセクションでは、Azure CLI を使用して、この記事用のデバイス ID を作成します。 デバイス ID には大文字と小文字の区別があります。

1. [Azure Cloud Shell](https://shell.azure.com/) を開きます。

1. Azure Cloud Shell で次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI をインストールします。

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. 次のコマンドを実行して、`myDeviceId` という新しいデバイス ID を作成し、デバイス接続文字列を取得します。

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

結果として得られたデバイスの接続文字列をメモしておきます。 このデバイス接続文字列は、デバイス アプリからデバイスとして IoT Hub に接続する際に使用します。

