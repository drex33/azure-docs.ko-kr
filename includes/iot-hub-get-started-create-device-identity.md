---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: a2bc060905f6b5c7dc07b2fc10c7fc6014ba8b6d
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132875542"
---
이 섹션에서는 Azure CLI를 사용하여 이 문서의 디바이스 ID를 만듭니다. 디바이스 ID는 대/소문자를 구분합니다.

1. [Azure Cloud Shell](https://shell.azure.com/)을 엽니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 Azure CLI용 Microsoft Azure IoT 확장을 설치합니다.

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. `myDeviceId`라는 새 디바이스 ID를 만들고 다음 명령으로 디바이스 연결 문자열을 검색합니다.

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity connection-string show --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

결과에서 디바이스 연결 문자열을 기록해 둡니다. 이 디바이스 연결 문자열은 디바이스 앱이 디바이스로 IoT Hub에 연결하는 데 사용됩니다.

<!-- images and links -->
