---
title: Azure IoT Edge 설치 | Microsoft Docs
description: Windows 또는 Linux 디바이스용 Azure IoT Edge 설치 지침
author: kgremban
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kgremban
ms.openlocfilehash: 0f0cf8f4ce8f82964a69635debacea772c4310da
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529368"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Linux용 Azure IoT Edge 설치 또는 제거

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. 디바이스가 IoT Edge 런타임을 사용하여 구성되면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다. 자세한 내용은 [Azure IoT Edge 런타임 및 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에는 Linux 디바이스에 Azure IoT Edge 런타임을 설치하는 단계가 나와 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [등록된 디바이스 ID](how-to-register-device.md)

  대칭 키 인증을 사용하여 디바이스를 등록한 경우 디바이스 연결 문자열을 준비합니다.

  X.509 자체 서명된 인증서 인증을 사용하여 디바이스를 등록한 경우 디바이스를 등록하는 데 사용한 ID 인증서들 중 최소 한 개 및 그와 일치하는 프라이빗 키가 디바이스에 있어야 합니다.

* Linux 디바이스

  X64, ARM32 또는 ARM64 Linux 디바이스가 있어야 합니다. Microsoft는 Ubuntu Server 18.04 및 Raspberry Pi OS Stretch 운영 체제용 설치 패키지를 제공합니다.

  현재 프로덕션 시나리오에 지원되는 운영 체제에 대한 최신 정보는 [Azure IoT Edge 지원 시스템](support.md#operating-systems)을 참조하세요.

  >[!NOTE]
  >ARM64 디바이스 지원은 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)로 제공됩니다.

* Microsoft 설치 패키지에 액세스할 수 있도록 디바이스를 준비합니다.

  디바이스 운영 체제와 일치하는 리포지토리 구성을 설치합니다.

  * **Ubuntu Server 18.04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Raspberry Pi OS Stretch**:

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  생성된 목록을 sources.list.d 디렉터리에 복사합니다.

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Microsoft GPG 공개 키를 설치합니다.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Azure IoT Edge 소프트웨어 패키지에는 각 패키지(`usr/share/doc/{package-name}` 또는 `LICENSE` 디렉터리)에 있는 사용 조건이 적용됩니다. 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치하고 사용하면 이러한 사용 조건에 동의하는 것입니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

## <a name="install-a-container-engine"></a>컨테이너 엔진 설치

Azure IoT Edge는 OCI 호환 컨테이너 런타임을 사용합니다. 프로덕션 시나리오의 경우 Moby 엔진을 사용하는 것이 좋습니다. Moby 엔진은 Azure IoT Edge에서 공식적으로 지원되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

Moby 엔진을 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

Moby 컨테이너 엔진 설치 시 오류가 발생하면 Linux 커널의 Moby 호환성을 확인합니다. 일부 포함된 디바이스의 제조업체는 컨테이너 엔진 호환성에 필요한 기능이 없는 사용자 지정 Linux 커널을 포함하는 디바이스 이미지를 제공합니다. Moby에서 제공한 [check-config 스크립트](https://github.com/moby/moby/blob/master/contrib/check-config.sh)를 사용하는 다음 명령을 실행하여 커널 구성을 확인합니다.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

스크립트의 출력에서 `Generally Necessary` 및 `Network Drivers` 아래의 모든 항목을 사용할 수 있는지 확인합니다. 기능이 누락됐다면 소스에서 커널을 다시 빌드하고 적절한 커널 .config 내에 포함하기 위한 관련 모듈을 선택하여 활성화합니다. 마찬가지로 `defconfig` 또는 `menuconfig` 같은 커널 구성 생성기를 사용하는 경우 각각의 기능을 찾아 활성화하고 그에 따라 커널을 다시 빌드합니다. 새로 수정된 커널을 배포한 후에는 check-config 스크립트를 다시 실행하여 필요한 모든 기능이 성공적으로 활성화되었는지 확인합니다.

## <a name="install-iot-edge"></a>IoT Edge 설치

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge 보안 디먼은 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

이 섹션의 단계는 인터넷에 연결된 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 보여 줍니다. 시험판 버전과 같은 특정 버전을 설치해야 하거나 오프라인 상태에서 설치해야 하는 경우 이 문서 뒷부분의 [오프라인 또는 특정 버전 설치](#offline-or-specific-version-installation-optional) 단계를 따르세요.

디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

**libiothsm-std** 패키지와 함께 IoT Edge 버번 1.1.*을 설치합니다.

   ```bash
   sudo apt-get install iotedge
   ```

>[!NOTE]
>IoT Edge 버전 1.1은 IoT Edge의 장기 지원 분기입니다. 이전 버전을 실행하는 경우 이전 버전이 더 이상 지원되지 않으므로 최신 패치를 설치하거나 업데이트하는 것이 좋습니다.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge 서비스는 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 서비스는 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

IoT ID 서비스는 IoT Edge 버전 1.2와 함께 도입되었습니다. 이 서비스는 IoT Edge 및 IoT Hub와 통신해야 하는 기타 디바이스 구성 요소에 대한 ID 프로비저닝 및 관리를 다룹니다.

이 섹션의 단계는 인터넷에 연결된 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 보여 줍니다. 시험판 버전과 같은 특정 버전을 설치해야 하거나 오프라인 상태에서 설치해야 하는 경우 이 문서 뒷부분의 [오프라인 또는 특정 버전 설치](#offline-or-specific-version-installation-optional) 단계를 따르세요.

>[!NOTE]
>이 섹션의 단계에서는 IoT Edge 버전 1.2를 설치하는 방법을 보여줍니다.
>
>이전 버전을 실행하는 IoT Edge 디바이스가 이미 있고 1.2로 업그레이드하려는 경우 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)의 단계를 사용합니다. 버전 1.2는 업그레이드에 특정 단계가 필요한 이전 버전의 IoT Edge와 충분히 다릅니다.

디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

사용할 수 있는 IoT Edge 및 IoT ID 서비스 버전을 확인합니다.

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

최신 버전의 IoT Edge 및 IoT id 서비스 패키지를 설치하려면 다음 명령을 사용합니다.

   ```bash
   sudo apt-get install aziot-edge
   ```

또는 최신 버전이 아닌 다른 IoT Edge 버전을 설치하도록 선택한 경우 `aziot-edge` 및 `aziot-identity-service` 서비스 모두에 동일한 버전을 설치해야 합니다.

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>클라우드 ID를 사용한 디바이스 프로비전

이제 컨테이너 엔진과 IoT Edge 런타임이 디바이스에 설치되었으므로 다음 단계를 수행할 수 있습니다. 이 단계에서는 클라우드 ID 및 인증 정보를 사용하여 디바이스를 설정합니다.

사용하려는 인증 유형에 따라 다음 섹션을 선택합니다.

* [옵션 1: 대칭 키를 사용하여 인증](#option-1-authenticate-with-symmetric-keys)
* [옵션 2: X.509 인증서를 사용하여 인증](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>옵션 1: 대칭 키를 사용하여 인증

이 시점에서 IoT Edge 런타임은 Linux 디바이스에 설치되고, 클라우드 ID 및 인증 정보를 사용하여 디바이스를 프로비전해야 합니다.

이 섹션에서는 대칭 키 인증을 사용하여 디바이스를 프로비전하는 단계를 안내합니다. IoT Hub에 디바이스를 등록했고 디바이스 정보에서 연결 문자열 검색을 완료한 상태여야 합니다. 그러지 않은 경우 [IoT Hub에서 IoT Edge 디바이스 등록](how-to-register-device.md) 단계를 따릅니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge 디바이스에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

파일의 프로비전 구성을 찾고 **Manual provisioning configuration using a connection string** 섹션의 주석 처리 제거가 되어 있지 않은 경우에 주석 처리 제거합니다.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

**device_connection_string** 의 값을 IoT Edge 디바이스의 연결 문자열로 업데이트합니다. 다른 프로비전 섹션이 주석 처리되었는지 확인합니다. **provisioning:** 줄 앞에 공백이 없고 중첩된 항목은 공백 두 칸으로 들여쓰기 되어 있는지 확인합니다.

클립보드 내용을 Nano에 붙여 넣으려면 `Shift+Right Click` 또는 `Shift+Insert` 키를 누릅니다.

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge 설치의 일부로 제공되는 템플릿 파일을 기반으로 디바이스의 구성 파일을 만듭니다.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

IoT Edge 디바이스에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

파일의 **Provisioning** 섹션을 찾고 연결 문자열 줄로 수동 프로비전의 주석 처리를 제거합니다.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string = "<ADD DEVICE CONNECTION STRING HERE>"
   ```

**connection_string** 의 값을 IoT Edge 디바이스의 연결 문자열로 업데이트합니다.

클립보드 내용을 Nano에 붙여 넣으려면 `Shift+Right Click` 또는 `Shift+Insert` 키를 누릅니다.

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에 프로비전 정보를 입력한 후 변경 내용을 적용합니다.

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>옵션 2: X.509 인증서를 사용하여 인증

이 시점에서 IoT Edge 런타임은 Linux 디바이스에 설치되고, 클라우드 ID 및 인증 정보를 사용하여 디바이스를 프로비전해야 합니다.

이 섹션에서는 X.509 인증서 인증을 사용하여 디바이스를 프로비전하는 단계를 안내합니다. 디바이스를 IoT Hub에 등록했고 IoT Edge 디바이스에 있는 인증서 및 프라이빗 키와 일치하는 지문의 제공을 완료한 상태여야 합니다. 그렇지 않은 경우 [IoT Hub에서 IoT Edge 디바이스 등록](how-to-register-device.md) 단계를 따릅니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge 디바이스에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

파일의 프로비전 구성을 찾고 **Manual provisioning configuration using an X.509 identity certificate** 섹션의 주석 처리를 제거합니다. 다른 프로비전 섹션이 주석 처리되었는지 확인합니다. **provisioning:** 줄 앞에 공백이 없고 중첩된 항목은 공백 두 칸으로 들여쓰기 되어 있는지 확인합니다.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

다음 필드를 업데이트합니다.

* **iothub_hostname**: 디바이스가 연결될 IoT 허브의 호스트 이름입니다. 예: `{IoT hub name}.azure-devices.net`.
* **device_id**: 디바이스를 등록할 때 제공한 ID입니다.
* **identity_cert**: 디바이스의 ID 인증서에 대한 URI입니다. 예: `file:///path/identity_certificate.pem`.
* **identity_pk**: 제공된 ID 인증서의 프라이빗 키 파일에 대한 URI입니다. 예: `file:///path/identity_key.pem`.

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge 설치의 일부로 제공되는 템플릿 파일을 기반으로 디바이스의 구성 파일을 만듭니다.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

IoT Edge 디바이스에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

파일의 **Provisioning** 섹션을 찾고 x.509 ID 인증서를 사용하여 수동 프로비전 줄의 주석 처리를 제거합니다. 다른 모든 프로비전 섹션을 주석으로 처리해야 합니다.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

다음 필드를 업데이트합니다.

* **iothub_hostname**: 디바이스가 연결될 IoT 허브의 호스트 이름입니다. 예: `{IoT hub name}.azure-devices.net`.
* **device_id**: 디바이스를 등록할 때 제공한 ID입니다.
* **identity_cert**: 디바이스의 ID 인증서에 대한 URI입니다(예: `file:///path/identity_certificate.pem`). 또는 EST나 로컬 인증 기관을 사용하여 동적으로 인증서를 발급합니다.
* **identity_pk**: 제공된 ID 인증서의 프라이빗 키 파일에 대한 URI입니다(예: )`file:///path/identity_key.pem`. 또는 PKCS#11 URI를 제공한 다음 나중에 **PKCS#11** 섹션에 있는 구성 정보를 구성 파일에 제공합니다.

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에 프로비전 정보를 입력한 후 변경 내용을 적용합니다.

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>구성 성공 확인

IoT Edge 디바이스에서 런타임이 성공적으로 설치 및 구성되었는지 확인합니다.

>[!TIP]
>`iotedge` 명령을 실행하려면 상승된 권한이 필요합니다. IoT Edge 런타임을 설치한 후 처음으로 머신에서 로그아웃했다가 다시 로그인하면 권한이 자동으로 업데이트됩니다. 그 전까지는 명령 앞에 `sudo`를 사용합니다.

IoT Edge 시스템 서비스가 실행되고 있는지 확인합니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

성공적인 상태 응답은 `Ok`입니다.

::: moniker-end

서비스 문제를 해결해야 할 경우 서비스 로그를 검색합니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

`check` 도구를 사용하여 디바이스의 구성 및 연결 상태를 확인합니다.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>사용 권한이 업데이트된 후에도 항상 `sudo`를 사용하여 검사 도구를 실행합니다. 검사 도구가 구성 상태를 확인하기 위해 구성 파일에 액세스하려면 상승된 권한이 필요합니다.

IoT Edge 디바이스에서 실행되는 모든 모듈을 봅니다. 서비스가 처음 시작되면 **edgeAgent** 모듈만 실행되는 것으로 표시됩니다. edgeAgent 모듈은 기본적으로 실행되며, 디바이스에 배포하는 추가 모듈을 설치하고 시작하는 데 도움이 됩니다.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>오프라인 설치 또는 특정 버전 설치(선택 사항)

이 섹션의 단계는 표준 설치 단계에서 다루지 않는 시나리오를 위한 단계입니다. 다음을 포함할 수 있습니다.

* 오프라인 상태에서 IoT Edge 설치
* 릴리스 후보 버전 설치

`apt-get install`을 통해 제공되지 않는 특정 버전의 Azure IoT Edge 런타임을 설치하려는 경우 이 섹션의 단계를 사용합니다. Microsoft 패키지 목록에는 제한된 최신 버전과 해당 하위 버전의 집합만 포함되어 있으므로 이러한 단계는 이전 버전 또는 릴리스 후보 버전을 설치하려는 모든 사용자에게 해당합니다.

curl 명령을 사용하면 IoT Edge GitHub 리포지토리에서 직접 구성 요소 파일을 대상으로 지정할 수 있습니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)로 이동하고 대상으로 지정할 릴리스 버전을 찾습니다.

2. 해당 버전의 **자산** 섹션을 확장합니다.

3. 모든 릴리스에는 IoT Edge 보안 디먼 및 hsmlib에 대한 새 파일이 있어야 합니다. 오프라인 디바이스에 IoT Edge를 설치하려는 경우 미리 새 파일을 다운로드합니다. 그렇지 않으면 다음 명령을 사용하여 해당 구성 요소를 업데이트합니다.

   1. IoT Edge 디바이스의 아키텍처와 일치하는 **libiothsm-std** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다.

   2. 다음 명령의 복사된 링크를 사용하여 해당 버전의 hsmlib를 설치합니다.

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. IoT Edge 디바이스의 아키텍처와 일치하는 **iotedge** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다.

   4. 다음 명령의 복사된 링크를 사용하여 해당 버전의 IoT Edge 보안 디먼을 설치합니다.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>디바이스가 현재 IoT Edge 버전 1.1 이상을 실행하고 있는 경우 이 섹션의 단계를 수행하기 전에 **iotedge** 및 **libiothsm-std** 패키지를 제거합니다. 자세한 내용은 [1.0 또는 1.1에서 1.2로 업데이트](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)를 참조하세요.

1. [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)로 이동하고 대상으로 지정할 릴리스 버전을 찾습니다.

2. 해당 버전의 **자산** 섹션을 확장합니다.

3. 모든 릴리스에는 IoT Edge 및 ID 서비스에 대한 새 파일이 있어야 합니다. 오프라인 디바이스에 IoT Edge를 설치하려는 경우 미리 새 파일을 다운로드합니다. 그렇지 않으면 다음 명령을 사용하여 해당 구성 요소를 업데이트합니다.

   1. IoT Edge 디바이스의 아키텍처와 일치하는 **aziot-identity-service** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다.

   2. 다음 명령의 복사된 링크를 사용하여 해당 버전의 ID 서비스를 설치합니다.

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. IoT Edge 디바이스의 아키텍처와 일치하는 **aziot-edge** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다.

   4. 다음 명령의 복사된 링크를 사용하여 해당 버전의 IoT Edge를 설치합니다.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

컨테이너 엔진과 IoT Edge 런타임이 디바이스에 설치되었으므로 다음 단계를 수행할 준비가 되었습니다. 이 단계에서는 [클라우드 ID를 사용하여 디바이스를 프로비전](#provision-the-device-with-its-cloud-identity)합니다.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

디바이스에서 IoT Edge 설치를 제거하려면 다음 명령을 사용합니다.

IoT Edge 런타임을 제거합니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

구성 파일을 포함하여 IoT Edge와 연결된 모든 파일을 삭제하려면 `--purge` 플래그를 사용합니다. IoT Edge를 다시 설치하고 나중에 동일한 구성 정보를 사용하려면 이 플래그를 생략합니다.

IoT Edge 런타임을 제거하면 생성된 컨테이너는 중지되지만 디바이스에는 계속 남아 있습니다. 모든 컨테이너를 보고 남아 있는 항목을 확인합니다.

```bash
sudo docker ps -a
```

두 개의 런타임 컨테이너를 포함하여 디바이스에서 컨테이너를 삭제합니다.

```bash
sudo docker rm -f <container name>
```

마지막으로 디바이스에서 컨테이너 런타임을 제거합니다.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>다음 단계

디바이스에 모듈을 배포하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md)를 계속 진행하세요.
