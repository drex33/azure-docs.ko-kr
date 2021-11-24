---
title: MXCHIP AZ3166을 Azure IoT Hub에 연결 빠른 시작
description: Azure RTOS 임베디드 소프트웨어를 사용하여 MXCHIP AZ3166 디바이스를 Azure IoT Hub에 연결하고 원격 분석을 보냅니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/09/2021
ms.custom: mode-other
ms.openlocfilehash: 73a942dd33b219c151e7490839a46694769a40d0
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133058738"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-hub"></a>빠른 시작: MXCHIP AZ3166 DevKit를 IoT Hub에 연결

**적용 대상**: [임베디드 디바이스 개발](about-iot-develop.md#embedded-device-development)<br>
**총 완료 시간**: 30분

[![코드 찾아보기](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

이 바른 시작에서는 Azure RTOS를 사용하여 MXCHIP AZ3166 IoT DevKit(이하 MXCHIP DevKit)를 Azure IoT에 연결합니다. 

또한 IoT Explorer 및 IoT 플러그 앤 플레이를 사용하여 MXCHIP DevKit를 관리합니다. IoT 플러그 앤 플레이는 애플리케이션이 프로그래밍 방식으로 디바이스의 기능을 쿼리하고 상호 작용할 수 있도록 하는 개방형 디바이스 모델을 제공합니다. 디바이스는 이 모델을 사용하여 해당 기능을 IoT 플러그 앤 플레이 지원 애플리케이션에 브로드캐스트합니다. 이 모델을 사용하면 디바이스를 추가, 구성 및 관리하는 작업을 간소화하고 개선할 수 있습니다. 자세한 내용은 [IoT 플러그 앤 플레이 설명서](../iot-develop/index.yml)를 참조하세요.

다음 작업을 완료합니다.

* C에서 MXChip DevKit를 프로그래밍하기 위한 임베디드 개발 도구 세트 설치
* 이미지를 빌드하고 MXCHIP DevKit로 플래시
* Azure CLI를 사용하여 MXCHIP DevKit가 안전하게 연결할 Azure IoT Hub를 만들고 관리
* Azure IoT Explorer를 사용하여 IoT Hub에 디바이스를 등록하고, 디바이스 속성을 보고, 디바이스 원격 분석을 보고, 디바이스에서 직접 명령 호출

## <a name="prerequisites"></a>사전 요구 사항

* Windows 10을 실행하는 PC
* Azure 구독이 아직 없는 경우 시작하기 전에 [무료 구독을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 리포지토리를 복제하기 위한 [Git](https://git-scm.com/downloads)
* Azure CLI. 이 빠른 시작에서 Azure CLI 명령을 실행하기 위한 두 가지 옵션이 있습니다.
    * 브라우저에서 CLI 명령을 실행하는 대화형 셸인 Azure Cloud Shell을 사용합니다. 이 옵션은 아무 것도 설치할 필요가 없으므로 권장됩니다. 처음으로 Cloud Shell을 사용하는 경우 [Azure Portal](https://portal.azure.com)에 로그인합니다. [Cloud Shell 빠른 시작](../cloud-shell/quickstart.md)의 단계를 따라 **Cloud Shell을 시작하고** **Bash 환경을 선택합니다**.
    * 선택적으로 로컬 컴퓨터에서 Azure CLI를 실행합니다. Azure CLI가 이미 설치된 경우 `az upgrade`를 실행하여 CLI 및 확장을 현재 버전으로 업그레이드합니다. Azure CLI를 설치하는 방법은 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

* [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases): Azure IoT 모니터링하고 관리하는 플랫폼 간 유틸리티 
* 하드웨어

    * [MXCHIP AZ3166 IoT DevKit](https://www.seeedstudio.com/AZ3166-IOT-Developer-Kit.html)(MXCHIP DevKit)
    * Wi-Fi 2.4GHz
    * USB 2.0 A~마이크로 USB 수 케이블

## <a name="prepare-the-development-environment"></a>개발 환경 준비

개발 환경을 설정하려면 먼저 빠른 시작에 필요한 모든 자산이 포함되어 있는 GitHub 리포지토리를 복제합니다. 그런 다음, 프로그래밍 도구 세트를 설치합니다.

### <a name="clone-the-repo-for-the-quickstart"></a>빠른 시작에 사용할 리포지토리를 복제합니다.

모든 샘플 디바이스 코드, 설치 스크립트 및 오프라인 버전 설명서를 다운로드하려면 다음 리포지토리를 복제합니다. 이전에 다른 빠른 시작에서 이 리포지토리를 복제한 경우 다시 복제할 필요가 없습니다.

리포지토리를 복제하려면 다음 명령을 실행합니다.

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>도구 설치

복제된 리포지토리에는 필요한 도구를 설치하고 구성하는 설치 스크립트가 포함되어 있습니다. 다른 포함된 디바이스 빠른 시작에서 이 도구를 설치한 경우 다시 설치할 필요가 없습니다.

> [!NOTE]
> 설치 스크립트에서 설치하는 도구는 다음과 같습니다.
> * [CMake](https://cmake.org): 빌드
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): 컴파일
> * [Termite](https://www.compuphase.com/software_termite.htm): 연결된 디바이스 리소스의 직렬 포트 출력 모니터링

도구를 설치하려면

1. 파일 탐색기에서 리포지토리의 다음 경로로 이동하여 *get-toolchain.bat* 이라는 설치 스크립트를 실행합니다.

    *getting-started\tools\get-toolchain.bat*

1. 설치 후 새 콘솔 창을 열어 설치 스크립트에서 변경한 구성을 인식합니다. 이 콘솔을 사용하여 빠른 시작의 나머지 프로그래밍 작업을 완료합니다. Windows용 Windows CMD, PowerShell 또는 Git Bash를 사용할 수 있습니다.
1. 다음 코드를 실행하여 CMake 버전 3.14 이상이 설치되어 있는지 확인합니다.

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>클라우드 구성 요소 만들기

### <a name="create-an-iot-hub"></a>IoT Hub 만들기

Azure CLI를 사용하여 디바이스에 대한 이벤트 및 메시징을 처리하는 IoT Hub를 만들 수 있습니다.

IoT Hub를 만듭니다.

1. CLI 앱을 시작합니다. 이 빠른 시작의 나머지 부분에서 CLI 명령을 실행하려면 명령 구문을 복사하고 CLI 애플리케이션에 붙여넣은 후 변수 값을 편집하고 Enter 키를 누릅니다.
    - 또는 Cloud Shell을 사용하려면 [Cloud Shell](https://shell.azure.com/bash)에 대한 링크를 마우스 오른쪽 단추로 클릭하고 새 탭에서 열기 옵션을 선택합니다.
    - Azure CLI를 로컬에서 사용하고 있는 경우 CLI 콘솔 앱을 시작하고 Azure CLI에 로그인합니다.

1. [az extension add](/cli/azure/extension?view=azure-cli-latest#az_extension_add&preserve-view=true)를 실행하여 *azure-iot* 확장을 현재 버전으로 설치하거나 업그레이드합니다.

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. [az group create](/cli/azure/group#az-group-create)를 실행하여 리소스 그룹을 만듭니다. 다음 명령을 사용하면 *centralus* 지역에 *MyResourceGroup* 이라는 리소스 그룹을 만듭니다.

    > [!NOTE] 
    > 선택적으로 대체 `location`을 설정할 수 있습니다. 사용 가능한 위치를 보려면 [az account list-locations](/cli/azure/account#az-account-list-locations)를 실행합니다.

    ```azurecli
    az group create --name MyResourceGroup --location centralus
    ```

1. [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 명령을 사용하여 IoT Hub를 만듭니다. IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다.

    *YourIotHubName*. 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다. IoT Hub 이름은 Azure에서 전역적으로 고유해야 합니다. 이 자리 표시자는 이 빠른 시작의 나머지 부분에서 고유한 IoT Hub 이름을 표시하는 데 사용됩니다.

    `--sku F1` 매개 변수는 무료 계층에 IoT Hub를 만듭니다. 무료 계층 허브에는 제한된 기능 세트가 있으며 개념 증명 애플리케이션에 사용됩니다. IoT Hub 계층, 기능 및 가격 책정에 대한 자세한 내용은 [Azure IoT Hub 가격 책정](https://azure.microsoft.com/pricing/details/iot-hub)을 참조하세요. 

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName} --sku F1 --partition-count 2
    ```

1. IoT Hub를 만든 후 콘솔에서 JSON 출력을 보고, 이후 단계에서 사용할 `hostName` 값을 복사합니다. `hostName` 값은 다음 예제와 같습니다.

    `{Your IoT hub name}.azure-devices.net`

### <a name="configure-iot-explorer"></a>IoT Explorer 구성

이 빠른 시작의 나머지 단계에서는 IoT Explorer를 사용하여 IoT Hub에 디바이스를 등록하고, 디바이스 속성 및 원격 분석을 보고, 디바이스에 명령을 보냅니다. 이 섹션에서는 방금 만든 IoT Hub에 연결하고 퍼블릭 모델 리포지토리에서 플러그 앤 플레이 모델을 읽도록 IoT Explorer를 구성합니다. 

IoT Hub에 대한 연결을 추가하려면

1. CLI 앱에서 [az iot hub connection-string show](/cli/azure/iot/hub/connection-string#az_iot_hub_connection_string_show) 명령을 실행하여 IoT Hub에 대한 연결 문자열을 가져옵니다.

    ```azurecli
    az iot hub connection-string  show --hub-name {YourIoTHubName}
    ```

1. 따옴표 문자로 묶지 말고 연결 문자열을 복사합니다.
1. Azure IoT Explorer의 왼쪽 메뉴에서 **IoT Hub** 를 선택한 다음, **+ 연결 추가** 를 선택합니다.
1. 연결 문자열을 **연결 문자열** 상자에 붙여넣습니다.
1. **저장** 을 선택합니다.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-add-connection.png" alt-text="IoT Explorer에서 연결을 추가하는 스크린샷":::

1. 연결에 성공하면 IoT Explorer가 **디바이스** 보기로 전환됩니다.

퍼블릭 모델 리포지토리를 추가하려면

1. IoT Explorer에서 **홈** 을 선택하여 홈 보기로 돌아갑니다.
1. 왼쪽 메뉴에서 **IoT 플러그 앤 플레이 설정** 을 선택한 다음, **+추가** 를 선택하고 드롭다운 메뉴에서 **러블릭 리포지토리** 를 선택합니다.
1. `https://devicemodels.azure.com`에 있는 퍼블릭 모델 리포지토리에 대한 항목이 나타납니다.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-add-public-repository.png" alt-text="IoT Explorer에서 퍼블릭 모델 리포지토리를 추가하는 스크린샷":::

1. **저장** 을 선택합니다.

### <a name="register-a-device"></a>디바이스 등록

이 섹션에서는 새 디바이스 인스턴스를 만들고 만든 IoT Hub에 등록합니다. 이후 섹션에서 새로 등록한 디바이스에 대한 연결 정보를 사용하여 물리적 디바이스를 안전하게 연결합니다.

디바이스를 등록하려면

1. IoT Explorer의 홈 보기에서 **IoT Hub** 를 선택합니다.
1. 이전에 추가한 연결이 표시됩니다. 연결 속성 아래에서 **이 허브의 디바이스 보기** 를 선택합니다.
1. **+ 새로 만들기** 를 선택하고 디바이스의 디바이스 ID(예: *mydevice*)를 입력합니다. 다른 모든 속성은 동일하게 유지합니다.
1. **만들기** 를 선택합니다.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-device-created.png" alt-text="Azure IoT Explorer 디바이스 ID의 스크린샷":::

1. 복사 단추를 사용하여 **디바이스 ID** 및 **기본 키** 필드를 복사하고 적어 둡니다.

다음 섹션을 계속 진행하기 전에 다음 값을 복사했는지 확인합니다.

* `hostName`
* `deviceId`
* `primaryKey`

## <a name="prepare-the-device"></a>디바이스 준비

MXCHIP DevKit를 Azure에 연결하려면 Wi-Fi 및 Azure IoT 설정에 대한 구성 파일을 수정하고, 이미지를 다시 빌드하고, 해당 이미지를 디바이스로 플래시합니다.

### <a name="add-configuration"></a>구성 추가

1. 텍스트 편집기에서 다음 파일을 엽니다.

    *getting-started\MXChip\AZ3166\app\azure_config.h*

1. 표시된 것처럼 파일 위쪽에 있는 다음 줄을 주석으로 처리합니다.

    ```c
    // #define ENABLE_DPS
    ```

1. Wi-Fi 상수를 로컬 환경의 다음 값으로 설정합니다.

    |상수 이름|값|
    |-------------|-----|
    |`WIFI_SSID` |{*사용자의 Wi-Fi SSID*}|
    |`WIFI_PASSWORD` |{*사용자의 Wi-Fi 암호*}|
    |`WIFI_MODE` |{*파일에 열거된 Wi-Fi 모드 값 중 하나*}|

1. Azure IoT 디바이스 정보 상수를 Azure 리소스를 만든 후에 저장한 값으로 설정합니다.

    |상수 이름|값|
    |-------------|-----|
    |`IOT_HUB_HOSTNAME` |{*Iot Hub 호스트 이름 값*}|
    |`IOT_DPS_REGISTRATION_ID` |{*사용자의 디바이스 ID 값*}|
    |`IOT_DEVICE_SAS_KEY` |{*사용자의 기본 키 값*}|

1. 파일을 저장하고 닫습니다.

### <a name="build-the-image"></a>이미지 빌드

1. 콘솔 또는 파일 탐색기에서 다음 경로에 있는 *rebuild.bat* 스크립트를 실행하여 이미지를 빌드합니다.

    *getting-started\MXChip\AZ3166\tools\rebuild.bat*

2. 빌드가 완료되면 이진 파일이 다음 경로에 만들어졌는지 확인합니다.

    *getting-started\MXChip\AZ3166\build\app\mxchip_azure_iot.bin*

### <a name="flash-the-image"></a>이미지 플래시

1. MXCHIP DevKit에서 **다시 설정** 단추 및 마이크로 USB 포트를 찾습니다. 이러한 구성 요소는 다음 단계에서 사용합니다. 다음 그림에는 두 가지 구성 요소가 모두 강조 표시되어 있습니다.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/mxchip-iot-devkit.png" alt-text="MXChip DevKit 보드에서 주요 구성 요소 찾기":::

1. 마이크로 USB 케이블을 MXCHIP DevKit의 마이크로 USB 포트에 연결한 다음, 컴퓨터에 연결합니다.
1. 파일 탐색기에서 이전 섹션에서 만든 이진 파일을 찾습니다.
1. *mxchip_azure_iot.bin* 이진 파일을 복사합니다.
1. 파일 탐색기에서 컴퓨터에 연결된 MXCHIP DevKit 디바이스를 찾습니다. 디바이스는 드라이브 레이블이 **AZ3166** 인 드라이브로 시스템에 표시됩니다.
1. 이진 파일을 MXCHIP DevKit의 루트 폴더에 붙여넣습니다. 깜박임이 자동으로 시작되고 몇 초 후에 완료됩니다.

    > [!NOTE]
    > 깜박임 프로세스 중에 녹색 LED에서 MXCHIP DevKit를 켭니다.

### <a name="confirm-device-connection-details"></a>디바이스 연결 세부 정보 확인

**Termite** 앱을 사용하여 통신을 모니터링하고 디바이스가 올바르게 설정되었는지 확인할 수 있습니다.

1. **Termite** 를 시작합니다.
    > [!TIP]
    > Termite를 DevKit에 연결할 수 없는 경우 [ST-LINK 드라이버](https://www.st.com/en/development-tools/stsw-link009.html)를 설치하고 다시 시도합니다. 추가 단계는 [문제 해결](troubleshoot-embedded-device-quickstarts.md)을 참조하세요.
1. **설정** 을 선택합니다.
1. **직렬 포트 설정** 대화 상자에서 다음 설정을 확인하고, 필요한 경우 업데이트합니다.
    * **전송 속도**: 115,200
    * **포트**: MXCHIP DevKit가 연결된 포트입니다. 드롭다운에 여러 포트 옵션이 있는 경우 사용할 올바른 포트를 찾을 수 있습니다. Windows **디바이스 관리자** 를 열고, **포트** 를 확인하여 사용할 포트를 식별합니다.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/termite-settings.png" alt-text="Termite 앱의 직렬 포트 설정 스크린샷":::

1. 확인을 선택합니다.
1. 디바이스의 **다시 설정** 단추를 누릅니다. 이 단추는 디바이스에 대한 레이블을 표시하고 마이크로 USB 커넥터 근처에 있습니다.
1. **Termite** 앱에서 다음 검사점 값을 확인하여 디바이스가 초기화되고 Azure IoT에 연결되었는지 확인합니다.

    ```output
    Starting Azure thread

    Initializing WiFi
        MAC address: C8:93:46:8A:4C:43
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 192.168.0.18
        Mask: 255.255.255.0
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 157.245.166.169
        SNTP time update: Jun 8, 2021 18:16:50.807 UTC
    SUCCESS: SNTP initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```

다음 단계에서 디바이스 출력을 모니터링하려면 Termite를 열어 둡니다.

## <a name="view-device-properties"></a>디바이스 속성 보기

Azure IoT Explorer를 사용하여 디바이스의 속성을 보고 관리할 수 있습니다. 이 섹션 및 다음 섹션에서는 IoT Explorer에 표시되는 플러그 앤 플레이 기능을 사용하여 MXCHIP DevKit를 관리하고 상호 작용합니다. 이러한 기능은 퍼블릭 모델 리포지토리에서 MXCHIP DevKit에 대해 게시된 디바이스 모델을 사용합니다. 이 빠른 시작의 앞부분에서는 이 리포지토리에서 디바이스 모델을 검색하도록 IoT Explorer를 구성했습니다. 대부분의 경우에는 IoT Explorer에 있는 디바이스 창의 왼쪽 메뉴에서 동일한 작업을 선택하여 플러그 앤 플레이를 사용하지 않고도 동일한 작업을 수행할 수 있습니다. 그러나 플러그 앤 플레이를 사용하면 개선된 환경을 얻을 수 있습니다. IoT Explorer는 플러그 앤 플레이 디바이스에서 지정된 디바이스 모델을 읽고 해당 디바이스와 관련된 정보를 제공할 수 있기 때문입니다.  

Iot Explorer에서 디바이스에 대한 IoT 플러그 앤 플레이 구성 요소에 액세스하려면

1. IoT Explorer의 홈 보기에서 **IoT Hub** 를 선택한 다음, **이 허브의 디바이스 보기** 를 선택합니다.
1. 디바이스를 선택합니다.
1. **IoT 플러그 앤 플레이 구성 요소** 를 참조하세요.
1. **기본 구성 요소** 를 선택합니다. IoT Explorer에는 디바이스에 구현된 IoT 플러그 앤 플레이 구성 요소가 표시됩니다.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-default-component-view.png" alt-text="IoT Explorer의 MXCHIP DevKit 기본 구성 요소 스크린샷":::

1. **인터페이스** 탭에서 디바이스 모델 **설명** 에 있는 JSON 콘텐츠를 확인합니다. JSON에는 디바이스 모델의 각 IoT 플러그 앤 플레이 구성 요소에 대한 구성 정보가 포함되어 있습니다.

    IoT Explorer의 각 탭은 디바이스 모델의 IoT 플러그 앤 플레이 구성 요소 중 하나에 해당합니다.

    | 탭 | Type | 속성 | Description |
    |---|---|---|---|
    | **인터페이스** | 인터페이스 | `MXCHIP Getting Started Guide` | MXCHIP DevKit에 대한 예제 모델 |
    | **속성(읽기 전용)** | 속성 | -- | 이 모델에는 현재 읽기 전용 속성이 없습니다. |
    | **속성(쓰기 가능)** | 속성 | `telemetryInterval` | 디바이스에서 원격 분석을 전송하는 간격 |
    | **명령** | 명령 | `setLedState` | LED 켜기 또는 끄기 |
    | **원격 분석** | 원격 분석 | `temperature` | 섭씨 온도 |

Azure IoT Explorer를 사용하여 디바이스 속성을 보려면

1. **속성(읽기 전용)** 탭을 선택합니다. 현재, 디바이스 모델에 의해 노출되는 읽기 전용 속성은 없습니다.
1. **속성(쓰기 가능)** 탭을 선택합니다. 원격 분석이 전송되는 간격이 표시됩니다.
1. `telemetryInterval`을 *5* 로 변경한 후 **원하는 값 업데이트** 를 선택합니다. 이제 디바이스에서 이 간격을 사용하여 원격 분석을 보냅니다.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-set-telemetry-interval.png" alt-text="IoT Explorer에서 MXCHIP DevKit에 대한 원격 분석 간격을 설정하는 스크린샷":::

1. IoT Explorer는 알림으로 응답합니다. Termite에서 업데이트를 확인할 수도 있습니다.
1. 원격 분석 간격을 10으로 다시 설정합니다.
 
Azure CLI를 사용하여 디바이스 속성을 보려면

1. [az iot hub device-identity show](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_show) 명령을 실행합니다.

    ```azurecli
    az iot hub device-identity show --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. 콘솔 출력에서 디바이스의 속성을 검사합니다.

## <a name="view-telemetry"></a>원격 분석 보기

Azure IoT Explorer를 사용하면 디바이스에서 클라우드로의 원격 분석 흐름을 볼 수 있습니다. 필요에 따라 Azure CLI를 사용하여 동일한 작업을 수행할 수 있습니다.

Azure IoT Explorer에서 원격 분석을 보려면

1. IoT Explorer의 디바이스에 대한 **IoT 플러그 앤 플레이 구성 요소**(기본 구성 요소) 창에서 **원격 분석** 탭을 선택합니다. **기본 제공 이벤트 허브 사용** 이 *예* 로 설정되어 있는지 확인합니다.
1. **시작** 을 선택합니다.
1. 디바이스에서 메시지를 클라우드에 보낼 때 원격 분석을 봅니다.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-device-telemetry.png" alt-text="IoT Explorer의 디바이스 원격 분석 스크린샷":::

    > [!NOTE]
    > Termite 앱을 사용하여 디바이스에서 원격 분석을 모니터링할 수도 있습니다.

1. 디바이스 모델에서 지정한 데이터 형식으로 이벤트를 보려면 **모델링된 이벤트 표시** 확인란을 선택합니다.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-show-modeled-events.png" alt-text="IoT Explorer의 모델링된 원격 분석 이벤트 스크린샷":::

1. **중지** 를 선택하여 수신 이벤트를 종료합니다.

Azure CLI를 사용하여 디바이스 원격 분석을 보려면

1. [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) 명령을 실행합니다. 이전에 디바이스 및 IoT Hub에 대해 Azure IoT에서 만든 이름을 사용합니다.

    ```azurecli
    az iot hub monitor-events --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. 콘솔에서 JSON 출력을 봅니다.

    ```json
    {
        "event": {
            "origin": "mydevice",
            "module": "",
            "interface": "dtmi:azurertos:devkit:gsgmxchip;1",
            "component": "",
            "payload": "{\"humidity\":41.21,\"temperature\":31.37,\"pressure\":1005.18}"
        }
    }
    ```

1. Ctrl+C를 선택하여 모니터링을 종료합니다.


## <a name="call-a-direct-method-on-the-device"></a>디바이스에서 직접 메서드 호출

Azure IoT Explorer를 사용하여 디바이스에서 구현한 직접 메서드를 호출할 수도 있습니다. 직접 메서드에는 이름이 있으며, 필요에 따라 JSON 페이로드, 구성 가능한 연결 및 메서드 시간 제한이 있을 수 있습니다. 이 섹션에서는 LED를 설정하거나 해제하는 메서드를 호출합니다. 필요에 따라 Azure CLI를 사용하여 동일한 작업을 수행할 수 있습니다.

Azure IoT 탐색기에서 메서드를 호출하려면

1. IoT Explorer의 디바이스에 대한 **IoT 플러그 앤 플레이 구성 요소**(기본 구성 요소) 창에서 **명령** 탭을 선택합니다.
1. **setLedState** 명령의 경우 **상태** 를 **true** 로 설정합니다.
1. **명령 보내기** 를 선택합니다. IoT Explorer에 알림이 표시되고 디바이스의 노란색 사용자 LED 표시등이 켜집니다.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-invoke-method.png" alt-text="IoT Explorer에서 setLedState 메서드를 호출하는 스크린샷":::

1. **상태** 를 **false** 로 설정한 다음, **명령 보내기** 를 선택합니다. 노란색 사용자 LED 표시등이 꺼집니다.
1. 필요에 따라 Termite에서 출력을 확인하여 메서드의 상태를 모니터링할 수 있습니다.

Azure CLI를 사용하여 메서드를 호출하려면

1. [az iot hub invoke-device-method](/cli/azure/iot/hub#az_iot_hub_invoke_device_method) 명령을 실행하고 메서드 이름과 페이로드를 지정합니다. 이 메서드의 경우 LED를 켜려면 `method-payload`를 `true`로 설정하고, LED를 끄려면 `false`로 설정합니다.

    ```azurecli
    az iot hub invoke-device-method --device-id mydevice --method-name setLedState --method-payload true --hub-name {YourIoTHubName}
    ```

    CLI 콘솔은 디바이스에서 메서드 호출의 상태를 표시합니다. 여기서 `204`는 성공을 나타냅니다.

    ```json
    {
        "payload": {},
        "status": 200
    }    
    ```

1. 디바이스에서 LED 상태를 확인합니다.

1. Termite 터미널을 보고 출력 메시지를 확인합니다.

    ```output
    Receive direct method: setLedState
        Payload: true
    LED is turned ON
    Device twin property sent: {"ledState":true}
    ```

## <a name="troubleshoot-and-debug"></a>문제 해결 및 디버그

디바이스 코드 빌드, 디바이스 플래시 또는 연결에 문제가 발생하는 경우 [문제 해결](troubleshoot-embedded-device-quickstarts.md)을 참조하세요.

애플리케이션 디버깅에 대한 자세한 내용은 [Visual Studio Code를 사용하여 디버깅](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 Azure 리소스가 더 이상 필요하지 않은 경우 Azure CLI를 사용하여 리소스 그룹 및 모든 리소스를 삭제할 수 있습니다.

> [!IMPORTANT] 
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다.

리소스 그룹을 이름으로 삭제하려면:

1. [az group delete](/cli/azure/group#az-group-delete) 명령을 실행합니다. 그러면 만든 리소스 그룹, IoT Hub 및 디바이스 등록이 제거됩니다.

    ```azurecli-interactive
    az group delete --name MyResourceGroup
    ```

1. [az group list](/cli/azure/group#az-group-list) 명령을 실행하여 리소스 그룹을 삭제했는지 확인합니다.  

    ```azurecli-interactive
    az group list
    ```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure RTOS 샘플 코드가 포함된 사용자 지정 이미지를 빌드한 다음, 해당 이미지를 MXCHIP DevKit 디바이스로 플래시했습니다. 또한 Azure CLI 및/또는 IoT Explorer를 사용하여 Azure 리소스를 만들고, MXCHIP DevKit를 Azure에 안전하게 연결하고, 원격 분석을 보고, 메시지를 보냈습니다.

다음 단계로, 다음 문서에서 IoT 디바이스 SDK를 사용하여 디바이스를 Azure IoT 연결하는 방법을 자세히 알아봅니다. 

> [!div class="nextstepaction"]
> [IoT Central에 MXCHIP AZ3166 devkit 연결](quickstart-devkit-mxchip-az3166.md)
> [!div class="nextstepaction"]
> [IoT Hub에 시뮬레이션된 디바이스 연결](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [IoT Hub에 시뮬레이션된 디바이스 연결](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Azure RTOS는 기본 MCU/MPU 하드웨어 보호 메커니즘을 사용하여 통신을 보호하고 코드 및 데이터 격리를 만드는 구성 요소를 OEM에 제공합니다. 그러나 각 OEM은 궁극적으로 디바이스에서 진화하는 보안 요구 사항을 충족하는지 확인해야 합니다.
