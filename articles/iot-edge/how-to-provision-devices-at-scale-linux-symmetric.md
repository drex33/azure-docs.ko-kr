---
title: Linux에서 대칭 키를 사용하여 IoT Edge 디바이스 만들기 및 프로비전 - Azure IoT Edge | Microsoft Docs
description: 대칭 키 증명을 사용하여 Device Provisioning Service를 사용하여 Azure IoT Edge에 대한 대규모 Linux 디바이스 프로비저닝 테스트
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c2bae93e325a36b6db28d1a9c1e6e47ee947db39
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705714"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-symmetric-key"></a>대칭 키를 사용하여 Linux에서 대규모로 IoT Edge 디바이스 만들기 및 프로비전

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

이 문서에서는 대칭 키를 사용하여 하나 이상의 Linux IoT Edge 디바이스를 자동으로 프로비전하는 엔드투엔드 지침을 제공합니다. Azure IoT Hub DPS(Device Provisioning Service)를 Azure IoT Edge 디바이스를 자동으로 [프로비전할](../iot-dps/index.yml) 수 있습니다. 자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [프로비전 개요를](../iot-dps/about-iot-dps.md#provisioning-process) 검토하세요.

작업은 다음과 같습니다.

1. 단일 디바이스에 대한 **개별 등록** 또는 디바이스 집합에 대한 **그룹 등록을** 만듭니다.
1. IoT Edge 런타임을 설치하고 IoT Hub에 연결합니다.

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>환경을 단순화하려면 [Azure IoT Edge 구성 도구](https://github.com/azure/iot-edge-config)를 사용해 보세요. 현재 공개 미리 보기로 제공되는 이 명령줄 도구는 장치에 IoT Edge를 설치하고 DPS 및 대칭 키 증명을 사용하여 이를 프로비전합니다.
:::moniker-end

대칭 키 증명은 Device Provisioning Service 인스턴스로 디바이스를 인증하는 간단한 방법입니다. 이 증명 방법은 디바이스 프로비저닝을 처음 사용하는 개발자나 엄격한 보안 요구 사항이 없는 개발자를 위한 "Hello World" 환경을 나타냅니다. [TPM](../iot-dps/concepts-tpm-attestation.md) 또는 [X.509 인증서](../iot-dps/concepts-x509-attestation.md)를 사용하는 디바이스 증명은 더욱 안전하며, 보다 엄격한 보안 요구 사항에 사용해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 IoT Hub
* IoT Edge 디바이스가 될 물리적 또는 가상 Linux 디바이스입니다.
  * 각 디바이스를 식별하려면 *고유한* **등록 ID를** 정의해야 합니다. 디바이스에서 MAC 주소, 일련 번호 또는 고유 정보를 사용할 수 있습니다. 이 예제에서는 등록 ID에 대한 `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` 문자열을 형성하는 MAC 주소와 일련 번호의 조합을 사용합니다. 유효한 문자는 소문자 영숫자 및 대시(`-`)입니다.
* Azure의 IoT Hub Device Provisioning Service의 새 인스턴스로 IoT 허브에 연결되어 있습니다.
  * Device Provisioning Service 인스턴스가 없는 경우 IoT Hub Device Provisioning Service 빠른 시작의 [새 IoT Hub Device Provisioning Service 만들기](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) 및 [IoT Hub 및 Device Provisioning Service 연결](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) 섹션의 지침을 따를 수 있습니다.
  * Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

DPS를 통해 하나 이상의 디바이스를 프로비전하는 등록을 만듭니다.

단일 IoT Edge 디바이스를 프로비전하려는 경우 **개별 등록을** 만듭니다. 여러 디바이스를 프로비전해야 하는 경우 DPS **그룹 등록을** 만드는 단계를 수행합니다.

DPS에서 등록을 만들 때 초기 디바이스 쌍 **상태를** 선언할 수 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

Device Provisioning Service의 등록에 대한 자세한 내용은 [디바이스 등록을 관리하는 방법](../iot-dps/how-to-manage-enrollments.md) 항목을 참조하세요.

# <a name="individual-enrollment"></a>[개별 등록](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>DPS 개별 등록 만들기

> [!TIP]
> 이 문서의 단계는 Azure Portal 위한 것이지만 Azure CLI 사용하여 개별 등록을 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)를 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. 등록을 구성하려면 **개별 등록 추가** 를 선택한 다음, 다음 단계를 완료합니다.  

   1. **메커니즘** 에서 **대칭 키** 를 선택합니다.

   1. 디바이스에 대한 고유한 **등록 ID** 를 제공합니다.

   1. 원하는 경우, 디바이스에 대한 **IoT Hub 디바이스 ID** 를 제공합니다. 디바이스 ID를 사용하여 모듈 배포에 대한 개별 디바이스를 대상으로 할 수 있습니다. 디바이스 ID를 제공하지 않으면 등록 ID가 사용됩니다.

   1. 등록이 IoT Edge 디바이스에 대한 것이라고 선언하려면 **True** 를 선택합니다.

   1. 원하는 경우, **초기 디바이스 쌍 상태** 에 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 예를 들면 다음과 같습니다.

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. **저장** 을 선택합니다.

1. IoT Edge 런타임을 설치할 때 사용할 개별 등록의 **기본 키** 값을 복사합니다.

이제 이 디바이스에 대한 등록이 존재하므로 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비저닝할 수 있습니다.

# <a name="group-enrollment"></a>[그룹 등록](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>DPS 그룹 등록 만들기

> [!TIP]
> 이 문서의 단계는 Azure Portal 위한 것이지만 Azure CLI 사용하여 그룹 등록을 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group)을 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다. 그룹 등록의 경우 모든 디바이스가 IoT Edge 디바이스여야 하며, 그렇지 않은 경우 어떤 디바이스도 등록할 수 없습니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. 등록을 구성하려면 **개별 등록 추가** 를 선택한 다음, 다음 단계를 완료합니다.  

   1. **그룹 이름** 을 제공합니다.

   1. 인증 유형으로 **대칭 키** 를 선택합니다.

   1. 등록이 IoT Edge 디바이스에 대한 것이라고 선언하려면 **True** 를 선택합니다. 그룹 등록의 경우 모든 디바이스가 IoT Edge 디바이스여야 하며, 그렇지 않은 경우 어떤 디바이스도 등록할 수 없습니다.

   1. 원하는 경우, **초기 디바이스 쌍 상태** 에 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 예를 들면 다음과 같습니다.

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. **저장** 을 선택합니다.

1. 그룹 등록에 사용할 장치 키를 만들 때 사용할 등록 그룹의 **기본 키** 값을 복사합니다.

이제 등록 그룹이 존재하기 때문에 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비전할 수 있습니다.

#### <a name="derive-a-device-key"></a>디바이스 키 파생

그룹 등록의 일부로 프로비전된 각 장치에는 프로비전 중에 등록으로 대칭 키 증명을 수행하기 위해 파생된 디바이스 키가 필요합니다.

디바이스 키를 생성하려면 DPS 등록 그룹에서 복사한 키를 사용하여 디바이스에 대한 고유한 등록 ID의 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)을 계산하고 결과를 Base64 형식으로 변환합니다.

> [!IMPORTANT]
> 디바이스 코드에 등록의 기본 또는 보조 키를 포함하지 마세요.

Linux의 경우 openssl을 사용하여 다음 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

**KEY** 의 값을 이전에 적어 둔 **기본 키** 로 바꿉니다.

**REG_ID** 의 값을 디바이스의 등록 ID로 바꿉니다.

```bash
KEY=PASTE_YOUR_ENROLLMENT_KEY_HERE
REG_ID=PASTE_YOUR_REGISTRATION_ID_HERE

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

다음은 파생된 디바이스 키의 샘플 출력입니다.

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

이 섹션에서는 IoT Edge 위해 Linux 가상 머신 또는 물리적 디바이스를 준비합니다. 그런 다음, IoT Edge 설치합니다.

IoT Edge 런타임을 설치할 준비가 되기 전에 디바이스에서 완료해야 하는 두 단계가 있습니다. 디바이스는 Microsoft 설치 패키지에 액세스해야 하며 컨테이너 엔진이 설치되어 있어야 합니다.

### <a name="access-the-microsoft-installation-packages"></a>Microsoft 설치 패키지 액세스

디바이스에 Microsoft 설치 패키지에 대한 액세스 권한이 있어야 합니다.

1. 디바이스의 운영 체제와 일치하는 리포지토리 구성을 설치합니다.

   * **Ubuntu Sever 18.04:**

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
      ```

   * **Raspberry Pi OS Stretch**:

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
      ```

1. 생성된 목록을 sources.list.d 디렉터리에 복사합니다.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Microsoft GPG 공개 키를 설치합니다.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trust.gpg.d/
   ```

> [!NOTE]
> Azure IoT Edge 소프트웨어 패키지에는 각 패키지(`usr/share/doc/{package-name}` 또는 `LICENSE` 디렉터리)에 있는 사용 조건이 적용됩니다. 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치하고 사용하면 이러한 사용 조건에 동의하는 것입니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

### <a name="install-a-container-engine"></a>컨테이너 엔진 설치

Azure IoT Edge는 OCI 호환 컨테이너 런타임을 사용합니다. 프로덕션 시나리오의 경우 Moby 엔진을 사용하는 것이 좋습니다. Moby 엔진은 Azure IoT Edge에서 공식적으로 지원되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

1. 디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

1. Moby 엔진을 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > Moby 컨테이너 엔진 설치 시 오류가 발생하면 Linux 커널의 Moby 호환성을 확인합니다. 일부 포함된 디바이스의 제조업체는 컨테이너 엔진 호환성에 필요한 기능이 없는 사용자 지정 Linux 커널을 포함하는 디바이스 이미지를 제공합니다. Moby에서 제공하는 [check-config 스크립트를](https://github.com/moby/moby/blob/master/contrib/check-config.sh) 사용하는 다음 명령을 실행하여 커널 구성을 확인합니다.
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > 스크립트의 출력에서 `Generally Necessary` 및 `Network Drivers` 아래의 모든 항목을 사용할 수 있는지 확인합니다. 기능이 누락됐다면 소스에서 커널을 다시 빌드하고 적절한 커널 .config 내에 포함하기 위한 관련 모듈을 선택하여 활성화합니다. 마찬가지로 `defconfig` 또는 `menuconfig` 같은 커널 구성 생성기를 사용하는 경우 각각의 기능을 찾아 활성화하고 그에 따라 커널을 다시 빌드합니다. 새로 수정된 커널을 배포한 후에는 check-config 스크립트를 다시 실행하여 필요한 모든 기능이 성공적으로 활성화되었는지 확인합니다.

### <a name="install-iot-edge"></a>IoT Edge 설치

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge 보안 디먼은 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

이 섹션의 단계는 인터넷에 연결되어 있는 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 나타냅니다. 특정 버전(예: 릴리스 전 버전)을 설치해야 하거나 오프라인 상태에서 설치해야 하는 경우 [오프라인 또는 특정 버전 설치 단계를 수행합니다.](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional)

1. 디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

1. **libiothsm-std** 패키지와 함께 IoT Edge 버전 1.1*을 설치합니다.

   ```bash
   sudo apt-get install iotedge
   ```

   > [!NOTE]
   > *IoT Edge 버전 1.1은 IoT Edge 장기 지원 분기입니다. 이전 버전을 실행하는 경우 이전 버전이 더 이상 지원되지 않는 최신 패치를 설치하거나 최신 패치로 업데이트하는 것이 좋습니다.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 서비스는 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 서비스는 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

IoT ID 서비스는 IoT Edge 버전 1.2와 함께 도입되었습니다. 이 서비스는 IoT Edge 및 IoT Hub와 통신해야 하는 기타 디바이스 구성 요소에 대한 ID 프로비저닝 및 관리를 다룹니다.

이 섹션의 단계는 인터넷에 연결된 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 보여 줍니다. 특정 버전(예: 릴리스 전 버전)을 설치해야 하거나 오프라인 상태에서 설치해야 하는 경우 [오프라인 또는 특정 버전 설치 단계를 수행합니다.](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional)

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

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-the-device-with-provisioning-information"></a>프로비저닝 정보를 사용하여 디바이스 구성

런타임이 디바이스에 설치되면 디바이스에서 Device Provisioning Service 및 IoT Hub에 연결하는 데 사용하는 정보로 디바이스를 구성합니다.

다음 정보를 준비합니다.

* DPS **ID 범위** 값
* 만든 디바이스 **등록 ID**
* 개별 등록의 **기본 키** 또는 그룹 등록을 사용하는 장치의 [파생 키](#derive-a-device-key) 중 하나입니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. IoT Edge 디바이스에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 파일의 프로비저닝 구성 섹션을 찾습니다. DPS 대칭 키 프로비저닝에 대한 줄을 주석 처리 제거하고 다른 프로비저닝 줄이 주석으로 처리되어 있는지 확인합니다.

   `provisioning:` 줄은 앞에 공백이 없어야 하며 중첩 항목은 두 개의 공백으로 들여써야 합니다.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "PASTE_YOUR_SCOPE_ID_HERE"
     attestation:
       method: "symmetric_key"
       registration_id: "PASTE_YOUR_REGISTRATION_ID_HERE"
       symmetric_key: "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. `scope_id`, `registration_id` 및 `symmetric_key`의 값을 DPS 및 디바이스 정보로 업데이트합니다.

1. 필요에 따라 `always_reprovision_on_startup` 또는 `dynamic_reprovisioning` 줄을 사용하여 디바이스의 다시 프로비저닝 동작을 구성합니다. 시작 시 디바이스가 다시 프로비저닝되도록 설정되어 있는 경우 항상 먼저 DPS를 사용하여 프로비저닝을 시도한 다음, 실패하는 경우 프로비저닝 백업으로 대체합니다. 디바이스가 동적으로 다시 프로비저닝되도록 설정된 경우 다시 프로비저닝 이벤트가 감지되면 IoT Edge가 다시 시작되고 다시 프로비저닝됩니다. 자세한 내용은 [IoT Hub 디바이스 다시 프로비저닝 개념](../iot-dps/concepts-device-reprovision.md)을 참조하세요.

1. 디바이스에서 한 모든 구성 변경을 선택하도록 IoT Edge 런타임을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. IoT Edge 설치의 일부로 제공되는 템플릿 파일을 기반으로 디바이스에 대한 구성 파일을 만듭니다.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. IoT Edge 디바이스에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. 파일의 **프로비전** 섹션을 찾습니다. 대칭 키를 통한 DPS 프로비저닝에 대한 줄을 주석 처리 제거하고 다른 프로비저닝 줄이 주석으로 처리되어 있는지 확인합니다.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "PASTE_YOUR_SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "PASTE_YOUR_REGISTRATION_ID_HERE"

   symmetric_key = "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   ```

1. `id_scope`, `registration_id` 및 `symmetric_key`의 값을 DPS 및 디바이스 정보로 업데이트합니다.

   대칭 키 매개 변수로 인라인 키, 파일 URI 또는 PKCS#11 URI 값을 사용할 수 있습니다. 사용 중인 형식을 기준으로 대칭 키 한 줄을 주석 처리 제거합니다.

   PKCS#11 URI를 사용하는 경우 구성 파일에서 **PKCS#11** 섹션을 찾고 PKCS#11 구성에 대한 정보를 제공합니다.

1. config.toml 파일을 저장하고 닫습니다.

1. IoT Edge에 대한 구성 변경 내용을 적용합니다.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하고 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다.

# <a name="individual-enrollment"></a>[개별 등록](#tab/individual-enrollment)

Device Provisioning Service에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure Portal에서 Device Provisioning Service 인스턴스로 이동합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

# <a name="group-enrollment"></a>[그룹 등록](#tab/group-enrollment)

Device Provisioning Service에서 만든 그룹 등록이 사용되었는지 확인할 수 있습니다. Azure Portal에서 Device Provisioning Service 인스턴스로 이동합니다. 만든 그룹 등록에 대한 등록 세부 정보를 엽니다. **등록 레코드** 탭으로 이동하여 해당 그룹에 등록된 모든 디바이스를 봅니다.

---

디바이스에서 다음 명령을 사용하여 IoT Edge가 성공적으로 설치되고 시작되는지 확인합니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge 서비스의 상태를 확인합니다.

```cmd/sh
systemctl status iotedge
```

서비스 로그를 검사합니다.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

실행 중인 모듈을 나열합니다.

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 서비스의 상태를 확인합니다.

```cmd/sh
sudo iotedge system status
```

서비스 로그를 검사합니다.

```cmd/sh
sudo iotedge system logs
```

실행 중인 모듈을 나열합니다.

```cmd/sh
sudo iotedge list
```

:::moniker-end

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 디바이스를 프로비전할 때 디바이스 ID 및 디바이스 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-cli-at-scale.md)하거나 [Azure Portal을 사용하여 대규모로 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-at-scale.md)하는 방법을 알아봅니다.
