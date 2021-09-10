---
title: Azure IoT Hub 에이전트의 디바이스 업데이트 프로비저닝| Microsoft Docs
description: Azure IoT Hub 에이전트의 디바이스 업데이트 프로비저닝
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: e0df727c93e5307e9b66ad5755c8218954a7ff7b
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113552553"
---
# <a name="device-update-agent-provisioning"></a>디바이스 업데이트 에이전트 프로비저닝

디바이스 업데이트 모듈 에이전트는 동일한 논리적 장치의 일부로 IoT Hub에 연결되는 다른 시스템 프로세스 및 [IoT Edge 모듈](../iot-edge/iot-edge-modules.md)과 함께 실행할 수 있습니다. 이 섹션에서는 디바이스 업데이트 에이전트를 모듈 ID로 프로비저닝하는 방법을 설명합니다. 


## <a name="module-identity-vs-device-identity"></a>모듈 ID vs 디바이스 ID

IoT Hub에서 각 디바이스 ID 아래에 최대 50개의 모듈 ID를 만들 수 있습니다. 각 모듈 ID는 모듈 쌍을 암시적으로 생성합니다. 디바이스 쪽에서 IoT Hub 디바이스 SDK를 사용하면 각각 IoT Hub에 대한 독립적 연결을 여는 모듈을 만들 수 있습니다. 모듈 ID 및 모듈 쌍은 디바이스 ID 및 디바이스 쌍과 동일한 기능을 제공하지만 세분성이 더 높습니다. [IoT Hub의 모듈 ID에 대한 자세한 정보](../iot-hub/iot-hub-devguide-module-twins.md)

디바이스에 모듈 ID로 에이전트를 추가하기 위해 디바이스 수준 에이전트에서 마이그레이션하는 경우 디바이스 쌍을 통해 통신하던 이전 에이전트를 제거합니다. Device Update 에이전트를 모듈 ID로 프로비저닝할 때 디바이스와 Device Update 서비스 간 모든 통신은 모듈 쌍을 통해 발생하므로 [그룹](device-update-groups.md)을 만들 때 디바이스의 모듈 쌍에 태그를 지정해야 하고 모든 [통신](device-update-plug-and-play.md)은 모듈 쌍을 통해 발생해야 합니다.

## <a name="support-for-device-update"></a>디바이스 업데이트에 대한 지원

현재 디바이스 업데이트에서는 다음 IoT 디바이스 유형이 지원됩니다.

* Linux 디바이스(IoT Edge 및 비 IoT Edge 디바이스)
    * 이미지 A/B 업데이트
        - 오픈 소스를 통해 확장하여 필요에 따라 다른 아키텍처에 대한 [고유의 이미지를 빌드](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent)하는 Yocto - ARM64(참조 이미지)
        - Ubuntu 18.04 시뮬레이터
       
    * 다음 플랫폼/아키텍처에 대한 빌드가 지원되는 패키지 에이전트
        - Ubuntu Server 18.04 x64 패키지 에이전트 
        - Debian 9 
        
* 제한된 디바이스
    * Azure RTOS(실시간 운영 체제) 디바이스 업데이트 에이전트 샘플: [Azure RTOS용 Azure IoT Hub 자습서의 디바이스 업데이트](device-update-azure-real-time-operating-system.md)

* 연결이 끊긴 디바이스 
    * [연결이 끊긴 디바이스 업데이트에 대한 지원 이해](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>사전 요구 사항  

[패키지 기반 업데이트](./understand-device-update.md#support-for-a-wide-range-of-update-artifacts)를 위해 IoT 디바이스/IoT Edge 디바이스를 설정하려면 다음 단계를 수행하여 컴퓨터의 리포지토리에 packages.microsoft.com을 추가합니다.

1. 디바이스 업데이트 에이전트를 설치하려는 컴퓨터 또는 IoT 디바이스에 로그온합니다.

1. 터미널 창을 엽니다.

1. 디바이스 운영 체제와 일치하는 리포지토리 구성을 설치합니다.

    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. 생성된 목록을 sources.list.d 디렉터리에 복사합니다.

    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Microsoft GPG 공개 키를 설치합니다.

    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>디바이스 업데이트 에이전트를 모듈 ID로 프로비저닝하는 방법

이 섹션에서는 디바이스 업데이트 에이전트를 모듈 ID로 프로비저닝하는 방법을 설명합니다. 
* IoT Edge 사용 디바이스 
* 비 Edge IoT 디바이스 또는
* 기타 IoT 디바이스 
 
아래 섹션 전체 또는 일부에 따라 관리 중인 IoT 장치 유형에 따라 장치 업데이트 에이전트를 추가하십시오. 

### <a name="on-iot-edge-enabled-devices"></a>IoT Edge 사용 디바이스

다음 지침에 따라 디바이스 업데이트 에이전트를 [IoT Edge 사용 디바이스](../iot-edge/index.yml)에 프로비저닝합니다.

1. 지침에 따라 [Azure IoT Edge 런타임을 설치](../iot-edge/how-to-install-iot-edge.md?preserve-view=true&view=iotedge-2020-11)합니다.

1. Device Update 이미지 업데이트 에이전트를 설치합니다.

    [Artifacts](https://github.com/Azure/iot-hub-device-update/releases) 리포지토리에서 샘플 이미지를 제공합니다. swUpdate 파일은 Raspberry Pi B3+ 보드에 플래시할 수 있는 기본 이미지입니다. .gz 파일은 IoT Hub용 디바이스 업데이트를 통해 가져올 업데이트입니다. 예는 [IoT Hub 기기에 이미지를 플래시하는 방법](./device-update-raspberry-pi.md#flash-sd-card-with-image)을 참조하세요.  

1. Device Update 패키지 업데이트 에이전트 설치

    - packages.miscrosoft.com의 최신 에이전트 버전: 디바이스에서 패키지 목록을 업데이트하고 다음을 사용하여 Device Update 에이전트 패키지와 해당 종속성을 설치합니다.   

        ```shell
        sudo apt-get update
        ```
    
        ```shell
        sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
        ```
    
    - [아티팩트](https://github.com/Azure/iot-hub-device-update/releases)의 릴리스 후보 에이전트 버전을 나타내는 ‘rc’: Device Update 에이전트를 설치하려는 머신에 .dep 파일을 다운로드한 후 다음을 수행합니다.
   
        ```shell
        sudo apt-get install -y ./"<PATH TO FILE>"/"<.DEP FILE NAME>"
        ```
    
1. IoT Edge 디바이스에서 Device Update 에이전트를 시작할 준비가 되었습니다. 

### <a name="on-non-edge-iot-linux-devices"></a>비 Edge IoT Linux 디바이스

다음 지침에 따라 디바이스 업데이트 에이전트를 IoT Linux 디바이스에 프로비저닝합니다.

1. IoT ID 서비스를 설치하고 IoT 디바이스에 최신 버전을 추가합니다. 
    1. 컴퓨터 또는 IoT 디바이스에 로그온합니다.
    1. 터미널 창을 엽니다.
    1.  다음 명령을 사용하여 IoT 디바이스에 최신 [IoT ID 서비스](https://github.com/Azure/iot-identity-service/blob/main/docs-dev/packaging.md#installing-and-configuring-the-package)를 설치합니다.
    > [!Note]
    > IoT ID 서비스는 현재 대칭 키를 사용하여 IoT Hub에 모듈 ID를 등록합니다.

    ```shell
    sudo apt-get install aziot-identity-service
    ```
        
1. IoT ID 서비스를 프로비저닝하여 IoT 디바이스 정보를 가져옵니다.

    프로비저닝 정보를 추가할 수 있도록 구성 템플릿의 사용자 지정 복사본을 만듭니다. 터미널에서 다음 명령을 입력합니다.
      
    ```shell
    sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
    ```
   
1. 다음으로 이 디바이스 또는 컴퓨터의 프로비저닝 프로그램 역할을 할 디바이스의 연결 문자열을 포함하도록 구성 파일을 편집합니다. 터미널에 아래 명령을 입력합니다.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. 다음 예시와 같은 메시지가 표시됩니다.

    :::image type="content" source="media/understand-device-update/config.png" alt-text="IoT ID 서비스 구성 파일의 다이어그램입니다." lightbox="media/understand-device-update/config.png":::

    1. 동일한 nano 창에서 “연결 문자열로 수동 프로비저닝”을 사용하여 블록을 찾습니다.
    1. 창에서 ‘provisioning’ 앞의 “#” 기호를 삭제합니다.
    1. 창에서 ‘source’ 앞의 “#” 기호를 삭제합니다. 
    1. 창에서 ‘connection_string’ 앞의 “#”기호를 삭제합니다.
    1. 창에서 따옴표 안에 있는 ‘connection_string’ 오른쪽의 문자열을 삭제한 다음 연결 문자열을 추가합니다. 
    1. ‘Ctrl + X’와 ‘Y’를 눌러 파일의 변경 내용을 저장하고 ‘enter’ 키를 눌러 변경 내용을 저장합니다. 
    
1. 이제 다음 명령을 사용하여 IoT ID 서비스를 적용하고 다시 시작합니다. 이제 “완료되었습니다.”라는 인쇄물이 표시되며, IoT ID 서비스를 성공적으로 구성했음을 의미합니다.

    > [!Note]
    > IoT ID 서비스는 현재 대칭 키를 사용하여 IoT Hub에 모듈 ID를 등록합니다.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1. 마지막으로, Device Update 에이전트를 설치합니다. [아티팩트](https://github.com/Azure/iot-hub-device-update/releases)에서 샘플 이미지를 제공합니다. swUpdate 파일은 Raspberry Pi B3+ 보드에서 플래시할 수 있는 기본 이미지이며 .gz 파일은 Device Update for IoT Hub를 통해 가져올 업데이트입니다. [IoT Hub 디바이스에 이미지를 플래시하는 방법](./device-update-raspberry-pi.md#flash-sd-card-with-image)의 예제를 참조하세요.

1. IoT 디바이스에서 Device Update 에이전트를 시작할 준비가 되었습니다. 

### <a name="other-iot-devices"></a>기타 IoT 디바이스

테스트 IoT ID 서비스를 사용하지 않고 또는 제한된 디바이스에서도 디바이스 업데이트 에이전트를 구성할 수 있습니다. 아래 단계에 따라 연결 문자열을 사용하여 (모듈 또는 디바이스에서) 디바이스 업데이트 에이전트를 프로비저닝합니다.

1. [Artifacts](https://github.com/Azure/iot-hub-device-update/releases) 리포지토리에서 샘플 이미지를 제공합니다. swUpdate 파일은 Raspberry Pi B3+ 보드에 플래시할 수 있는 기본 이미지입니다. .gz 파일은 IoT Hub용 디바이스 업데이트를 통해 가져올 업데이트입니다. 예는 [IoT Hub 기기에 이미지를 플래시하는 방법](./device-update-raspberry-pi.md#flash-sd-card-with-image)을 참조하세요.

1. 컴퓨터 또는 IoT Edge 디바이스/IoT 디바이스에 로그온합니다.
    
1. 터미널 창을 엽니다.

1. [디바이스 업데이트 구성 파일](device-update-configuration-file.md)에 연결 문자열을 추가합니다.

    1. 터미널 창에 다음을 입력합니다.
   
        - [패키지 업데이트](device-update-ubuntu-agent.md) 사용: sudo nano /etc/adu/adu-conf.txt
        - [이미지 업데이트](device-update-raspberry-pi.md) 사용: sudo nano /adu/adu-conf.txt
       
    1. 일부 텍스트가 표시된 창이 열립니다. IoT 디바이스에 디바이스 업데이트 에이전트를 처음으로 프로비저닝할 때 ‘connection_String=’ 뒤의 전체 문자열을 삭제합니다. 자리 표시자 텍스트입니다.
    
    1. 터미널에서 <your-connection-string>을 디바이스 업데이트 에이전트 인스턴스의 디바이스 연결 문자열로 바꿉니다. Enter를 선택한 다음 **저장** 을 선택합니다. 다음 예제와 같이 표시됩니다.
    
        ```text
        connection_string=<ADD CONNECTION STRING HERE>
        ```   
        
    > [!Important]
    > 연결 문자열 옆에 따옴표를 추가하지 마세요.
    
1. 이제 IoT 디바이스에서 디바이스 업데이트 에이전트를 시작할 준비가 되었습니다. 


## <a name="how-to-start-the-device-update-agent"></a>디바이스 업데이트 에이전트를 시작하는 방법

이 섹션에서는 IoT 디바이스에서 성공적으로 실행되는 모듈 ID로 디바이스 업데이트 에이전트를 시작하고 확인하는 방법을 설명합니다.

1. 디바이스 업데이트 에이전트가 설치된 컴퓨터 또는 디바이스에 로그인합니다.

1. 터미널 창을 열고 아래 명령을 입력합니다.

    ```shell
    sudo systemctl restart adu-agent
    ```
    
1. 다음 명령을 사용하여 에이전트의 상태를 확인할 수 있습니다. 문제가 확인되는 경우 이 [문제 해결 가이드](troubleshoot-device-update.md)를 참조하세요.
    
    ```shell
    sudo systemctl status adu-agent
    ```
    
    정상 상태가 표시됩니다.

1. IoT Hub 포털에서 IoT 디바이스 또는 IoT Edge 디바이스로 이동하여 디바이스 업데이트 에이전트로 구성한 디바이스를 찾습니다. 그러면 디바이스 업데이트 에이전트가 모듈로서 실행됩니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="디바이스 업데이트 모듈 이름의 다이어그램입니다." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>디바이스 업데이트 에이전트를 빌드하고 실행하는 방법

고유의 고객 디바이스 업데이트 에이전트를 빌드하고 수정할 수도 있습니다.

지침에 따라 소스에서 디바이스 업데이트 에이전트를 [빌드](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)합니다.

에이전트가 성공적으로 빌드되면 에이전트를 [실행](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md)합니다.

이제 에이전트를 이미지에 통합하는 데 필요한 변경 내용을 적용합니다.  참고 자료는 디바이스 업데이트 에이전트를 [수정](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md)하는 방법을 참조하세요.


## <a name="troubleshooting-guide"></a>문제 해결 가이드

문제가 발생하는 경우 IoT Hub 디바이스 업데이트 [문제 해결 가이드](troubleshoot-device-update.md)를 검토하여 가능한 문제를 차단 해제하고 필요한 정보를 수집하여 Microsoft에 제공할 수 있습니다.


## <a name="next-steps"></a>다음 단계

다음 미리 빌드된 이미지와 이진 파일을 사용하여 IoT Hub 디바이스 업데이트의 간단한 데모를 실행할 수 있습니다.

- 오픈 소스를 통해 필요에 따라 다른 아키텍처용 고유 이미지를 빌드하여 확장 가능한 [이미지 업데이트: Raspberry Pi 3 B+ 참조 Yocto 이미지로 시작](device-update-raspberry-pi.md)

- [Ubuntu(18.04 x64) 시뮬레이터 참조 에이전트를 사용하여 시작](device-update-simulator.md)

- [패키지 업데이트: Ubuntu Server 18.04 x64 패키지 에이전트를 사용하여 시작](device-update-ubuntu-agent.md)

- [Azure RTOS(실시간 운영 체제)용 Azure IoT Hub 자습서의 디바이스 업데이트](device-update-azure-real-time-operating-system.md)
