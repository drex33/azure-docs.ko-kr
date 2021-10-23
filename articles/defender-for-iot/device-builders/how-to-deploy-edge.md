---
title: IoT Edge 보안 모듈 배포
description: IoT Edge에 Defender for IoT 보안 에이전트를 배포하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: 67c60841d4d1e9080c95cf50a71be6ad4a845ef1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130238627"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>IoT Edge 디바이스에 보안 모듈 배포

**Defender for IoT** 모듈은 IoT Edge 디바이스를 위한 포괄적인 보안 솔루션을 제공합니다.
보안 모듈은 운영 체제 및 컨테이너 시스템에서 원시 보안 데이터를 수집하고 집계하고 분석하여 실행 가능한 보안 권장 사항 및 경고로 변환합니다.
자세한 내용은 [IoT Edge용 보안 모듈](security-edge-architecture.md)을 참조하세요.

이 문서에서는 IoT Edge 디바이스에 보안 모듈을 배포하는 방법에 대해 알아봅니다.

## <a name="deploy-security-module"></a>보안 모듈 배포

다음 단계를 따라 IoT Edge용 Defender for IoT 보안 모듈을 배포합니다.

### <a name="prerequisites"></a>필수 구성 요소

1. IoT Hub에서 디바이스가 [새 디바이스 등록](../../iot-edge/how-to-provision-single-device-linux-symmetric.md#register-your-device)인지 확인합니다.

1. Defender for IoT Edge 모듈을 사용하려면 IoT Edge 디바이스에 [AuditD 프레임워크](https://linux.die.net/man/8/auditd)가 설치되어 있어야 합니다.

    - IoT Edge 디바이스에서 다음 명령을 실행하여 프레임워크를 설치합니다.

    `sudo apt-get install auditd audispd-plugins`

    - 다음 명령을 실행하여 AuditD가 활성 상태인지 확인합니다.

    `sudo systemctl status auditd`<br>
    - 예상 응답은 `active (running)`입니다.

### <a name="deployment-using-azure-portal"></a>Azure Portal을 사용하는 배포

1. Azure Portal에서 **Marketplace** 를 엽니다.

1. **사물 인터넷** 을 선택한다음 **IoT용 Azure Security Center** 를 검색하고 선택합니다.

   :::image type="content" source="media/howto/edge-onboarding.png" alt-text="Defender for IoT 선택":::

1. **만들기** 를 선택하여 배포를 구성합니다.

1. IoT Hub의 Azure **구독** 을 선택한 다음 **IoT Hub** 를 선택합니다.<br>**디바이스에 배포** 를 선택하여 단일 디바이스를 대상으로 하거나 **대규모로 배포** 를 선택하여 여러 디바이스를 대상으로 하고, **만들기** 를 선택합니다. 대규모 배포에 대한 자세한 내용은 [배포 방법](../../iot-edge/how-to-deploy-at-scale.md)을 참조하세요.

    >[!Note]
    >**대규모로 배포** 를 선택한 경우 다음 지침의 **모듈 추가** 탭으로 진행하기 전에 디바이스 이름 및 세부 정보를 추가합니다.

각 단계를 완료하여 Defender for IoT용 IoT Edge 배포를 완료합니다.

#### <a name="step-1-modules"></a>1단계: 모듈

1. **AzureSecurityCenterforIoT** 모듈을 선택합니다.

1. **모듈 설정** 탭에서 **이름** 을 **azureiotsecurity** 로 변경합니다.

1. **환경 변수** 탭에서 필요한 경우 변수를 추가합니다. 예를 들어 *디버그 수준* 을 추가하고 ‘치명적’, ‘오류’, ‘경고’, ‘정보’ 등의 값 중 하나로 설정할 수 있습니다.

1. **컨테이너 만들기 옵션** 탭에서 다음 구성을 추가합니다.

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. **모듈 쌍 설정** 탭에서 다음 구성을 추가합니다.

   모듈 쌍 속성:

   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   모듈 쌍 속성 콘텐츠:

   ```json
     {

     }
   ```

   에이전트를 구성하는 방법에 대한 자세한 내용은 [보안 에이전트 구성](./how-to-agent-configuration.md)을 참조하세요.

1. **업데이트** 를 선택합니다.

#### <a name="step-2-runtime-settings"></a>2단계: 런타임 설정

1. **런타임 설정** 을 선택합니다.
2. **Edge Hub** 에서 **이미지** 를 **mcr.microsoft.com/azureiotedge-hub:1.0.8.3** 으로 변경합니다.

    >[!Note]
    > 현재 1.0.8.3 또는 이전 버전이 지원됩니다.

3. **만들기 옵션** 이 다음 구성으로 설정되어 있는지 확인합니다.

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

4. **저장** 을 선택합니다.

5. **다음** 을 선택합니다.

#### <a name="step-3-specify-routes"></a>3단계: 경로 지정

1. **경로 지정** 탭에서 다음 예제에 따라 **azureiotsecurity** 모듈에서 **$upstream** 으로 메시지를 전달할 명시적이거나 암시적인 경로가 있는지 확인합니다. 경로가 있는 경우에만 **다음** 을 선택합니다.

   예제 경로:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. **다음** 을 선택합니다.

#### <a name="step-4-review-deployment"></a>4단계: 배포 검토

- **배포 검토** 탭에서 배포 정보를 검토한 다음 **만들기** 를 선택하여 배포를 완료합니다.

## <a name="diagnostic-steps"></a>진단 단계

문제가 발생하는 경우 IoT Edge 보안 모듈 디바이스의 상태를 알아보는 가장 좋은 방법은 컨테이너 로그입니다. 이 섹션의 명령 및 도구를 사용하여 정보를 수집합니다.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>필요한 컨테이너가 설치되어 있고 예상대로 작동하는지 확인합니다.

1. IoT Edge 디바이스에서 다음 명령을 실행합니다.

    `sudo docker ps`

1. 다음 컨테이너가 실행 중인지 확인합니다.

   | 이름 | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   필요한 최소 컨테이너가 없는 경우 IoT Edge 배포 매니페스트가 권장 설정과 일치하는지 확인합니다. 자세한 내용은 [IoT Edge 모듈 배포](#deployment-using-azure-portal)를 참조하세요.

### <a name="inspect-the-module-logs-for-errors"></a>모듈 로그에서 오류 검사

1. IoT Edge 디바이스에서 다음 명령을 실행합니다.

   `sudo docker logs azureiotsecurity`

1. 자세한 로그를 보려면 **azureiotsecurity** 모듈 배포에 `logLevel=Debug` 환경 변수를 추가합니다.

## <a name="next-steps"></a>다음 단계

구성 옵션에 대해 자세히 알아보려면 모듈 구성 방법 가이드를 참조하세요.
> [!div class="nextstepaction"]
> [모듈 구성 방법 가이드](./how-to-agent-configuration.md)
