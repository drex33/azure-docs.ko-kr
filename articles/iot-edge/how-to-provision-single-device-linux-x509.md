---
title: x.509 인증서를 사용 하 여 Linux에서 IoT Edge 장치 만들기 및 프로 비전-Azure IoT Edge | Microsoft Docs
description: X.509 인증서를 사용한 수동 프로 비전을 위해 IoT Hub에서 단일 IoT Edge 장치 만들기 및 프로 비전
author: kgremban
ms.reviewer: v-tcassi
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: kgremban
ms.openlocfilehash: 1f1d6a627baf4166a34680961e1601307292bb3b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845090"
---
# <a name="create-and-provision-an-iot-edge-device-on-linux-using-x509-certificates"></a>X.509 인증서를 사용 하 여 Linux에서 IoT Edge 장치 만들기 및 프로 비전

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

이 문서에서는 IoT Edge 설치를 포함 하 여 Linux IoT Edge 장치를 등록 하 고 프로 비전 하는 종단 간 지침을 제공 합니다.

IoT 허브에 연결하는 모든 디바이스에는 클라우드-디바이스 또는 디바이스-클라우드 통신을 추적하는 데 사용되는 디바이스 ID가 있습니다. IoT 허브 호스트 이름, 디바이스 ID, 디바이스에서 IoT Hub 인증에 사용하는 정보를 포함하는 연결 정보로 디바이스를 구성합니다.

이 문서의 단계는 단일 디바이스를 IoT 허브에 연결하는 수동 프로비저닝이라는 프로세스를 안내합니다. 수동 프로비저닝을 위해 IoT Edge 디바이스를 인증하는 두 가지 옵션이 있습니다.

* **대칭 키**: IoT Hub에서 새 장치 id를 만들면 서비스에서 두 개의 키를 만듭니다. 디바이스에 키 중 하나를 저장하고 인증 시 IoT Hub 키를 제시합니다.

  이 인증 방식을 사용하면 빠르게 시작할 수 있지만 안전하지 않습니다.

* **X.509 자체 서명**: 두 개의 x.509 ID 인증서를 만들어 디바이스에 저장합니다. IoT Hub에서 새 디바이스 ID를 만들 때 두 인증서의 지문을 모두 제공합니다. 디바이스는 IoT Hub에 인증 시 인증서 하나를 제시되고 IoT Hub는 인증서가 지문과 일치하는지 확인합니다.

  이 인증 방법은 보다 안전 하며 프로덕션 시나리오에 권장 됩니다.

이 문서에서는 인증 방법으로 x.509 인증서를 사용 하는 방법을 설명 합니다. 대칭 키를 사용 하려면 [Linux에서 대칭 키를 사용 하 여 IoT Edge 장치 만들기 및 프로 비전](how-to-provision-single-device-linux-symmetric.md)을 참조 하세요.

> [!NOTE]
> 설정할 장치가 많고 각 장치를 수동으로 프로 비전 하지 않으려는 경우 다음 문서 중 하나를 사용 하 여 IoT Edge IoT Hub 장치 프로 비전 서비스에서 작동 하는 방법을 알아봅니다.
>
> * [X.509 인증서를 사용 하 여 대규모로 IoT Edge 장치 만들기 및 프로 비전](how-to-provision-devices-at-scale-linux-x509.md)
> * [TPM을 사용 하 여 대규모로 IoT Edge 장치 만들기 및 프로 비전](how-to-provision-devices-at-scale-linux-tpm.md)
> * [대칭 키를 사용 하 여 대규모로 IoT Edge 장치 만들기 및 프로 비전](how-to-provision-devices-at-scale-linux-symmetric.md)

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 IoT Edge 장치를 등록 하 고 IoT Edge 설치 하는 방법을 설명 합니다. 이러한 작업을 수행 하는 데 사용 되는 다양 한 필수 조건 및 유틸리티가 있습니다. 계속 하기 전에 모든 필수 구성 요소가 포함 되어 있는지 확인 합니다.

<!-- Device registration prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-register-device.md](../../includes/iot-edge-prerequisites-register-device.md)]

### <a name="iot-edge-installation"></a>IoT Edge 설치

X64, ARM32 또는 ARM64 Linux 장치입니다.

Microsoft는 Ubuntu Server 18.04 및 Raspberry Pi OS Stretch 운영 체제용 설치 패키지를 제공합니다.

현재 프로덕션 시나리오에 대해 지원 되는 운영 체제에 대 한 최신 정보는 [Azure IoT Edge 지원 시스템](support.md#operating-systems)을 참조 하세요.

>[!NOTE]
>ARM64 디바이스 지원은 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)로 제공됩니다.

<!-- Generate device identity certificates H2 and content -->
[!INCLUDE [iot-edge-generate-device-identity-certs.md](../../includes/iot-edge-generate-device-identity-certs.md)]

<!-- Register your device and View provisioning information H2s and content -->
[!INCLUDE [iot-edge-register-device-x509.md](../../includes/iot-edge-register-device-x509.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>클라우드 ID를 사용한 디바이스 프로비전

이제 컨테이너 엔진과 IoT Edge 런타임이 디바이스에 설치되었으므로 다음 단계를 수행할 수 있습니다. 이 단계에서는 클라우드 ID 및 인증 정보를 사용하여 디바이스를 설정합니다.

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
       iothub_hostname: "REQUIRED_IOTHUB_HOSTNAME"
       device_id: "REQUIRED_DEVICE_ID_PROVISIONED_IN_IOTHUB"
       identity_cert: "REQUIRED_URI_TO_DEVICE_IDENTITY_CERTIFICATE"
       identity_pk: "REQUIRED_URI_TO_DEVICE_IDENTITY_PRIVATE_KEY"
   ```

다음 필드를 업데이트합니다.

* **iothub_hostname**: 디바이스가 연결될 IoT 허브의 호스트 이름입니다. 예들 들어 `{IoT hub name}.azure-devices.net`입니다.
* **device_id**: 디바이스를 등록할 때 제공한 ID입니다.
* **identity_cert**: 디바이스의 ID 인증서에 대한 URI입니다. 예들 들어 `file:///path/identity_certificate.pem`입니다.
* **identity_pk**: 제공된 ID 인증서의 프라이빗 키 파일에 대한 URI입니다. 예들 들어 `file:///path/identity_key.pem`입니다.

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
   iothub_hostname = "REQUIRED_IOTHUB_HOSTNAME"
   device_id = "REQUIRED_DEVICE_ID_PROVISIONED_IN_IOTHUB"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "REQUIRED_URI_OR_POINTER_TO_DEVICE_IDENTITY_CERTIFICATE"

   identity_pk = "REQUIRED_URI_TO_DEVICE_IDENTITY_PRIVATE_KEY"
   ```

다음 필드를 업데이트합니다.

* **iothub_hostname**: 디바이스가 연결될 IoT 허브의 호스트 이름입니다. 예들 들어 `{IoT hub name}.azure-devices.net`입니다.
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

새 IoT Edge 디바이스를 만들면 Azure Portal에 상태 코드 `417 -- The device's deployment configuration is not set`이 표시됩니다. 이 상태는 정상이며 디바이스가 모듈 배포를 수신할 준비가 되었음을 의미합니다.

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
      curl -L libiothsm-std_link_here -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. IoT Edge 디바이스의 아키텍처와 일치하는 **iotedge** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다.

   4. 다음 명령의 복사된 링크를 사용하여 해당 버전의 IoT Edge 보안 디먼을 설치합니다.

      ```bash
      curl -L iotedge_link_here -o iotedge.deb && sudo apt-get install ./iotedge.deb
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
