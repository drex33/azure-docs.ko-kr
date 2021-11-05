---
title: Windows의 Linux에서 대칭 키를 사용 하 여 IoT Edge 장치 만들기 및 프로 비전-Azure IoT Edge | Microsoft Docs
description: 대칭 키 증명을 사용 하 여 장치 프로 비전 서비스와 Azure IoT에 대 한 대규모로 Windows 장치에서 프로 비전 Linux 테스트
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 8b11313c7ff443226352558ffb172c3bbf59964b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852126"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-symmetric-keys"></a>대칭 키를 사용 하 여 대규모로 Windows 장치에서 Linux 용 IoT Edge 만들기 및 프로 비전 

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 대칭 키를 사용 하 여 [Windows 장치에서 Linux에 대 한](iot-edge-for-linux-on-windows.md) 하나 이상의 IoT Edge를 자동으로 프로 비전 하는 종단 간 지침을 제공 합니다. [Azure IoT 허브 장치 프로 비전 서비스](../iot-dps/index.yml) (DPS)를 사용 하 여 Azure IoT에 지 장치를 자동으로 프로 비전 할 수 있습니다. Autoprovisioning 비전 프로세스에 익숙하지 않은 경우 계속 하기 전에 [프로 비전 개요](../iot-dps/about-iot-dps.md#provisioning-process) 를 검토 합니다.

작업은 다음과 같습니다.

1. 단일 장치에 대 한 **개별 등록** 또는 장치 집합에 대 한 **그룹 등록** 을 만듭니다.
1. IoT Edge 런타임을 설치한 Linux 가상 머신을 배포 하 고 IoT Hub에 연결 합니다.

대칭 키 증명은 장치 프로 비전 서비스 인스턴스를 사용 하 여 장치를 인증 하는 간단한 방법입니다. 이 증명 방법은 디바이스 프로비저닝을 처음 사용하는 개발자나 엄격한 보안 요구 사항이 없는 개발자를 위한 "Hello World" 환경을 나타냅니다. [TPM](../iot-dps/concepts-tpm-attestation.md) 또는 [X.509 인증서](../iot-dps/concepts-x509-attestation.md)를 사용하는 디바이스 증명은 더욱 안전하며, 보다 엄격한 보안 요구 사항에 사용해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>클라우드 ID를 사용한 디바이스 프로비전

런타임이 장치에 설치 되 면 장치 프로 비전 서비스에 연결 하는 데 사용 하는 정보를 사용 하 여 장치를 구성 하 고 IoT Hub 합니다.

다음 정보를 준비합니다.

* DPS **ID 범위** 값
* 만든 디바이스 **등록 ID**
* 개별 등록의 **기본 키** 또는 그룹 등록을 사용하는 장치의 [파생 키](#derive-a-device-key) 중 하나입니다.

PowerShell 또는 Windows Admin Center를 사용하여 IoT Edge 디바이스를 프로비저닝할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell의 경우 자체 값으로 업데이트된 자리 표시자 값을 사용하여 다음 명령을 실행합니다.

```powershell
Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -symmKey PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows Admin Center의 경우 다음 단계를 사용합니다.

1. **Azure IoT Edge 디바이스 프로비전** 창의 프로비전 방법 드롭다운에서 **대칭 키(DPS)** 를 선택합니다.

1. [Azure Portal](https://ms.portal.azure.com/)에서 DPS 인스턴스로 이동합니다.

1. Windows Admin Center 필드에 DPS 범위 ID, 장치 등록 ID, 등록 기본 키, 파생 키를 제공합니다.

1. **선택한 방법을 통한 프로비전** 을 선택합니다.

   ![대칭 키 프로 비전에 대 한 필수 필드인 PNG를 입력 한 후 선택한 방법으로 프로 비전을 선택 합니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/provisioning-with-selected-method-symmetric-key.png)

1. 프로비전이 완료되면 **마침** 을 선택합니다. 기본 대시보드로 돌아갑니다. 이제 유형이 `IoT Edge Devices`인 새 디바이스가 표시됩니다. 연결할 IoT Edge 디바이스를 선택할 수 있습니다. **개요** 페이지에서 디바이스의 **IoT Edge 모듈 목록** 및 **IoT Edge 상태** 가 표시됩니다.

---

## <a name="verify-successful-installation"></a>성공적인 설치 확인

IoT Edge for Linux on Windows가 IoT Edge 디바이스에 성공적으로 설치되고 구성되었는지 확인합니다.

# <a name="individual-enrollment"></a>[개별 등록](#tab/individual-enrollment)

장치 프로 비전 서비스에서 만든 개별 등록이 사용 되었는지 확인할 수 있습니다. Azure Portal에서 장치 프로 비전 서비스 인스턴스로 이동 합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

# <a name="group-enrollment"></a>[그룹 등록](#tab/group-enrollment)

장치 프로 비전 서비스에서 만든 그룹 등록이 사용 되었는지 확인할 수 있습니다. Azure Portal에서 장치 프로 비전 서비스 인스턴스로 이동 합니다. 만든 그룹 등록에 대 한 등록 정보를 엽니다. **등록 레코드** 탭으로 이동 하 여 해당 그룹에 등록 된 모든 장치를 볼 수 있습니다.

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

    1. 서비스 로그를 검색 합니다.

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

장치 프로 비전 서비스 등록 프로세스를 통해 새 장치를 프로 비전 하는 동시에 장치 ID 및 장치 쌍 태그를 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-cli-at-scale.md)하거나 [Azure Portal을 사용하여 대규모로 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-at-scale.md)하는 방법을 알아봅니다.

다음도 가능합니다.

* 디바이스에 모듈을 배포하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md)를 계속 진행하세요.
* [IoT Edge for Linux on Windows 가상 머신에서 인증서를 관리](how-to-manage-device-certificates.md)하고 파일을 호스트 OS에서 Linux 가상 머신으로 전송하는 방법을 알아봅니다.
* [프록시 서버를 통해 통신하도록 IoT Edge 디바이스를 구성](how-to-configure-proxy-support.md)하는 방법을 알아봅니다.
