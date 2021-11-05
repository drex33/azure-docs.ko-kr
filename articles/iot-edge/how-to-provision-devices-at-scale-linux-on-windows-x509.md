---
title: Windows Linux에서 X.509 인증서를 사용하여 IoT Edge 디바이스 만들기 및 프로비전 - Azure IoT Edge | Microsoft Docs
description: X.509 인증서 증명을 사용하여 디바이스 프로비저닝 서비스에서 Azure IoT Edge에 대한 대규모 프로비저닝 디바이스 테스트
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 59c29f3ce91f5ee1e1e5ee65710cc34ce1572907
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846004"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-x509-certificates"></a>X.509 인증서를 사용하여 대규모로 Windows 디바이스에서 Linux용 IoT Edge 만들고 프로비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 X.509 인증서를 사용하여 Windows [디바이스에서 Linux용 하나](iot-edge-for-linux-on-windows.md) 이상의 IoT Edge 자동 프로비전하는 엔드투엔드 지침을 제공합니다. Azure IoT Hub DPS(디바이스 [프로비저닝 서비스)를 Azure IoT Edge 디바이스를 자동으로 프로비전할](../iot-dps/index.yml) 수 있습니다. 자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [프로비전 개요를](../iot-dps/about-iot-dps.md#provisioning-process) 검토하세요.

작업은 다음과 같습니다.

1. 인증서 및 키를 생성합니다.
1. 단일 디바이스에 대한 **개별 등록** 또는 디바이스 집합에 대한 **그룹 등록을** 만듭니다.
1. IoT Edge 런타임이 설치된 Linux 가상 머신을 배포하고 IoT Hub 연결합니다.

X.509 인증서를 증명 메커니즘으로 사용하면 프로덕션의 크기를 조정하고 디바이스 프로비전을 간소화할 수 있습니다. 일반적으로 X.509 인증서는 신뢰할 수 있는 인증서 체인에 정렬됩니다. 자체 서명되었거나 신뢰할 수 있는 루트 인증서부터 시작하여, 체인에 존재하는 각 인증서는 그다음으로 낮은 인증서에 서명합니다. 이 패턴을 통해 루트 인증서부터 시작하여 각 중간 인증서를 거쳐 디바이스에 설치된 마지막 “리프” 인증서에서 완료되는 위임된 신뢰 체인이 만들어집니다.

## <a name="prerequisites"></a>사전 요구 사항

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

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

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>클라우드 ID를 사용한 디바이스 프로비전

런타임이 디바이스에 설치되면 디바이스 프로비전 서비스에 연결하고 IoT Hub 데 사용하는 정보로 디바이스를 구성합니다.

다음 정보를 준비합니다.

* DPS **ID 범위** 값. Azure Portal에 있는 DPS 인스턴스의 개요 페이지에서 이 값을 검색할 수 있습니다.
* 디바이스에 있는 디바이스 ID 인증서 체인 파일.
* 디바이스에 있는 디바이스 ID 키 파일.

PowerShell 또는 Windows Admin Center를 사용하여 IoT Edge 디바이스를 프로비저닝할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell의 경우 자체 값으로 업데이트된 자리 표시자 값을 사용하여 다음 명령을 실행합니다.

```powershell
Provision-EflowVm -provisioningType DpsX509 -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -identityCertPath PASTE_ABSOLUTE_PATH_TO_IDENTITY_CERTIFICATE_HERE -identityPrivateKey PASTE_ABSOLUTE_PATH_TO_IDENTITY_PRIVATE_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows Admin Center의 경우 다음 단계를 사용합니다.

1. Azure IoT **Edge 디바이스 프로비저닝 창의 프로비저닝** 방법 드롭다운에서 **X509 DPS(인증서)를** 선택합니다.

1. Windows Admin Center 필드에 DPS 범위 ID, 장치 등록 ID, 등록 기본 키, 파생 키를 제공합니다.

1. **선택한 방법을 통한 프로비전** 을 선택합니다.

   ![대칭 키 프로비전에 필요한 필드 PNG를 입력한 후 선택한 방법으로 프로비저닝을 선택합니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/provisioning-with-selected-method-x509-certs.png)

1. 프로비전이 완료되면 **마침** 을 선택합니다. 기본 대시보드로 돌아갑니다. 이제 유형이 `IoT Edge Devices`인 새 디바이스가 표시됩니다. 연결할 IoT Edge 디바이스를 선택할 수 있습니다. **개요** 페이지에서 디바이스의 **IoT Edge 모듈 목록** 및 **IoT Edge 상태** 가 표시됩니다.

---

## <a name="verify-successful-installation"></a>성공적인 설치 확인

IoT Edge for Linux on Windows가 IoT Edge 디바이스에 성공적으로 설치되고 구성되었는지 확인합니다.

# <a name="individual-enrollment"></a>[개별 등록](#tab/individual-enrollment)

디바이스 프로비저닝 서비스에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure Portal 디바이스 프로비저닝 서비스 인스턴스로 이동합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

# <a name="group-enrollment"></a>[그룹 등록](#tab/group-enrollment)

디바이스 프로비저닝 서비스에서 만든 그룹 등록이 사용되었는지 확인할 수 있습니다. Azure Portal 디바이스 프로비저닝 서비스 인스턴스로 이동합니다. 만든 그룹 등록에 대한 등록 세부 정보를 엽니다. **등록 레코드** 탭으로 이동하여 해당 그룹에 등록된 모든 디바이스를 봅니다.

---

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. PowerShell 세션에서 다음 명령을 사용하여 IoT Edge for Linux on Windows 가상 머신에 로그인합니다.

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >SSH를 가상 머신에 허용하는 유일한 계정은 가상 머신을 만든 사용자입니다.

1. 로그인하면 다음 Linux 명령을 사용하여 실행되는 IoT Edge 모듈의 목록을 확인할 수 있습니다.

   ```bash
   sudo iotedge list
   ```

1. IoT Edge 서비스 문제를 해결해야 하는 경우 다음 Linux 명령을 사용합니다.

    1. 서비스 문제를 해결해야 할 경우 서비스 로그를 검색합니다.

       ```bash
       sudo journalctl -u iotedge
       ```

    2. `check` 도구를 사용하여 디바이스의 구성 및 연결 상태를 확인합니다.

       ```bash
       sudo iotedge check
       ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Windows Admin Center의 연결된 디바이스 목록에서 IoT Edge 디바이스를 선택하여 연결합니다.

1. 디바이스 개요 페이지에는 디바이스에 대한 몇 가지 정보가 표시됩니다.

   * **IoT Edge 모듈 목록** 섹션에는 디바이스에서 실행되는 모듈이 표시됩니다. IoT Edge 서비스가 처음 시작되면 **edgeAgent** 모듈만 실행되는 것으로 표시됩니다. edgeAgent 모듈은 기본적으로 실행되며, 디바이스에 배포하는 추가 모듈을 설치하고 시작하는 데 도움이 됩니다.

   * **IoT Edge 상태** 섹션에는 서비스 상태가 표시되며 **활성(실행 중)** 이 보고됩니다.

---

새 IoT Edge 디바이스를 만들면 Azure Portal에 상태 코드 `417 -- The device's deployment configuration is not set`이 표시됩니다. 이 상태는 정상이며 디바이스가 모듈 배포를 수신할 준비가 되었음을 의미합니다.

## <a name="next-steps"></a>다음 단계

디바이스 프로비전 서비스 등록 프로세스를 사용하면 새 디바이스를 프로비전하는 동시에 디바이스 ID 및 디바이스 쌍 태그를 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-cli-at-scale.md)하거나 [Azure Portal을 사용하여 대규모로 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-at-scale.md)하는 방법을 알아봅니다.

다음도 가능합니다.

* 디바이스에 모듈을 배포하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md)를 계속 진행하세요.
* [IoT Edge for Linux on Windows 가상 머신에서 인증서를 관리](how-to-manage-device-certificates.md)하고 파일을 호스트 OS에서 Linux 가상 머신으로 전송하는 방법을 알아봅니다.
* [프록시 서버를 통해 통신하도록 IoT Edge 디바이스를 구성](how-to-configure-proxy-support.md)하는 방법을 알아봅니다.
