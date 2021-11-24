---
title: Windows X.509 인증서를 사용하여 대규모로 IoT Edge 디바이스 만들기 및 프로비전 - Azure IoT Edge | Microsoft Docs
description: X.509 인증서를 사용하여 디바이스 프로비저닝 서비스에서 Azure IoT Edge에 대한 대규모 프로비저닝 디바이스 테스트
author: kgremban
ms.author: kgremban
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a293070336a30d029f055bf22840a49a38ae673b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133058438"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-windows-using-x509-certificates"></a>X.509 인증서를 사용하여 Windows 대규모로 IoT Edge 디바이스 만들기 및 프로비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 X.509 인증서를 사용하여 하나 이상의 Windows IoT Edge 디바이스를 자동 프로비전하는 엔드 투 엔드 지침을 제공합니다. Azure IoT Hub DPS(디바이스 [프로비저닝 서비스)를 Azure IoT Edge 디바이스를 자동으로 프로비전할](../iot-dps/index.yml) 수 있습니다. 자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [프로비전 개요를](../iot-dps/about-iot-dps.md#provisioning-process) 검토하세요.

>[!NOTE]
>Windows 컨테이너를 사용하는 Azure IoT Edge는 Azure IoT Edge 버전 1.2부터 지원되지 않습니다.
>
>Window 디바이스에서 IoT Edge를 실행하는 새로운 방법을 사용하려면 [Azure IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md)를 참조하세요.
>
>Windows Linux용 Azure IoT Edge를 사용하려는 경우 [해당하는 방법 가이드의](how-to-provision-devices-at-scale-linux-on-windows-x509.md)단계를 따르면 됩니다.

작업은 다음과 같습니다.

1. 인증서 및 키를 생성합니다.
1. 단일 디바이스에 대한 **개별 등록** 또는 디바이스 집합에 대한 **그룹 등록을** 만듭니다.
1. IoT Edge 런타임을 설치하고 IoT Hub를 사용하여 디바이스를 등록합니다.

X.509 인증서를 증명 메커니즘으로 사용하면 프로덕션의 크기를 조정하고 디바이스 프로비전을 간소화할 수 있습니다. 일반적으로 X.509 인증서는 신뢰할 수 있는 인증서 체인에 정렬됩니다. 자체 서명되었거나 신뢰할 수 있는 루트 인증서부터 시작하여, 체인에 존재하는 각 인증서는 그다음으로 낮은 인증서에 서명합니다. 이 패턴을 통해 루트 인증서부터 시작하여 각 중간 인증서를 거쳐 디바이스에 설치된 마지막 “리프” 인증서에서 완료되는 위임된 신뢰 체인이 만들어집니다.

## <a name="prerequisites"></a>사전 요구 사항

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="device-requirements"></a>디바이스 요구 사항

IoT Edge 디바이스가 될 물리적 또는 가상 Windows 디바이스입니다.

## <a name="generate-device-identity-certificates"></a>디바이스 ID 인증서 생성

디바이스 ID 인증서는 인증서 신뢰 체인을 통해 상위 X.509 인증 기관(CA) 인증서에 연결하는 리프 인증서입니다. 디바이스 ID 인증서의 CN(일반 이름)은 디바이스가 IoT Hub에 갖도록 하려는 디바이스 ID로 설정되어 있어야 합니다.

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

<!-- Create a DPS enrollment using X.509 certificates H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-x509.md](../../includes/iot-edge-create-dps-enrollment-x509.md)]

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>클라우드 ID를 사용한 디바이스 프로비전

런타임이 디바이스에 설치되면 디바이스 프로비전 서비스에 연결하고 IoT Hub 데 사용하는 정보로 디바이스를 구성합니다.

다음 정보를 준비합니다.

* DPS **ID 범위** 값. Azure Portal에 있는 DPS 인스턴스의 개요 페이지에서 이 값을 검색할 수 있습니다.
* 디바이스에 있는 디바이스 ID 인증서 체인 파일.
* 디바이스에 있는 디바이스 ID 키 파일.

1. 관리자 모드에서 PowerShell 창을 엽니다. PowerShell(x86)이 아닌 IoT Edge를 설치하는 경우 PowerShell의 AMD64 세션을 사용해야 합니다.

1. **Initialize IoTEdge** 명령은 사용자의 머신에서 IoT Edge 런타임을 구성합니다. 이 명령은 기본적으로 Windows 컨테이너를 사용하여 수동으로 프로비저닝하므로 X.509 인증서 인증을 사용하여 자동으로 프로비저닝하려면 `-DpsX509` 플래그를 사용합니다.

   `scope_id`, `identity cert chain path` 및 `identity key path`에 대한 자리 표시자 값을 DPS 인스턴스의 적절한 값 및 디바이스의 파일 경로로 바꿉니다.

   디바이스 `-RegistrationId paste_registration_id_here` ID를 ID 인증서의 CN 이름이 아닌 다른 이름으로 설정하려면 매개 변수를 추가합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId paste_scope_id_here -X509IdentityCertificate paste_identity_cert_chain_path_here -X509IdentityPrivateKey paste_identity_key_path_here
   ```

   >[!TIP]
   >구성 파일은 인증서 및 키 정보를 파일 URI로 저장합니다. 그러나 Initialize-IoTEdge 명령이 사용자 대신 이 형식 지정 단계를 처리하므로 디바이스의 인증서 및 키 파일에 대한 절대 경로를 제공할 수 있습니다.

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하고 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다.

# <a name="individual-enrollment"></a>[개별 등록](#tab/individual-enrollment)

디바이스 프로비저닝 서비스에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure Portal 디바이스 프로비저닝 서비스 인스턴스로 이동합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

# <a name="group-enrollment"></a>[그룹 등록](#tab/group-enrollment)

디바이스 프로비저닝 서비스에서 만든 그룹 등록이 사용되었는지 확인할 수 있습니다. Azure Portal 디바이스 프로비저닝 서비스 인스턴스로 이동합니다. 만든 그룹 등록에 대한 등록 세부 정보를 엽니다. **등록 레코드** 탭으로 이동하여 해당 그룹에 등록된 모든 디바이스를 봅니다.

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

디바이스 프로비전 서비스 등록 프로세스를 사용하면 새 디바이스를 프로비전하는 동시에 디바이스 ID 및 디바이스 쌍 태그를 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-cli-at-scale.md)하거나 [Azure Portal을 사용하여 대규모로 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-at-scale.md)하는 방법을 알아봅니다.
