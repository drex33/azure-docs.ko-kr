---
title: 대칭 키를 사용하여 Windows 디바이스에서 Linux용 IoT Edge 만들고 프로비전 - Azure IoT Edge | Microsoft Docs
description: 대칭 키로 수동 프로비저닝을 사용하여 IoT Hub Windows 디바이스에서 Linux용 단일 IoT Edge 만들고 프로비전합니다.
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: 319f1af5316e1e1d7d3e8532e7ce397e7788a4da
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132942121"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-using-symmetric-keys"></a>대칭 키를 사용하여 Windows 디바이스에서 Linux용 IoT Edge 만들기 및 프로비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 Windows 디바이스에서 Linux용 IoT Edge 등록 및 프로비전하기 위한 엔드투엔드 지침을 제공합니다.

IoT 허브에 연결하는 모든 디바이스에는 클라우드-디바이스 또는 디바이스-클라우드 통신을 추적하는 데 사용되는 디바이스 ID가 있습니다. IoT 허브 호스트 이름, 디바이스 ID, 디바이스에서 IoT Hub 인증에 사용하는 정보를 포함하는 연결 정보로 디바이스를 구성합니다.

이 문서의 단계는 단일 디바이스를 IoT 허브에 연결하는 수동 프로비저닝이라는 프로세스를 안내합니다. 수동 프로비저닝을 위해 IoT Edge 디바이스를 인증하는 두 가지 옵션이 있습니다.

* **대칭 키:** IoT Hub 새 디바이스 ID를 만들 때 서비스에서 두 개의 키를 만듭니다. 디바이스에 키 중 하나를 저장하고 인증 시 IoT Hub 키를 제시합니다.

  이 인증 방식을 사용하면 빠르게 시작할 수 있지만 안전하지 않습니다.

* **X.509 자체 서명**: 두 개의 x.509 ID 인증서를 만들어 디바이스에 저장합니다. IoT Hub에서 새 디바이스 ID를 만들 때 두 인증서의 지문을 모두 제공합니다. 디바이스는 IoT Hub에 인증 시 인증서 하나를 제시되고 IoT Hub는 인증서가 지문과 일치하는지 확인합니다.

  이 인증 방법은 더 안전하며 프로덕션 시나리오에 권장됩니다.

이 문서에서는 대칭 키를 인증 방법으로 사용하는 방법을 다룹니다. X.509 인증서를 사용하려면 X.509 [인증서를 사용하여 Windows 디바이스에서 Linux용 IoT Edge 만들기 및 프로비저닝을 참조하세요.](how-to-provision-single-device-linux-on-windows-x509.md)

> [!NOTE]
> 설정할 디바이스가 많고 각 디바이스를 수동으로 프로비전하지 않으려는 경우 다음 문서 중 하나를 사용하여 IoT Hub 디바이스 프로비저닝 서비스에서 IoT Edge 작동하는 방법을 알아봅니다.
>
> * [X.509 인증서를 사용하여 대규모로 IoT Edge 디바이스 만들기 및 프로비전](how-to-provision-devices-at-scale-linux-on-windows-x509.md)
> * [TPM을 사용하여 대규모로 IoT Edge 디바이스 만들기 및 프로비전](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)
> * [대칭 키를 사용하여 대규모로 IoT Edge 디바이스 만들기 및 프로비전](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 IoT Edge 디바이스 등록 및 Windows Linux용 IoT Edge 설치에 대해 설명합니다. 이러한 작업에는 이러한 작업을 수행하는 데 사용되는 다양한 필수 구성 조건 및 유틸리티가 있습니다. 계속하기 전에 모든 필수 구성요소가 있는지 확인합니다.

<!-- Device registration prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-register-device.md](../../includes/iot-edge-prerequisites-register-device.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

<!-- Register your device and View provisioning information H2s and content -->
[!INCLUDE [iot-edge-register-device-symmetric.md](../../includes/iot-edge-register-device-symmetric.md)]

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>클라우드 ID를 사용한 디바이스 프로비전

클라우드 ID 및 인증 정보를 사용하여 디바이스를 설정할 준비가 완료되었습니다.

대칭 키를 사용하여 디바이스를 프로비전하려면 디바이스의 **연결 문자열** 가 필요합니다.

Windows Admin Center 또는 관리자 권한 PowerShell 세션을 사용하여 디바이스를 프로비전할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

대상 디바이스의 관리자 권한 PowerShell 세션에서 다음 명령을 실행합니다. 자리 표시자 텍스트를 고유한 값으로 바꿉니다.

```powershell
Provision-EflowVm -provisioningType ManualConnectionString -devConnString "PASTE_DEVICE_CONNECTION_STRING_HERE"
```

`Provision-EflowVM` 명령에 대한 자세한 내용은 [Windows의 Linux용 IoT Edge용 PowerShell 함수](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm)를 참조하세요.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **Azure IoT Edge 디바이스 프로비전** 창의 프로비전 방법 드롭다운에서 **연결 문자열(수동)** 을 선택합니다.

1. 디바이스를 등록한 후 IoT Hub에서 검색한 **디바이스 연결 문자열** 을 제공합니다.

1. **선택한 방법으로 프로비저닝** 을 선택합니다.

   ![디바이스의 연결 문자열을 붙여넣은 후 선택한 방법을 통한 프로비전을 선택함](./media/how-to-provision-single-device-linux-on-windows-symmetric/provisioning-with-selected-method-connection-string.png)

1. 프로비전이 완료되면 **마침** 을 선택합니다. 기본 대시보드로 돌아갑니다. 이제 유형이 `IoT Edge Devices`인 새 디바이스가 표시됩니다. 연결할 IoT Edge 디바이스를 선택할 수 있습니다. **개요** 페이지에서 디바이스의 **IoT Edge 모듈 목록** 및 **IoT Edge 상태** 가 표시됩니다.

---

## <a name="verify-successful-configuration"></a>성공적인 구성 확인

IoT Edge for Linux on Windows가 IoT Edge 디바이스에 성공적으로 설치되고 구성되었는지 확인합니다.

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

    1. 서비스 로그를 검색합니다.

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

* 디바이스에 모듈을 배포하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md)를 계속 진행하세요.
* [IoT Edge for Linux on Windows 가상 머신에서 인증서를 관리](how-to-manage-device-certificates.md)하고 파일을 호스트 OS에서 Linux 가상 머신으로 전송하는 방법을 알아봅니다.
* [프록시 서버를 통해 통신하도록 IoT Edge 디바이스를 구성](how-to-configure-proxy-support.md)하는 방법을 알아봅니다.
