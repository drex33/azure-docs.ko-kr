---
title: X.509 인증서를 사용하여 DPS에 디바이스를 자동으로 프로비저닝 - Azure IoT Edge | Microsoft Docs
description: X.509 인증서를 사용하여 Device Provisioning Service로 Azure IoT Edge에 대한 자동 디바이스 프로비저닝 테스트
author: kgremban
ms.author: kgremban
ms.date: 06/18/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4095207fbc4fbbf4cb3f6463e3091717fcd3921a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536353"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>X.509 인증서를 사용하여 IoT Edge 디바이스 만들기 및 프로비저닝

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

[Azure IoT Hub Device Provisioning Service(DPS)](../iot-dps/index.yml)를 통해 X.509 인증서를 사용하여 IoT Edge 디바이스를 자동으로 프로비저닝할 수 있습니다. 자동 프로비저닝 프로세스에 익숙하지 않은 경우 계속하기 전에 [프로비저닝](../iot-dps/about-iot-dps.md#provisioning-process) 개요를 검토하세요.

이 문서에서는 다음 단계를 따라 IoT Edge 디바이스에서 X.509 인증서를 사용하여 Device Provisioning Service 등록을 만드는 방법을 보여 줍니다.

* 인증서 및 키를 생성합니다.
* 디바이스에 대한 개별 등록 또는 디바이스 집합에 대한 그룹 등록을 만듭니다.
* IoT Edge 런타임을 설치하고 IoT Hub를 사용하여 디바이스를 등록합니다.

X.509 인증서를 증명 메커니즘으로 사용하면 프로덕션의 크기를 조정하고 디바이스 프로비전을 간소화할 수 있습니다. 일반적으로 X.509 인증서는 신뢰할 수 있는 인증서 체인에 정렬됩니다. 자체 서명되었거나 신뢰할 수 있는 루트 인증서부터 시작하여, 체인에 존재하는 각 인증서는 그다음으로 낮은 인증서에 서명합니다. 이 패턴을 통해 루트 인증서부터 시작하여 각 중간 인증서를 거쳐 디바이스에 설치된 마지막 “리프” 인증서에서 완료되는 위임된 신뢰 체인이 만들어집니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 IoT Hub
* IoT Edge 디바이스가 될 수 있는 실제 또는 가상 디바이스입니다.
* 최신 버전의 [Git](https://git-scm.com/download/)이 설치되어 있습니다.
* Azure의 IoT Hub Device Provisioning Service의 새 인스턴스로 IoT 허브에 연결되어 있습니다.
  * Device Provisioning Service 인스턴스가 없는 경우 [IOT HUB DPS 설정](../iot-dps/quick-setup-auto-provision.md)의 지침을 따르세요.
  * Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

## <a name="generate-device-identity-certificates"></a>디바이스 ID 인증서 생성

디바이스 ID 인증서는 인증서 신뢰 체인을 통해 상위 X.509 인증 기관(CA) 인증서에 연결하는 리프 인증서입니다. 디바이스 ID 인증서에는 디바이스를 IoT 허브에 포함하려는 디바이스 ID로 설정된 일반 이름(CN)이 있어야 합니다.

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

### <a name="use-test-certificates-optional"></a>테스트 인증서 사용(선택 사항)

새 ID 인증서를 만드는 데 사용할 수 있는 인증 기관이 없지만 이 시나리오를 사용해 보려는 경우, Azure IoT Edge Git 리포지토리에 테스트 인증서 생성에 사용할 수 있는 스크립트가 포함되어 있습니다. 해당 인증서는 개발 테스트 전용으로 설계되었으며 프로덕션 단계에서 사용해서는 안 됩니다.

테스트 인증서를 만들려면 [데모 인증서를 만들어 IoT Edge 디바이스 기능 테스트](how-to-create-test-certificates.md)의 단계를 따릅니다. 인증서 생성 스크립트를 설정하고 루트 CA 인증서를 만들기 위한 두 개의 필수 섹션을 완료합니다. 그런 다음 단계를 따라 디바이스 ID 인증서를 만듭니다. 단계를 모두 완료하면 다음 인증서 체인 및 키 쌍이 있어야 합니다.

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

IoT Edge 디바이스에는 해당 인증서가 모두 필요합니다. DPS에서 개별 등록을 사용하려면 .cert.pem 파일을 업로드합니다. DPS에서 그룹 등록을 사용하려면 업로드하려는 동일한 인증서 신뢰 체인에 중간 또는 루트 CA 인증서도 있어야 합니다. 데모 인증서를 사용한다면 그룹 등록에 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 인증서를 사용합니다.

## <a name="create-a-dps-individual-enrollment"></a>DPS 개별 등록 만들기

단일 IoT Edge 디바이스에 대해 DPS에서 개별 등록을 만들려면 생성된 인증서와 키를 사용합니다. 개별 등록은 디바이스의 ID 인증서의 퍼블릭 부분을 가져와 디바이스의 인증서와 일치시킵니다.

여러 IoT Edge 디바이스를 프로비저닝하려는 경우 다음 섹션인 [DPS 그룹 등록 만들기](#create-a-dps-group-enrollment)의 단계를 수행합니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태** 를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

Device Provisioning Service의 등록에 대한 자세한 내용은 [디바이스 등록을 관리하는 방법](../iot-dps/how-to-manage-enrollments.md) 항목을 참조하세요.

   > [!TIP]
   > Azure CLI에서 [등록](/cli/azure/iot/dps/enrollment) 또는 [등록 그룹](/cli/azure/iot/dps/enrollment-group)을 만들고, **edge-enabled** 플래그를 사용하여 디바이스 또는 디바이스 그룹이 IoT Edge 디바이스임을 지정할 수 있습니다.

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

이제 이 디바이스에 대한 등록이 존재하므로 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비저닝할 수 있습니다. 이제 [IoT Edge 런타임 설치](#install-the-iot-edge-runtime) 섹션으로 이동하여 IoT Edge 디바이스를 설정합니다.

## <a name="create-a-dps-group-enrollment"></a>DPS 그룹 등록 만들기

여러 IoT Edge 디바이스에 대해 DPS에서 그룹 등록을 만들려면 생성된 인증서와 키를 사용합니다. 그룹 등록은 개별 디바이스 ID 인증서 생성에 사용되는 인증서 신뢰 체인의 중간 또는 루트 CA 인증서를 사용합니다.

단일 IoT Edge 디바이스를 대신 프로비저닝하려는 경우 이전 섹션인 [DPS 개별 등록 만들기](#create-a-dps-individual-enrollment)의 단계를 따르세요.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태** 를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

### <a name="verify-your-root-certificate"></a>루트 인증서 확인

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

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Azure Portal의 동일한 인증서 정보 페이지에서 새로 생성된 확인 인증서를 업로드합니다.

1. **확인** 을 선택합니다.

### <a name="create-enrollment-group"></a>등록 그룹 만들기

Device Provisioning Service의 등록에 대한 자세한 내용은 [디바이스 등록을 관리하는 방법](../iot-dps/how-to-manage-enrollments.md) 항목을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. 등록을 구성하려면 **등록 그룹 추가** 를 선택한 다음, 다음 단계를 완료합니다.

   * **그룹 이름**: 이 그룹 등록에 대해 기억하기 쉬운 이름을 입력합니다.

   * **증명 형식**: **인증서** 를 선택합니다.

   * **IoT Edge 디바이스**: **True** 를 선택합니다. 그룹 등록의 경우 모든 디바이스가 IoT Edge 디바이스여야 하며, 그렇지 않은 경우 어떤 디바이스도 등록할 수 없습니다.

   * **인증서 유형**: DPS로 저장된 확인 된 CA 인증서가 있는 경우 **CA 인증서** 를 선택하고, 이 등록에 대해서만 새 파일을 업로드하려는 경우 **중간 인증서** 를 선택합니다.

   * **기본 인증서**: 지난 섹션에서 CA 인증서를 선택한 경우 드롭다운 목록에서 인증서를 선택합니다. 중간 인증서를 선택한 경우 디바이스 ID 인증서 생성에 사용된 인증서 신뢰 체인에 있는 CA 인증서에서 퍼블릭 파일을 업로드합니다.

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

이제 이 디바이스에 대한 등록이 존재하므로 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비저닝할 수 있습니다. 다음 섹션으로 계속 진행하여 IoT Edge 디바이스를 설정합니다.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

IoT Edge 런타임은 모든 IoT Edge 디바이스에 배포되며, 해당 구성 요소는 컨테이너에서 실행되며, Edge에서 코드를 실행할 수 있도록 디바이스에 추가 컨테이너의 배포를 허용합니다.

<!-- 1.1 -->
:::moniker range="=iotedge-2018-06"

운영 체제에 따라 Azure IoT Edge를 설치하려면 적절한 단계를 따르세요.

* [Linux용 IoT Edge 설치](how-to-install-iot-edge.md)
* [IoT Edge for Linux on Windows 디바이스 설치](how-to-install-iot-edge-on-windows.md)
  * 이 시나리오는 Windows 디바이스에서 IoT Edge를 실행하는 데 권장되는 방법입니다.
* [Windows 컨테이너로 IoT Edge 설치](how-to-install-iot-edge-windows-on-windows.md)

디바이스에 IoT Edge가 설치되면 이 문서로 돌아와 디바이스를 프로비저닝합니다.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

[Azure IoT Edge 런타임 설치](how-to-install-iot-edge.md)의 단계를 수행한 다음 이 문서로 돌아와서 디바이스를 프로비저닝합니다.

:::moniker-end
<!-- end 1.2 -->

DPS로 X.509를 프로비저닝하는 것은 IoT Edge 버전 1.0.9 이상에서만 지원됩니다.

## <a name="configure-the-device-with-provisioning-information"></a>프로비저닝 정보를 사용하여 디바이스 구성

런타임이 디바이스에 설치되면 디바이스에서 Device Provisioning Service 및 IoT Hub에 연결하는 데 사용하는 정보로 디바이스를 구성합니다.

다음 정보를 준비합니다.

* DPS **ID 범위** 값. Azure Portal에 있는 DPS 인스턴스의 개요 페이지에서 이 값을 검색할 수 있습니다.
* 디바이스에 있는 디바이스 ID 인증서 체인 파일.
* 디바이스에 있는 디바이스 ID 키 파일.

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="linux-on-windows"></a>[Windows의 Linux](#tab/eflow)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

PowerShell 또는 Windows Admin Center를 사용하여 IoT Edge 디바이스를 프로비저닝할 수 있습니다.

### <a name="powershell"></a>PowerShell

PowerShell의 경우 자체 값으로 업데이트된 자리 표시자 값을 사용하여 다음 명령을 실행합니다.

```powershell
Provision-EflowVm -provisioningType DPSX509 -scopeId <ID_SCOPE_HERE> -identityCertPath <ABSOLUTE_CERT_DEST_PATH_ON_WINDOWS_HOST> -identityPrivKeyPath <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_WINDOWS_HOST>
```

### <a name="windows-admin-center"></a>Windows Admin Center

Windows Admin Center의 경우 다음 단계를 사용합니다.

1. **Azure IoT Edge 디바이스 프로비전** 창의 프로비전 방법 드롭다운에서 **X.509 인증서(DPS)** 를 선택합니다.

1. [Azure Portal](https://ms.portal.azure.com/)에서 DPS 인스턴스로 이동합니다.

1. **개요** 탭에서 **ID 범위** 값을 복사합니다. Windows Admin Center의 범위 ID 필드에 붙여넣습니다.

1. Windows Admin Center의 등록 ID 필드에서 디바이스의 등록 ID를 제공합니다.

1. 인증서 및 프라이빗 키 파일을 업로드합니다.

1. **선택한 방법을 통한 프로비전** 을 선택합니다.

   ![X.509 인증서 프로비전에 필요한 필드를 입력한 후 선택한 방법을 통한 프로비전을 선택함](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. 프로비전이 완료되면 **마침** 을 선택합니다. 기본 대시보드로 돌아갑니다. 이제 유형이 `IoT Edge Devices`인 새 디바이스가 표시됩니다. 연결할 IoT Edge 디바이스를 선택할 수 있습니다. **개요** 페이지에서 디바이스의 **IoT Edge 모듈 목록** 및 **IoT Edge 상태** 가 표시됩니다.

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>현재 Windows용 IoT Edge for Linux에서 실행되는 IoT Edge 버전 1.2는 지원되지 않습니다.

:::moniker-end
<!-- end 1.2 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. 관리자 모드에서 PowerShell 창을 엽니다. PowerShell(x86)이 아닌 IoT Edge를 설치하는 경우 PowerShell의 AMD64 세션을 사용해야 합니다.

1. **Initialize IoTEdge** 명령은 사용자의 머신에서 IoT Edge 런타임을 구성합니다. 이 명령은 기본적으로 Windows 컨테이너를 사용하여 수동으로 프로비저닝하므로 X.509 인증서 인증을 사용하여 자동으로 프로비저닝하려면 `-DpsX509` 플래그를 사용합니다.

   `{scope_id}`, `{identity cert chain path}` 및 `{identity key path}`에 대한 자리 표시자 값을 DPS 인스턴스의 적절한 값 및 디바이스의 파일 경로로 바꿉니다.

   디바이스 ID를 ID 인증서의 CN 이름 이외의 이름으로 설정하려면 `-RegistrationId {registration_id}`를 추가합니다.

   Windows에서 Linux 컨테이너를 사용하는 경우 `-ContainerOs Linux` 매개 변수를 추가합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >구성 파일은 인증서 및 키 정보를 파일 URI로 저장합니다. 그러나 Initialize-IoTEdge 명령이 사용자 대신 이 형식 지정 단계를 처리하므로 디바이스의 인증서 및 키 파일에 대한 절대 경로를 제공할 수 있습니다.

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>현재 Windows에서 실행되는 IoT Edge 버전 1.2에 대한 지원은 없습니다.

:::moniker-end
<!-- end 1.2 -->

---

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하고 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다.

Device Provisioning Service에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure Portal에서 Device Provisioning Service 인스턴스로 이동합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

디바이스에서 다음 명령을 사용하여 IoT Edge가 성공적으로 설치되고 시작되는지 확인합니다.

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="linux-on-windows"></a>[Windows의 Linux](#tab/eflow)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge for Linux on Windows 가상 머신에 연결합니다.

```powershell
Connect-EflowVM
```

IoT Edge 서비스의 상태를 확인합니다.

```cmd/sh
sudo systemctl status iotedge
```

서비스 로그를 검사합니다.

```cmd/sh
sudo journalctl -u iotedge --no-pager --no-full
```

실행 중인 모듈을 나열합니다.

```cmd/sh
sudo iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>현재 Windows용 IoT Edge for Linux에서 실행되는 IoT Edge 버전 1.2는 지원되지 않습니다.

:::moniker-end
<!-- end 1.2 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
:::moniker range="=iotedge-2018-06"

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

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>현재 Windows에서 실행되는 IoT Edge 버전 1.2에 대한 지원은 없습니다.

:::moniker-end
<!-- end 1.2 -->

---

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 디바이스를 프로비전할 때 디바이스 ID 및 디바이스 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-cli-at-scale.md)하거나 [Azure Portal을 사용하여 대규모로 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-at-scale.md)하는 방법을 알아봅니다.
