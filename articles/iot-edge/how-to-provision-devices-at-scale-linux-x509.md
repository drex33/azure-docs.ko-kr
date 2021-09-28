---
title: Linux에서 x.509 인증서를 사용 하 여 대규모로 IoT Edge 장치 만들기 및 프로 비전-Azure IoT Edge | Microsoft Docs
description: x.509 인증서를 사용 하 여 장치 프로 비전 서비스를 통해 Azure IoT Edge에 대 한 대규모로 프로 비전 장치 테스트
author: v-tcassi
ms.author: v-tcassi
ms.date: 08/12/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2dc45c3b088dc25a39496280b1e612a27c9dd69d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705694"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-x509-certificates"></a>X.509 인증서를 사용 하 여 Linux에서 대규모로 IoT Edge 장치 만들기 및 프로 비전

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

이 문서에서는 x.509 인증서를 사용 하 여 하나 이상의 Linux IoT Edge 장치를 자동 프로 비전 하기 위한 종단 간 지침을 제공 합니다. [Azure IoT 허브 장치 프로 비전 서비스](../iot-dps/index.yml) (DPS)를 사용 하 여 Azure IoT에 지 장치를 자동으로 프로 비전 할 수 있습니다. 자동 프로 비전 프로세스에 익숙하지 않은 경우 계속 하기 전에 [프로 비전 개요](../iot-dps/about-iot-dps.md#provisioning-process) 를 검토 합니다.

작업은 다음과 같습니다.

1. 인증서 및 키를 생성합니다.
1. 단일 장치에 대 한 **개별 등록** 또는 장치 집합에 대 한 **그룹 등록** 을 만듭니다.
1. IoT Edge 런타임을 설치하고 IoT Hub를 사용하여 디바이스를 등록합니다.

X.509 인증서를 증명 메커니즘으로 사용하면 프로덕션의 크기를 조정하고 디바이스 프로비전을 간소화할 수 있습니다. 일반적으로 X.509 인증서는 신뢰할 수 있는 인증서 체인에 정렬됩니다. 자체 서명되었거나 신뢰할 수 있는 루트 인증서부터 시작하여, 체인에 존재하는 각 인증서는 그다음으로 낮은 인증서에 서명합니다. 이 패턴을 통해 루트 인증서부터 시작하여 각 중간 인증서를 거쳐 디바이스에 설치된 마지막 “리프” 인증서에서 완료되는 위임된 신뢰 체인이 만들어집니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 IoT Hub
* IoT Edge 장치를 사용할 수 있는 물리적 또는 가상 Linux 장치입니다.
* 최신 버전의 [Git](https://git-scm.com/download/)이 설치되어 있습니다.
* Azure의 IoT Hub Device Provisioning Service의 새 인스턴스로 IoT 허브에 연결되어 있습니다.
  * 장치 프로 비전 서비스 인스턴스가 없는 경우 [새 IoT Hub Device Provisioning Service 만들기](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) 의 지침에 따라 IoT Hub Device Provisioning Service 빠른 시작의 [IoT Hub 및 장치 프로 비전 서비스를 연결할](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) 수 있습니다.
  * Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

## <a name="generate-device-identity-certificates"></a>디바이스 ID 인증서 생성

디바이스 ID 인증서는 인증서 신뢰 체인을 통해 상위 X.509 인증 기관(CA) 인증서에 연결하는 리프 인증서입니다. 장치 id 인증서에는 장치를 IoT hub에 포함 하려는 장치 ID로 설정 된 CN (일반 이름)이 있어야 합니다.

디바이스 ID 인증서는 IoT Edge 디바이스를 프로비저닝하고 Azure IoT Hub 디바이스를 인증하는 데만 사용됩니다. 해당 인증서는 IoT Edge 디바이스가 모듈이나 리프 디바이스에 확인을 위해 제공하는 CA 인증서와 달리 서명 인증서가 아닙니다. 자세한 내용은 [Azure IoT Edge 인증서 사용 현황 세부 정보](iot-edge-certs.md)를 참조하세요.

디바이스 ID 인증서를 만든 후에는 2개의 파일이 있어야 합니다. 이는 인증서의 퍼블릭 부분을 포함하는 .cer 또는 .pem 파일 및 인증서의 프라이빗 키를 포함하는 .cer 또는 .pem 파일입니다. DPS에서 그룹 등록을 사용하려면 동일한 인증서 신뢰 체인에 중간 또는 루트 CA 인증서의 퍼블릭 부분도 있어야 합니다.

X.509를 사용하여 자동 프로비저닝을 설정하려면 다음 파일이 필요합니다.

* 디바이스 ID 인증서 및 해당 인증서의 프라이빗 키 인증서. 개별 등록을 만들면 디바이스 ID 인증서가 DPS에 업로드됩니다. 프라이빗 키가 IoT Edge 런타임에 전달됩니다.
* 최소한 디바이스 ID와 중간 인증서를 포함해야 하는 전체 체인 인증서. 전체 체인 인증서는 IoT Edge 런타임에 전달됩니다.
* 인증서 신뢰 체인의 중간 또는 루트 CA 인증서. 이 인증서는 그룹 등록을 만들면 DPS에 업로드됩니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> 현재 libiothsm의 제한으로 인해 2038년 1월 1일 이후에 만료되는 인증서는 사용할 수 없습니다.

:::moniker-end
<!-- end 1.1 -->

### <a name="use-test-certificates-optional"></a>테스트 인증서 사용(선택 사항)

새 ID 인증서를 만드는 데 사용할 수 있는 인증 기관이 없지만 이 시나리오를 사용해 보려는 경우, Azure IoT Edge Git 리포지토리에 테스트 인증서 생성에 사용할 수 있는 스크립트가 포함되어 있습니다. 해당 인증서는 개발 테스트 전용으로 설계되었으며 프로덕션 단계에서 사용해서는 안 됩니다.

테스트 인증서를 만들려면 [데모 인증서를 만들어 IoT Edge 디바이스 기능 테스트](how-to-create-test-certificates.md)의 단계를 따릅니다. 인증서 생성 스크립트를 설정하고 루트 CA 인증서를 만들기 위한 두 개의 필수 섹션을 완료합니다. 그런 다음 단계를 따라 디바이스 ID 인증서를 만듭니다. 단계를 모두 완료하면 다음 인증서 체인 및 키 쌍이 있어야 합니다.

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

IoT Edge 디바이스에는 해당 인증서가 모두 필요합니다. DPS에서 개별 등록을 사용하려면 .cert.pem 파일을 업로드합니다. DPS에서 그룹 등록을 사용하려면 업로드하려는 동일한 인증서 신뢰 체인에 중간 또는 루트 CA 인증서도 있어야 합니다. 데모 인증서를 사용한다면 그룹 등록에 `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem` 인증서를 사용합니다.

## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

생성 된 인증서 및 키를 사용 하 여 하나 이상의 IoT Edge 장치에 대해 DPS에서 등록을 만듭니다.

단일 IoT Edge 장치를 프로 비전 하려는 경우 **개별 등록** 을 만듭니다. 여러 장치를 프로 비전 해야 하는 경우 DPS **그룹 등록** 을 만드는 단계를 수행 합니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태** 를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

Device Provisioning Service의 등록에 대한 자세한 내용은 [디바이스 등록을 관리하는 방법](../iot-dps/how-to-manage-enrollments.md) 항목을 참조하세요.

# <a name="individual-enrollment"></a>[개별 등록](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>DPS 개별 등록 만들기

개별 등록은 디바이스의 ID 인증서의 퍼블릭 부분을 가져와 디바이스의 인증서와 일치시킵니다.

> [!TIP]
> 이 문서의 단계는 Azure Portal에 대 한 것 이지만 Azure CLI를 사용 하 여 개별 등록를 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)를 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. 등록을 구성하려면 **개별 등록 추가** 를 선택한 다음, 다음 단계를 완료합니다.  

   * **메커니즘**: **X.509** 를 선택합니다.

   * **기본 인증서 .pem 또는 .cer 파일**: 디바이스 ID 인증서에서 퍼블릭 파일을 업로드합니다. 스크립트를 사용하여 테스트 인증서를 생성한 경우 다음 파일을 선택합니다.

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT Hub 디바이스 ID**: 원하는 경우 디바이스에 대한 ID를 제공합니다. 디바이스 ID를 사용하여 모듈 배포에 대한 개별 디바이스를 대상으로 할 수 있습니다. 디바이스 ID를 제공하지 않으면 X.509 인증서의 일반 이름(CN)이 사용됩니다.

   * **IoT Edge 디바이스**: 등록이 IoT Edge 디바이스에 대한 것이라고 선언하려면 **True** 를 선택합니다.

   * **이 디바이스를 할당할 수 있는 IoT 허브 선택**: 디바이스를 연결하려는 연결된 IoT 허브를 선택합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 허브 중 하나에 디바이스가 할당됩니다.

   * **초기 디바이스 쌍 상태**: 원하는 경우 디바이스 쌍에 태그 값을 추가합니다. 태그를 사용하여 자동 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 예를 들면 다음과 같습니다.

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

이제 이 디바이스에 대한 등록이 존재하므로 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비저닝할 수 있습니다.

# <a name="group-enrollment"></a>[그룹 등록](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>DPS 그룹 등록 만들기

Group 등록은 신뢰의 인증서 체인에서 중간 또는 루트 CA 인증서를 사용 하 여 개별 장치 id 인증서를 확인 합니다.

#### <a name="verify-your-root-certificate"></a>루트 인증서 확인

등록 그룹을 만들 때 확인된 인증서를 사용할 수 있는 옵션이 존재합니다. 루트 인증서의 소유권이 있음을 증명하여 DPS로 인증서를 확인할 수 있습니다. 자세한 내용은 [X.509 CA 인증서에 대해 소유 증명을 수행하는 방법](../iot-dps/how-to-verify-certificates.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

1. 왼쪽 메뉴에서 **인증서** 를 선택합니다.

1. **추가** 를 선택하여 새 인증서를 추가합니다.

1. 인증서에 식별 이름을 입력하고 X.509 인증서의 퍼블릭 부분을 나타내는 .cer 또는 .pem 파일로 이동합니다.

   데모 인증서를 사용하는 경우 `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` 인증서를 업로드합니다.

1. **저장** 을 선택합니다.

1. 이제 인증서가 **인증서** 페이지에 나열됩니다. 인증서 세부 정보를 열려면 해당 페이지를 선택합니다.

1. **확인 코드 생성** 을 선택하고 생성된 코드를 복사합니다.

1. 사용자 고유의 CA 인증서를 사용하든 데모 인증서를 사용하든 관계없이 IoT Edge 리포지토리에 제공되는 확인 도구를 사용하여 소유 증명을 확인할 수 있습니다. 확인 도구는 CA 인증서를 사용하여 제공된 확인 코드를 주체 이름으로 포함하는 새 인증서에 서명합니다.

   ```bash
   ./certGen.sh create_verification_certificate <verification code>
   ```

1. Azure Portal의 동일한 인증서 정보 페이지에서 새로 생성된 확인 인증서를 업로드합니다.

1. **확인** 을 선택합니다.

#### <a name="create-enrollment-group"></a>등록 그룹 만들기

Device Provisioning Service의 등록에 대한 자세한 내용은 [디바이스 등록을 관리하는 방법](../iot-dps/how-to-manage-enrollments.md) 항목을 참조하세요.

> [!TIP]
> 이 문서의 단계는 Azure Portal 대한 단계이지만 Azure CLI 사용하여 그룹 등록을 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group)을 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다. 그룹 등록의 경우 모든 디바이스가 IoT Edge 디바이스여야 하며, 그렇지 않은 경우 어떤 디바이스도 등록할 수 없습니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. 등록을 구성하려면 **등록 그룹 추가** 를 선택한 다음, 다음 단계를 완료합니다.

   * **그룹 이름**: 이 그룹 등록에 대해 기억하기 쉬운 이름을 입력합니다.

   * **증명 형식**: **인증서** 를 선택합니다.

   * **IoT Edge 디바이스**: **True** 를 선택합니다. 그룹 등록의 경우 모든 디바이스가 IoT Edge 디바이스여야 하며, 그렇지 않은 경우 어떤 디바이스도 등록할 수 없습니다.

   * **인증서 유형:** **CA 인증서** 를 선택합니다.

   * **기본 인증서:** 드롭다운 목록에서 인증서를 선택합니다.

   * **이 디바이스를 할당할 수 있는 IoT 허브 선택**: 디바이스를 연결하려는 연결된 IoT 허브를 선택합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 허브 중 하나에 디바이스가 할당됩니다.

   * **초기 디바이스 쌍 상태**: 원하는 경우 디바이스 쌍에 태그 값을 추가합니다. 태그를 사용하여 자동 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 예를 들면 다음과 같습니다.

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

이제 이러한 디바이스에 대한 등록이 존재하기 때문에 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비전할 수 있습니다.

---

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

이 섹션에서는 IoT Edge 위해 Linux 가상 머신 또는 물리적 디바이스를 준비합니다. 그런 다음, IoT Edge 설치합니다.

IoT Edge 런타임을 설치할 준비가 되기 전에 디바이스에서 완료해야 하는 두 단계가 있습니다. 디바이스는 Microsoft 설치 패키지에 액세스해야 하며 컨테이너 엔진을 설치해야 합니다.

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

이 섹션의 단계는 인터넷에 연결되어 있는 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 나타냅니다. 사전 릴리스 버전과 같은 특정 버전을 설치해야 하거나 오프라인 상태에서 설치해야 하는 경우 오프라인 또는 특정 버전 설치 단계를 수행합니다.

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

이 섹션의 단계는 인터넷에 연결된 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 보여 줍니다. 사전 릴리스 버전과 같은 특정 버전을 설치해야 하거나 오프라인 상태에서 설치해야 하는 경우 오프라인 또는 특정 버전 설치 단계를 수행합니다.

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

* DPS **ID 범위** 값. Azure Portal에 있는 DPS 인스턴스의 개요 페이지에서 이 값을 검색할 수 있습니다.
* 디바이스에 있는 디바이스 ID 인증서 체인 파일.
* 디바이스에 있는 디바이스 ID 키 파일.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. IoT Edge 디바이스에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 파일의 프로비저닝 구성 섹션을 찾습니다. DPS X.509 인증서 프로비저닝에 대한 줄의 주석 처리를 제거하고 다른 프로비저닝 줄이 주석으로 처리되어 있는지 확인합니다.

   `provisioning:` 줄은 앞에 공백이 없어야 하며 중첩 항목은 두 개의 공백으로 들여써야 합니다.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. `scope_id`, `identity_cert` 및 `identity_pk`의 값을 DPS 및 디바이스 정보로 업데이트합니다.

   X.509 인증서 및 키 정보를 config.yaml 파일에 추가하는 경우 경로를 파일 URI로 제공해야 합니다. 예를 들어:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. 필요에 따라 ID 인증서의 CN(일반 이름)과 일치해야 하는 디바이스에 대한 `registration_id`를 제공합니다. 해당 줄을 주석 처리하지 않으면 CN이 자동으로 적용됩니다.

1. 필요에 따라 `always_reprovision_on_startup` 또는 `dynamic_reprovisioning` 줄을 사용하여 디바이스의 다시 프로비저닝 동작을 구성합니다. 시작 시 디바이스가 다시 프로비저닝되도록 설정되어 있는 경우 항상 먼저 DPS를 사용하여 프로비저닝을 시도한 다음, 실패하는 경우 프로비저닝 백업으로 대체합니다. 디바이스가 동적으로 다시 프로비저닝되도록 설정된 경우 다시 프로비저닝 이벤트가 감지되면 IoT Edge가 다시 시작되고 다시 프로비저닝됩니다. 자세한 내용은 [IoT Hub 디바이스 다시 프로비저닝 개념](../iot-dps/concepts-device-reprovision.md)을 참조하세요.

1. config.yaml 파일을 저장한 후 닫습니다.

1. 디바이스에서 한 모든 구성 변경을 선택하도록 IoT Edge 런타임을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

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

1. 파일의 **프로비전** 섹션을 찾습니다. X.509 인증서를 사용하는 DPS 프로비저닝에 대한 줄의 주석 처리를 제거하고 다른 프로비저닝 줄이 주석으로 처리되어 있는지 확인합니다.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   registration_id = "<REGISTRATION ID>"

   identity_cert = "<DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<DEVICE IDENTITY PRIVATE KEY>"
   ```

1. `id_scope` 값을 DPS 인스턴스에서 복사한 범위 ID로 업데이트합니다.

1. 디바이스가 IoT Hub에서 갖게 될 ID인 디바이스에 대한 `registration_id`를 제공합니다. 등록 ID는 ID 인증서의 CN(일반 이름)과 일치해야 합니다.

1. 인증서와 키 정보로 `identity_cert` 및 `identity_pk` 값을 업데이트합니다.

   ID 인증서 값을 파일 URI로 제공하거나 EST 또는 로컬 인증 기관을 사용하여 동적으로 발급할 수 있습니다. 사용하도록 선택한 형식에 따라 한 줄의 주석 처리만을 제거합니다.

   ID 프라이빗 키 값은 파일 URI 또는 PKCS#11 URI로 제공될 수 있습니다. 사용하도록 선택한 형식에 따라 한 줄의 주석 처리만을 제거합니다.

   PKCS#11 URI를 사용하는 경우 구성 파일에서 **PKCS#11** 섹션을 찾고 PKCS#11 구성에 대한 정보를 제공합니다.

1. 파일을 저장하고 닫습니다.

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
