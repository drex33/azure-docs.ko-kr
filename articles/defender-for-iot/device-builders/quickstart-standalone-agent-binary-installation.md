---
title: '빠른 시작: IoT 마이크로 에이전트용 Defender 설치(미리 보기)'
description: 이 빠른 시작에서는 Defender for IoT 마이크로 에이전트를 설치하고 인증하는 방법을 알아봅니다.
ms.date: 11/09/2021
ms.topic: quickstart
ms.openlocfilehash: 30b8630cacdec1eebcb4b2984154a9869ce816ff
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707351"
---
# <a name="quickstart-install-defender-for-iot-micro-agent-preview"></a>빠른 시작: IoT 마이크로 에이전트용 Defender 설치(미리 보기)

이 문서에서는 Defender for IoT 마이크로 에이전트를 설치하고 인증하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

Defender for IoT 모듈을 설치하기 전에 IoT Hub에서 모듈 ID를 만들어야 합니다. 모듈 ID를 만드는 방법에 대한 자세한 내용은 [Defender for IoT 마이크로 에이전트 모듈 쌍 만들기(미리 보기)](quickstart-create-micro-agent-module-twin.md)를 참조하세요.

## <a name="install-the-package"></a>패키지 설치

**적절한 Microsoft 패키지 리포지토리를 추가하려면**:

1. 디바이스 운영 체제와 일치하는 리포지토리 구성을 다운로드합니다.  

    - Ubuntu 18.04의 경우

        ```bash
        curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
        ```

    - Ubuntu 20.04의 경우

        ```bash
            curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list > ./microsoft-prod.list
        ```

    - Debian 9(AMD64 및 ARM64 모두)의 경우

        ```bash
        curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
        ```

1. 리포지토리 구성을 `sources.list.d` 디렉터리에 복사합니다.

    ```bash
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```

1. Microsoft GPG 공개 키를 설치합니다.

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

Defender for IoT 마이크로 에이전트 패키지를 Debian 및 Ubuntu 기반 Linux 배포판에 설치하려면 다음 명령을 사용합니다.

```bash
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>마이크로 에이전트 인증 방법

Defender for IoT 마이크로 에이전트를 인증하는 데 사용 되는 두 가지 옵션은 다음과 같습니다.

- 모듈 ID 연결 문자열

- 인증서

### <a name="authenticate-using-a-module-identity-connection-string"></a>모듈 ID 연결 문자열을 사용한 인증

이 문서의 [사전 요구 사항](#prerequisites)이 충족되고 이 단계를 시작하기 전에 모듈 ID를 생성했는지 확인합니다.

#### <a name="get-the-module-identity-connection-string"></a>모듈 ID 연결 문자열 가져오기

IoT Hub에서 모듈 ID 연결 문자열을 가져오려면 다음을 수행합니다.

1. IoT Hub으로 이동하여 허브를 선택합니다.

1. 왼쪽 메뉴의 **탐색기** 섹션 아래에서 **IoT 디바이스** 를 선택합니다.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/iot-devices.png" alt-text="왼쪽 메뉴에서 IoT 디바이스를 선택합니다.":::

1. 장치 ID 목록에서 디바이스를 선택하여 **디바이스 세부 정보** 페이지를 표시합니다.

1.  **모듈 ID** 탭을 선택합니다.

1. 디바이스와 연결된 모듈 ID 목록에서 **DefenderIotMicroAgent**  모듈을 선택합니다.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/module-identities.png" alt-text="모듈 ID 탭을 선택합니다.":::

1. **모듈 ID 세부 정보** 페이지에서 **복사** 단추를 선택하여 연결 문자열(기본 키)을 복사합니다.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/copy-button.png" alt-text="복사 단추를 선택하여 연결 문자열(기본 키)을 복사합니다.":::

#### <a name="configure-authentication-using-a-module-identity-connection-string"></a>모듈 ID 연결 문자열을 사용하여 인증 구성

모듈 ID 연결 문자열을 사용하여 인증하도록 에이전트를 구성하려면 다음과 같이 수행합니다.

1. 다음 명령을 입력하여 utf-8로 인코딩된 연결 문자열이 포함된 `connection_string.txt`라는 파일을 Defender for Cloud 에이전트 디렉터리 `/var/defender_iot_micro_agent` 경로에 저장합니다.

    ```bash
    sudo bash -c 'echo "<connection string>" > /var/defender_iot_micro_agent/connection_string.txt'
    ```

    `connection_string.txt`는 다음 경로 위치 `/var/defender_iot_micro_agent/connection_string.txt`에 있어야 합니다.

1. 다음 명령을 사용하여 서비스를 다시 시작합니다.  

    ```bash
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>인증서를 사용하여 인증

인증서를 사용하여 인증하려면 다음을 수행합니다.

1. [이 지침](../../iot-hub/tutorial-x509-scripts.md)에 따라 인증서를 확보합니다.

1. 인증서 및 프라이빗 키의 PEM으로 인코딩된 공개 부분을 Defender for Cloud 에이전트 디렉터리의 `certificate_public.pem` 및 `certificate_private.pem` 파일에 저장합니다.

1. 적절한 연결 문자열을 `connection_string.txt` 파일에 저장합니다. 연결 문자열은 다음과 같아야 합니다.

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true`

    이 문자열은 인증을 위해 인증서가 제공되어야 함을 Defender for Cloud 에이전트에 알립니다.

1. 다음 명령을 사용하여 서비스를 다시 시작합니다.  

    ```bash
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>설치 유효성 검사

설치 유효성을 검사하려면 다음을 수행합니다.

1. 다음 명령을 사용하여 마이크로 에이전트가 제대로 실행 되고 있는지 확인합니다.  

    ```bash
    systemctl status defender-iot-micro-agent.service
    ```

1. 서비스가 `active`인지 확인하여 서비스가 안정적이고, 프로세스의 작동 시간이 적절한지 확인합니다.

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="서비스가 안정적이며 활성 상태인지 확인합니다.":::

## <a name="testing-the-system-end-to-end"></a>엔드투엔드 시스템 테스트

디바이스에 트리거 파일을 만들어 시스템을 끝에서 끝까지 테스트할 수 있습니다. 트리거 파일은 에이전트의 기준 검사에서 해당 파일을 기준 위반으로 감지하도록 합니다.

다음 명령을 사용하여 파일 시스템에 파일을 만듭니다.

```bash
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```

허브에서 CIS-debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0.0의 `CceId`와 함께 기준 유효성 검사 실패 권장 사항이 발생합니다.

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="허브에서 발생하는 기준 유효성 검사 실패 권장 사항입니다." lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

권장 사항이 허브에 표시되기까지 최대 1시간까지 걸릴 수 있습니다.

## <a name="micro-agent-versioning"></a>마이크로 에이전트 버전 관리

특정 버전의 Defender for IoT 마이크로 에이전트를 설치하려면 다음 명령을 실행합니다.

```bash
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: Defender for IoT 마이크로 에이전트 모듈 쌍 생성(미리 보기)](quickstart-create-micro-agent-module-twin.md)
