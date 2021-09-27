---
title: Windows에서 x.509 인증서를 사용 하 여 대규모로 IoT Edge 장치 만들기 및 프로 비전 Azure IoT Edge | Microsoft Docs
description: x.509 인증서를 사용 하 여 장치 프로 비전 서비스를 통해 Azure IoT Edge에 대 한 대규모로 프로 비전 장치 테스트
author: v-tcassi
ms.author: v-tcassi
ms.date: 08/12/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d0be2b1752460744d9dad8686f69b27a9350ffb3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705633"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-windows-using-x509-certificates"></a>x.509 인증서를 사용 하 여 Windows 대규모로 IoT Edge 장치 만들기 및 프로 비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 x.509 인증서를 사용 하 여 하나 이상의 Windows IoT Edge 장치를 자동 프로 비전 하기 위한 종단 간 지침을 제공 합니다. [Azure IoT 허브 장치 프로 비전 서비스](../iot-dps/index.yml) (DPS)를 사용 하 여 Azure IoT에 지 장치를 자동으로 프로 비전 할 수 있습니다. 자동 프로 비전 프로세스에 익숙하지 않은 경우 계속 하기 전에 [프로 비전 개요](../iot-dps/about-iot-dps.md#provisioning-process) 를 검토 합니다.

작업은 다음과 같습니다.

1. 인증서 및 키를 생성합니다.
1. 단일 장치에 대 한 **개별 등록** 또는 장치 집합에 대 한 **그룹 등록** 을 만듭니다.
1. IoT Edge 런타임을 설치하고 IoT Hub를 사용하여 디바이스를 등록합니다.

X.509 인증서를 증명 메커니즘으로 사용하면 프로덕션의 크기를 조정하고 디바이스 프로비전을 간소화할 수 있습니다. 일반적으로 X.509 인증서는 신뢰할 수 있는 인증서 체인에 정렬됩니다. 자체 서명되었거나 신뢰할 수 있는 루트 인증서부터 시작하여, 체인에 존재하는 각 인증서는 그다음으로 낮은 인증서에 서명합니다. 이 패턴을 통해 루트 인증서부터 시작하여 각 중간 인증서를 거쳐 디바이스에 설치된 마지막 “리프” 인증서에서 완료되는 위임된 신뢰 체인이 만들어집니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 IoT Hub
* IoT Edge 장치로 사용할 실제 또는 가상 Windows 장치입니다.
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

> [!NOTE]
> 현재 libiothsm의 제한으로 인해 2038년 1월 1일 이후에 만료되는 인증서는 사용할 수 없습니다.

### <a name="use-test-certificates-optional"></a>테스트 인증서 사용(선택 사항)

새 ID 인증서를 만드는 데 사용할 수 있는 인증 기관이 없지만 이 시나리오를 사용해 보려는 경우, Azure IoT Edge Git 리포지토리에 테스트 인증서 생성에 사용할 수 있는 스크립트가 포함되어 있습니다. 해당 인증서는 개발 테스트 전용으로 설계되었으며 프로덕션 단계에서 사용해서는 안 됩니다.

테스트 인증서를 만들려면 [데모 인증서를 만들어 IoT Edge 디바이스 기능 테스트](how-to-create-test-certificates.md)의 단계를 따릅니다. 인증서 생성 스크립트를 설정하고 루트 CA 인증서를 만들기 위한 두 개의 필수 섹션을 완료합니다. 그런 다음 단계를 따라 디바이스 ID 인증서를 만듭니다. 단계를 모두 완료하면 다음 인증서 체인 및 키 쌍이 있어야 합니다.

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

IoT Edge 디바이스에는 해당 인증서가 모두 필요합니다. DPS에서 개별 등록을 사용하려면 .cert.pem 파일을 업로드합니다. DPS에서 그룹 등록을 사용하려면 업로드하려는 동일한 인증서 신뢰 체인에 중간 또는 루트 CA 인증서도 있어야 합니다. 데모 인증서를 사용한다면 그룹 등록에 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 인증서를 사용합니다.

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

      `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`

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

그룹 등록은 개별 디바이스 ID 인증서 생성에 사용되는 인증서 신뢰 체인의 중간 또는 루트 CA 인증서를 사용합니다.

#### <a name="verify-your-root-certificate"></a>루트 인증서 확인

등록 그룹을 만들 때 확인된 인증서를 사용할 수 있는 옵션이 존재합니다. 루트 인증서의 소유권이 있음을 증명하여 DPS로 인증서를 확인할 수 있습니다. 자세한 내용은 [X.509 CA 인증서에 대해 소유 증명을 수행하는 방법](../iot-dps/how-to-verify-certificates.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

1. 왼쪽 메뉴에서 **인증서** 를 선택합니다.

1. **추가** 를 선택하여 새 인증서를 추가합니다.

1. 인증서에 식별 이름을 입력하고 X.509 인증서의 퍼블릭 부분을 나타내는 .cer 또는 .pem 파일로 이동합니다.

   데모 인증서를 사용하는 경우 `<wrkdir>\certs\azure-iot-test-only.root.ca.cert.pem` 인증서를 업로드합니다.

1. **저장** 을 선택합니다.

1. 이제 인증서가 **인증서** 페이지에 나열됩니다. 인증서 세부 정보를 열려면 해당 페이지를 선택합니다.

1. **확인 코드 생성** 을 선택하고 생성된 코드를 복사합니다.

1. 사용자 고유의 CA 인증서를 사용하든 데모 인증서를 사용하든 관계없이 IoT Edge 리포지토리에 제공되는 확인 도구를 사용하여 소유 증명을 확인할 수 있습니다. 확인 도구는 CA 인증서를 사용하여 제공된 확인 코드를 주체 이름으로 포함하는 새 인증서에 서명합니다.

   ```powershell
   New-CACertsVerificationCert "<verification code>"
   ```

1. Azure Portal의 동일한 인증서 정보 페이지에서 새로 생성된 확인 인증서를 업로드합니다.

1. **확인** 을 선택합니다.

#### <a name="create-enrollment-group"></a>등록 그룹 만들기

Device Provisioning Service의 등록에 대한 자세한 내용은 [디바이스 등록을 관리하는 방법](../iot-dps/how-to-manage-enrollments.md) 항목을 참조하세요.

> [!TIP]
> 이 문서의 단계는 Azure Portal에 대 한 것 이지만 Azure CLI를 사용 하 여 그룹 등록 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group)을 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다. 그룹 등록의 경우 모든 디바이스가 IoT Edge 디바이스여야 하며, 그렇지 않은 경우 어떤 디바이스도 등록할 수 없습니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. 등록을 구성하려면 **등록 그룹 추가** 를 선택한 다음, 다음 단계를 완료합니다.

   * **그룹 이름**: 이 그룹 등록에 대해 기억하기 쉬운 이름을 입력합니다.

   * **증명 형식**: **인증서** 를 선택합니다.

   * **IoT Edge 디바이스**: **True** 를 선택합니다. 그룹 등록의 경우 모든 디바이스가 IoT Edge 디바이스여야 하며, 그렇지 않은 경우 어떤 디바이스도 등록할 수 없습니다.

   * **인증서 유형**: **CA 인증서** 를 선택 합니다.

   * **기본 인증서**: 드롭다운 목록에서 인증서를 선택 합니다.

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

이제 이러한 장치에 대 한 등록이 있으므로 IoT Edge 런타임은 설치 중에 자동으로 장치를 프로 비전 할 수 있습니다.

---

다음 섹션으로 진행할 준비가 되었습니다.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

이 섹션에서는 IoT Edge에 대 한 Windows 가상 머신 또는 물리적 장치를 준비 합니다. 그런 다음 IoT Edge를 설치 합니다.

IoT Edge 런타임을 설치할 준비가 되기 전에 장치에서 완료 해야 하는 단계가 하나 있습니다. 장치에 컨테이너 엔진이 설치 되어 있어야 합니다.

### <a name="install-a-container-engine"></a>컨테이너 엔진 설치

Azure IoT Edge는 설치 스크립트에 포함 된 moby 기반 엔진인 [moby](https://github.com/moby/moby)와 같은 OCI 호환 컨테이너 런타임에 의존 합니다. 엔진 설치를 위한 추가 단계는 없습니다.

### <a name="install-iot-edge"></a>IoT Edge 설치

IoT Edge 보안 디먼은 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

이 섹션의 단계는 인터넷에 연결 된 장치에 최신 버전을 설치 하는 일반적인 프로세스를 나타냅니다. 시험판 버전과 같은 특정 버전을 설치 해야 하거나 오프 라인 상태에서 설치 해야 하는 경우 오프 라인 또는 특정 버전의 설치 단계를 따르세요.

1. PowerShell을 관리자 권한으로 실행합니다.

   PowerShell(x86)이 아닌 AMD64 PowerShell 세션을 사용합니다. 사용 중인 세션 형식을 잘 모르는 경우 다음 명령을 실행합니다.

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. 다음 작업을 수행하는 [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) 명령을 실행합니다.

   * Windows 머신이 지원되는 버전에 해당하는지 확인합니다.
   * 컨테이너 기능을 사용하도록 설정합니다.
   * Moby 엔진과 IoT Edge 런타임을 다운로드합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. 메시지가 표시되면 디바이스를 다시 시작합니다.

디바이스에 IoT Edge를 설치하는 경우 추가 매개 변수를 사용하여 다음과 같이 프로세스를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 오프라인 설치 시 설치 프로그램이 로컬 디렉터리를 가리키도록 합니다.

추가 매개 변수에 대한 자세한 내용은 [Windows 컨테이너를 포함한 IoT Edge용 PowerShell 스크립트](reference-windows-scripts.md)를 참조하세요.

## <a name="configure-the-device-with-provisioning-information"></a>프로비저닝 정보를 사용하여 디바이스 구성

런타임이 디바이스에 설치되면 디바이스에서 Device Provisioning Service 및 IoT Hub에 연결하는 데 사용하는 정보로 디바이스를 구성합니다.

다음 정보를 준비합니다.

* DPS **ID 범위** 값. Azure Portal에 있는 DPS 인스턴스의 개요 페이지에서 이 값을 검색할 수 있습니다.
* 디바이스에 있는 디바이스 ID 인증서 체인 파일.
* 디바이스에 있는 디바이스 ID 키 파일.

1. 관리자 모드에서 PowerShell 창을 엽니다. PowerShell(x86)이 아닌 IoT Edge를 설치하는 경우 PowerShell의 AMD64 세션을 사용해야 합니다.

1. **Initialize IoTEdge** 명령은 사용자의 머신에서 IoT Edge 런타임을 구성합니다. 이 명령은 기본적으로 Windows 컨테이너를 사용하여 수동으로 프로비저닝하므로 X.509 인증서 인증을 사용하여 자동으로 프로비저닝하려면 `-DpsX509` 플래그를 사용합니다.

   `{scope_id}`, `{identity cert chain path}` 및 `{identity key path}`에 대한 자리 표시자 값을 DPS 인스턴스의 적절한 값 및 디바이스의 파일 경로로 바꿉니다.

   `-RegistrationId {registration_id}`장치 id를 id 인증서의 CN 이름 이외의 이름으로 설정 하려는 경우 매개 변수를 추가 합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >구성 파일은 인증서 및 키 정보를 파일 URI로 저장합니다. 그러나 Initialize-IoTEdge 명령이 사용자 대신 이 형식 지정 단계를 처리하므로 디바이스의 인증서 및 키 파일에 대한 절대 경로를 제공할 수 있습니다.

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하고 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다.

# <a name="individual-enrollment"></a>[개별 등록](#tab/individual-enrollment)

Device Provisioning Service에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure Portal에서 Device Provisioning Service 인스턴스로 이동합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

# <a name="group-enrollment"></a>[그룹 등록](#tab/group-enrollment)

장치 프로 비전 서비스에서 만든 그룹 등록이 사용 되었는지 확인할 수 있습니다. Azure Portal에서 Device Provisioning Service 인스턴스로 이동합니다. 만든 그룹 등록에 대 한 등록 정보를 엽니다. **등록 레코드** 탭으로 이동 하 여 해당 그룹에 등록 된 모든 장치를 볼 수 있습니다.

---

디바이스에서 다음 명령을 사용하여 IoT Edge가 성공적으로 설치되고 시작되는지 확인합니다.

IoT Edge 서비스의 상태를 확인합니다.

```powershell
Get-Service iotedge
```

서비스 로그를 검사합니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

실행 중인 모듈을 나열합니다.

```powershell
iotedge list
```

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 디바이스를 프로비전할 때 디바이스 ID 및 디바이스 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-cli-at-scale.md)하거나 [Azure Portal을 사용하여 대규모로 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-at-scale.md)하는 방법을 알아봅니다.
