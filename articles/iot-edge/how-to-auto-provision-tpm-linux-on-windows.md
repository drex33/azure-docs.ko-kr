---
title: DPS 및 TPM을 사용하여 Windows 디바이스 자동 프로비저닝 - Azure IoT Edge | Microsoft Docs
description: Device Provisioning Service 및 TPM 증명을 통해 IoT Edge for Linux on Windows에 자동 디바이스 프로비저닝 사용
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 07/08/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 03ed23fbc22c632e3bc9a0e5afeb1163e7f99c80
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278503"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-with-tpm-attestation"></a>TPM 증명을 사용하여 IoT Edge for Linux on Windows 디바이스를 만들고 프로비저닝

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

에지를 사용하지 않는 디바이스와 마찬가지로 [Device Provisioning Service](../iot-dps/index.yml)를 사용하여 Azure IoT Edge 디바이스를 자동 프로비저닝할 수 있습니다. 자동 프로비저닝 프로세스에 익숙하지 않은 경우 계속하기 전에 [프로비저닝](../iot-dps/about-iot-dps.md#provisioning-process) 개요를 검토하세요.

DPS는 그룹 등록이 아닌 개별 등록에서만 IoT Edge 디바이스에 TPM(신뢰할 수 있는 플랫폼 모듈) 증명을 지원합니다.

이 문서에서는 다음 단계에 따라 IoT Edge for Linux on Windows를 실행하는 디바이스에서 자동 프로비저닝을 사용하는 방법을 보여줍니다.

* IoT Edge for Linux on Windows를 설치합니다.
* 디바이스에서 TPM 정보를 검색합니다.
* 디바이스에 대한 개별 등록을 만듭니다.
* TPM 정보를 사용하여 디바이스를 프로비저닝합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Windows 디바이스. 지원되는 Windows 버전은 [운영 체제](support.md#operating-systems)를 참조하세요.
* 활성 IoT 허브
* Azure의 IoT Hub Device Provisioning Service의 새 인스턴스로 IoT 허브에 연결되어 있습니다.
  * Device Provisioning Service 인스턴스가 없는 경우 [IOT HUB DPS 설정](../iot-dps/quick-setup-auto-provision.md)의 지침을 따르세요.
  * Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. 이 값은 IoT Edge 디바이스를 프로비저닝할 때 사용합니다.

> [!NOTE]
> TPM 2.0은 DPS에서 TPM 증명을 사용할 때 필요하며 그룹이 아닌 개별 등록을 만드는 데만 사용할 수 있습니다.

## <a name="install-iot-edge-for-linux-on-windows"></a>Windows에서 Linux용 IoT Edge 설치

이 섹션의 설치 단계는 TPM 프로비저닝 시나리오와 관련된 단계를 중심으로 요약되었습니다. 필수 구성 요소 및 원격 설치 단계를 비롯한 자세한 지침은 [Windows 디바이스에 Azure IoT Edge for Linux 설치 및 프로비저닝](how-to-install-iot-edge-on-windows.md)을 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Windows 디바이스에서 관리자 권한 PowerShell 세션을 엽니다.

1. IoT Edge for Linux on Windows를 다운로드합니다.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. IoT Edge for Linux on Windows를 디바이스에 설치합니다.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

1. 배포를 성공적으로 실행하려면 디바이스의 실행 정책을 `AllSigned`로 설정해야 합니다(아직 설정하지 않은 경우).

   1. 현재 실행 정책을 확인하세요.

      ```powershell
      Get-ExecutionPolicy -List
      ```

   1. `local machine`의 실행 정책이 `AllSigned`가 아닌 경우 실행 정책을 업데이트합니다.

      ```powershell
      Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
      ```

1. IoT Edge for Linux on Windows를 배포합니다.

   ```powershell
   Deploy-Eflow
   ```

1. `Y`를 입력하여 사용 조건에 동의합니다.

1. 기본 설정에 따라 `O` 또는 `R`을 입력하여 **선택적 진단 데이터** 를 설정하거나 해제합니다.

1. 디바이스에 IoT Edge for Linux on Windows가 성공적으로 배포되면 출력에 **Deployment successful** 이 보고됩니다.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Windows Admin Center용 Azure IoT Edge 확장은 현재 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)입니다. 설치 및 관리 프로세스는 일반적으로 사용할 수 있는 기능과 다를 수 있습니다.

1. **Azure IoT Edge** 확장을 사용하여 Windows Admin Center를 구성합니다.

1. Windows Admin Center 연결 페이지에서 **추가** 를 선택합니다.

1. **리소스 추가 또는 만들기** 창에서 **Azure IoT Edge** 타일을 찾습니다. **새로 만들기** 를 선택하여 새 Azure IoT Edge for Linux on Windows 인스턴스를 디바이스에 설치합니다.

1. 배포 마법사의 단계에 따라 IoT Edge for Linux on Windows를 설치하고 구성합니다.

   1. **시작** 단계에서 필수 구성 요소를 검토하고, 사용 조건에 동의하고, 진단 데이터를 보낼지 여부를 선택합니다.

   1. **배포** 단계에서 디바이스 및 디바이스의 구성 설정을 선택합니다. 그런 다음, IoT Edge가 디바이스에 배포될 때 진행률을 관찰합니다.

   1. **다음** 을 선택하여 **연결** 단계를 계속 진행합니다. 이 단계에서 디바이스의 프로비저닝 정보를 제공합니다.

1. 계속하기 전에 TPM을 사용하여 디바이스 프로비저닝을 준비하기 위해 수행해야 하는 몇 가지 단계가 있으므로 **프로비저닝** 페이지에서 배포 마법사의 다음 단계를 중지합니다. Windows Admin Center 창을 계속 열어 둡니다. 이 문서를 마친 후 프로비저닝 단계를 완료하기 위해 다시 이 창으로 돌아가야 합니다.

---

## <a name="enable-tpm-passthrough"></a>TPM 통과 사용

IoT Edge for Linux on Windows 가상 머신에는 사용하도록 또는 사용하지 않도록 설정할 수 있는 TPM 기능이 있습니다. 이 기능은 기본적으로 사용하지 않도록 설정됩니다. 이 기능을 사용하도록 설정하면 가상 머신이 호스트 머신의 TPM에 액세스할 수 있습니다.

1. 관리자 권한 세션에서 PowerShell을 엽니다.

1. 아직 디바이스에서 실행 정책을 `AllSigned`로 설정하지 않은 경우 지금 설정합니다. 그래야만 IoT Edge for Linux on Windows PowerShell 함수를 실행할 수 있습니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. TPM 기능을 켭니다.

   ```powershell
   Set-EflowVmFeature -feature 'DpsTpm' -enable
   ```

## <a name="retrieve-the-tpm-information-from-your-device"></a>디바이스에서 TPM 정보 검색

디바이스를 프로비저닝하려면 TPM 칩에서 정보를 수집하여 DPS(Device Provisioning Service) 인스턴스에 제공해야만 이 서비스가 연결을 시도할 때 디바이스를 인식할 수 있습니다.

먼저 **인증 키** 를 확인해야 합니다. 이 키는 각 TPM 칩에서 고유하고 연결된 TPM 칩 제조업체에서 가져옵니다. 그런 다음, 디바이스의 **등록 ID** 를 제공해야 합니다. 예를 들어 인증 키의 SHA-256 해시를 만들어 TPM 디바이스의 고유한 등록 ID를 파생할 수 있습니다.

IoT Edge for Linux on Windows는 TPM에서 이 정보를 검색하는 데 도움이 되는 PowerShell 함수를 제공합니다. 스크립트를 사용하려면 디바이스에서 다음 단계를 수행합니다.

1. 관리자 권한 세션에서 PowerShell을 엽니다.

1. 명령을 실행합니다.

   ```powershell
   Get-EflowVmTpmProvisioningInfo
   ```

### <a name="simulate-a-tpm-optional"></a>TPM 시뮬레이션(선택 사항)

사용 가능한 실제 TPM이 없고 이 프로비저닝 메서드를 테스트하려는 경우에는 디바이스에서 TPM을 시뮬레이션하면 됩니다.

IoT Hub Device Provisioning Service는 TPM을 시뮬레이션하고 인증 키와 등록 ID를 반환하는 샘플을 제공합니다.

1. 원하는 언어에 따라 다음 목록에서 샘플 중 하나를 선택합니다.
1. 시뮬레이션된 TPM이 실행 중이고 인증 키 및 등록 ID를 수집한 후에는 DPS 샘플 단계를 중지합니다. *Enter* 키를 눌러 샘플 애플리케이션에서 등록을 실행하지 마세요.
1. 이 시나리오의 테스트가 완전히 완료될 때까지 시뮬레이션된 TPM을 호스트하는 창을 계속 유지합니다.
1. 이 문서로 돌아와서 DPS 등록을 만들고 디바이스를 구성합니다.

시뮬레이션 된 TPM 장치에 대 한 개별 등록을 만드는 방법을 알아보려면 [시뮬레이트된 tpm 장치 프로 비전 빠른 시작 가이드](../iot-dps/quick-create-simulated-device-tpm.md)를 참조 하세요.


## <a name="create-a-device-enrollment-entry"></a>디바이스 등록 항목 만들기

Device Provisioning Service는 다음과 같은 두 가지 등록 유형을 지원합니다. 등록 그룹은 여러 관련 디바이스를 등록하는 데 사용됩니다. 개별 등록은 단일 디바이스를 등록하는 데 사용됩니다. TPM 증명은 개별 등록만 지원합니다. 디바이스 그룹은 TPM 정보를 공유할 수 없기 때문입니다. 따라서 이 문서에서는 개별 등록을 시연합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고, Device Provisioning Service 인스턴스로 이동합니다.

1. Device Provisioning Service 메뉴에서 **등록 관리** 를 선택합니다. 위쪽에서 **개별 등록 추가** 단추를 선택합니다.

1. **등록 추가** 패널에서 다음 정보를 입력합니다.

   * ID 증명 메커니즘으로 **TPM** 을 선택합니다.
   * 앞에서 기록한 값에서 TPM 디바이스의 등록 ID 및 인증 키를 입력합니다.
   * 프로비전 서비스와 연결된 IoT Hub를 선택합니다.
   * 선택 사항으로 고유한 디바이스 ID를 입력합니다. 제공하지 않도록 선택하면 등록 ID가 대신 디바이스를 식별하는 데 사용됩니다.
   * **True** 를 선택하여 등록이 IoT Edge 디바이스에 대한 것이라고 선언합니다.

1. **저장** 을 선택하여 개별 등록을 저장합니다.

## <a name="configure-the-device-with-provisioning-information"></a>프로비저닝 정보를 사용하여 디바이스 구성

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Windows 디바이스에서 관리자 권한 PowerShell 세션을 엽니다.

1. Device Provisioning Service 인스턴스에서 수집한 **범위 ID** 를 사용하여 디바이스를 프로비저닝합니다.

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "<scope id>"
   ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **연결** 단계에서 디바이스를 프로비저닝합니다.

   1. **DpsTpm** 프로비저닝 메서드를 선택합니다.
   1. Device Provisioning Service의 인스턴스에서 검색하는 **범위 ID** 를 제공합니다.

   ![DPS 및 TPM 증명을 사용하여 디바이스를 프로비저닝합니다.](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. **선택한 방법으로 프로비저닝** 을 선택합니다.

1. IoT Edge가 성공적으로 디바이스에 설치되고 프로비저닝되었으면 **마침** 을 선택하여 배포 마법사를 종료합니다.

---

## <a name="verify-successful-configuration"></a>성공적인 구성 확인

IoT Edge for Linux on Windows가 IoT Edge 디바이스에 성공적으로 설치되고 구성되었는지 확인합니다.

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하고 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다.

Device Provisioning Service에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure Portal에서 Device Provisioning Service 인스턴스로 이동합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

디바이스에서 다음 명령을 사용하여 IoT Edge가 성공적으로 설치되고 시작되는지 확인합니다.

1. PowerShell 세션에서 다음 명령을 사용하여 IoT Edge for Linux on Windows 가상 머신에 연결합니다.

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

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 디바이스를 프로비전할 때 디바이스 ID 및 디바이스 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-cli-at-scale.md)하거나 [Azure Portal을 사용하여 대규모 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-at-scale.md)하는 방법을 알아봅니다.
