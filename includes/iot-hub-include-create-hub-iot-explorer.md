---
title: 포함 파일
description: 포함 파일
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 830c636dfe3f8179a459fd8bfe7aedd51ff11902
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860429"
---
## <a name="create-an-iot-hub"></a>IoT Hub 만들기
이 섹션에서는 Azure CLI를 사용하여 IoT 허브 및 리소스 그룹을 만듭니다.  Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. IoT 허브는 IoT 애플리케이션과 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다.

IoT 허브 및 리소스 그룹을 만들려면 다음을 수행합니다.

1. Azure CLI를 시작합니다. 
    - Cloud Shell을 사용하려면 CLI 명령에서 **사용해 보기** 단추를 선택하여 분할 브라우저 창에서 Cloud Shell을 시작합니다. 또는 별도의 브라우저 탭에서 [Cloud Shell](https://shell.azure.com/bash)을 열 수 있습니다.
    - Azure CLI를 로컬로 사용하는 경우 Windows CMD, PowerShell 또는 Bash와 같은 콘솔을 열고 [Azure CLI에 로그인](/cli/azure/authenticate-azure-cli)합니다.
    
    이 빠른 시작의 나머지 부분에서 CLI 명령을 실행하려면 명령 구문을 복사하고 Cloud Shell 창이나 CLI 콘솔에 붙여넣고 변수 값을 편집한 다음, Enter 키를 누릅니다.

1. [az extension add](/cli/azure/extension#az_extension_add)를 실행하여 *azure-iot* 확장을 현재 버전으로 설치하거나 업그레이드합니다.

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. [az group create](/cli/azure/group#az_group_create)를 실행하여 리소스 그룹을 만듭니다. 다음 명령은 *eastus* 위치에 *MyResourceGroup* 이라는 리소스 그룹을 만듭니다. 
    >[!NOTE]
    > 선택적으로 대체 위치를 설정할 수 있습니다. 사용 가능한 위치를 보려면 `az account list-locations`를 실행합니다. 이 자습서에서는 예제 명령에 표시된 대로 *eastus* 를 사용합니다. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```

1. [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) 명령을 사용하여 IoT Hub를 만듭니다. IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다. 

    *YourIotHubName*. IoT 허브에 대해 선택한 이름을 사용하여 다음 명령에서 이 자리 표시자와 주변 중괄호를 바꿉니다. IoT Hub 이름은 Azure에서 전역적으로 고유해야 합니다. 자리 표시자가 표시될 때마다 이 빠른 시작의 나머지 부분에서 IoT 허브 이름을 사용합니다.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```
    > [!TIP]
    > IoT Hub를 만든 후 이 빠른 시작의 나머지 부분에서 Azure IoT Explorer를 사용하여 IoT Hub와 상호 작용합니다. IoT Explorer는 기존 IoT 허브에 연결하고 디바이스를 추가, 관리 및 모니터링할 수 있는 GUI 애플리케이션입니다. 자세히 알아보려면 [Azure IoT 탐색기 설치 및 사용](../articles/iot-fundamentals/howto-use-iot-explorer.md)을 참조하세요. 선택적으로 CLI 명령을 계속 사용할 수 있습니다.

### <a name="configure-iot-explorer"></a>IoT Explorer 구성

이 빠른 시작의 나머지 부분에서는 IoT Explorer를 사용하여 IoT Hub에 디바이스를 등록하고 디바이스 원격 분석을 봅니다. 이 섹션에서는 방금 만든 IoT Hub에 연결하고 퍼블릭 모델 리포지토리에서 플러그 앤 플레이 모델을 읽도록 IoT Explorer를 구성합니다. 

> [!NOTE]
> 또한 Azure CLI를 사용하여 디바이스를 등록할 수 있습니다. *[az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) --device-id mydevice --hub-name {YourIoTHubName}* 명령을 사용하여 새 디바이스를 등록하고 *[az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show) --device-id mydevice --hub-name {YourIoTHubName}* 명령을 사용하여 디바이스의 기본 연결 문자열을 가져옵니다. 디바이스 연결 문자열을 메모해 둔 후에는 [디바이스 실행 샘플](#run-the-device-sample)로 건너뛸 수 있습니다.

IoT Hub에 대한 연결을 추가하려면

1. [az iot hub connection-string show](/cli/azure/iot/hub/connection-string#az_iot_hub_connection_string_show) 명령을 실행하여 IoT 허브에 대한 연결 문자열을 가져옵니다.

    ```azurecli
    az iot hub connection-string  show --hub-name {YourIoTHubName}
    ```

1. 따옴표 문자로 묶지 말고 연결 문자열을 복사합니다.
1. Azure IoT Explorer의 왼쪽 메뉴에서 **IoT Hub** 를 선택한 다음, **+ 연결 추가** 를 선택합니다.
1. 연결 문자열을 **연결 문자열** 상자에 붙여넣습니다.
1. **저장** 을 선택합니다.

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-add-connection.png" alt-text="IoT Explorer에서 연결을 추가하는 스크린샷":::

1. 연결에 성공하면 IoT Explorer가 **디바이스** 보기로 전환됩니다.

퍼블릭 모델 리포지토리를 추가하려면

1. IoT Explorer에서 **홈** 을 선택하여 홈 보기로 돌아갑니다.
1. 왼쪽 메뉴에서 **IoT 플러그 앤 플레이 설정** 을 선택한 다음, **+추가** 를 선택하고 드롭다운 메뉴에서 **러블릭 리포지토리** 를 선택합니다.
1. `https://devicemodels.azure.com`에 있는 퍼블릭 모델 리포지토리에 대한 항목이 나타납니다.

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-add-public-repository.png" alt-text="IoT Explorer에서 퍼블릭 모델 리포지토리를 추가하는 스크린샷":::

1. **저장** 을 선택합니다.

### <a name="register-a-device"></a>디바이스 등록

이 섹션에서는 새 디바이스 인스턴스를 만들고 만든 IoT Hub에 등록합니다. 새로 등록된 디바이스의 연결 정보를 사용하여 이후 섹션에서 디바이스를 안전하게 연결합니다.

디바이스를 등록하려면

1. IoT Explorer의 홈 보기에서 **IoT Hub** 를 선택합니다.
1. 이전에 추가한 연결이 표시됩니다. 연결 속성 아래에서 **이 허브의 디바이스 보기** 를 선택합니다.
1. **+ 새로 만들기** 를 선택하고 디바이스의 디바이스 ID(예: *mydevice*)를 입력합니다. 다른 모든 속성은 동일하게 유지합니다.
1. **만들기** 를 선택합니다.

    :::image type="content" source="media/iot-hub-include-create-hub-iot-explorer/iot-explorer-device-created.png" alt-text="Azure IoT Explorer 디바이스 ID의 스크린샷":::

1. 복사 단추를 사용하여 **기본 연결 문자열** 필드를 복사하고 기록해 둡니다. 이 연결 문자열은 나중에 필요합니다.
