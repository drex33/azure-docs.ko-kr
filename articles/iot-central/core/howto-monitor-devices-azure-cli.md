---
title: Azure IoT Central Explorer를 사용하여 디바이스 연결 모니터링
description: 디바이스 메시지를 모니터링하고 IoT Central Explorer CLI를 통해 디바이스 쌍 변경 내용을 관찰합니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
ms.openlocfilehash: bccf644ed13c7b93281f7f5b97c45935b2918e1e
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218808"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Azure CLI를 사용하여 디바이스 연결 모니터링

Azure CLI IoT 확장을 사용하여 디바이스가 IoT Central에 보내고 있는 메시지를 확인하고 디바이스 쌍에서 변경 내용을 관찰합니다. 이 도구를 사용하여 디바이스 연결을 디버그 및 관찰하고 디바이스 메시지가 클라우드에 도달하지 않거나 디바이스가 쌍 변경 내용에 응답하지 않는 문제를 진단할 수 있습니다.

[자세한 내용은 Azure CLI 확장 참조를 참조하세요.](/cli/azure/iot/central)

## <a name="prerequisites"></a>필수 구성 요소

IoT Central 애플리케이션에서 사용자로 추가된 Azure의 회사 또는 학교 계정

[!INCLUDE [azure-cli-prepare-your-environment-h3](../../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="install-the-iot-central-extension"></a>IoT Central 확장 설치

명령줄에서 다음 명령을 실행하여 설치합니다.

```azurecli
az extension add --name azure-iot
```

다음을 실행하여 확장 버전을 확인합니다.

```azurecli
az --version
```

Azure-IoT 확장이 0.9.9 이상인지 확인해야 합니다. 그렇지 않은 경우 다음을 실행합니다.

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>확장 사용

다음 섹션에서는 `az iot central`을 실행할 때 사용할 수 있는 일반적인 명령 및 옵션에 대해 설명합니다. 전체 명령 및 옵션 집합을 보려면 `--help`를 `az iot central` 또는 해당 하위 명령에 전달합니다.

### <a name="login"></a>로그인

Azure CLI에 로그인하여 시작합니다. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>IoT Central 앱의 애플리케이션 ID 가져오기
**관리/애플리케이션 설정** 에서 **애플리케이션 ID** 를 복사합니다. 이후 단계에서 이 값을 사용합니다.

### <a name="monitor-messages"></a>메시지 모니터링
디바이스에서 IoT Central 앱에 전송되는 메시지를 모니터링합니다. 출력에는 모든 헤더 및 주석이 포함됩니다.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>디바이스 속성 보기
지정된 디바이스에 대한 현재 읽기 및 읽기/쓰기 디바이스 속성을 봅니다.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>다음 단계

제안된 다음 단계는 [Windows(EFLOW)에서 Linux용 Azure IoT Edge를 연결하는 방법을](./howto-connect-eflow.md)알아보는 것입니다.