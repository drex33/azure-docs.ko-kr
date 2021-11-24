---
title: TPM Azure IoT Edge를 사용 하 여 Windows 장치에서 Linux 용 IoT Edge 만들기 및 프로 비전 Microsoft Docs
description: Windows 장치의 Linux에서 시뮬레이션 된 TPM을 사용 하 여 Azure IoT에 지에 대 한 Azure device 프로 비전 서비스를 테스트 합니다.
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 1aeedcdd4fbcbe388df2f879834af579d6743318
ms.sourcegitcommit: e9e332a512ed615a3c8ad5a11baa21649f14116d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133095994"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-at-scale-by-using-a-tpm"></a>TPM을 사용 하 여 규모에 따라 Windows 장치에서 Linux 용 IoT Edge 만들기 및 프로 비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 TPM (신뢰할 수 있는 플랫폼 모듈)을 사용 하 여 Windows 장치에서 Linux 용 Azure IoT Edge를 자동 프로 비전 비전 하기 위한 지침을 제공 합니다. [Azure IoT 허브 장치 프로 비전 서비스](../iot-dps/index.yml)를 사용 하 여 Azure IoT에 지 장치를 자동으로 프로 비전 할 수 있습니다. Autoprovisioning 비전 프로세스에 익숙하지 않은 경우 계속 하기 전에 [프로 비전 개요](../iot-dps/about-iot-dps.md#provisioning-process) 를 검토 합니다.

이 문서에서는 두 가지 방법을 설명 합니다. 솔루션의 아키텍처에 따라 기본 설정을 선택 합니다.

- 물리적 TPM 하드웨어를 사용 하 여 Windows 장치에서 Linux를 Autoprovision 합니다.
- 시뮬레이션 된 TPM을 사용 하 여 Windows 장치에서 Linux를 Autoprovision 합니다. 이 방법론은 테스트 시나리오로만 권장 됩니다. 시뮬레이션 된 TPM은 물리적 TPM과 동일한 보안을 제공 하지 않습니다.

작업은 다음과 같습니다.

# <a name="physical-tpm"></a>[물리적 TPM](#tab/physical-tpm)

* IoT Edge for Linux on Windows를 설치합니다.
* 디바이스에서 TPM 정보를 검색합니다.
* 디바이스에 대한 개별 등록을 만듭니다.
* TPM 정보를 사용하여 디바이스를 프로비저닝합니다.

# <a name="simulated-tpm"></a>[시뮬레이션 된 TPM](#tab/simulated-tpm)

* IoT Edge for Linux on Windows를 설치합니다.
* 시뮬레이션 된 TPM을 설정 하 고 프로 비전 정보를 검색 합니다.
* 디바이스에 대한 개별 등록을 만듭니다.
* TPM 정보를 사용하여 디바이스를 프로비저닝합니다.

---

## <a name="prerequisites"></a>필수 구성 요소

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

> [!NOTE]
> Tpm 2.0는 장치 프로 비전 서비스에 TPM 증명을 사용 하는 경우에 필요 합니다.
>
> TPM을 사용 하는 경우 그룹, 장치 프로 비전 서비스 등록만 만들 수 있습니다.

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

TPM을 사용한 프로 비전을 위해 장치를 준비 하는 몇 가지 단계가 있습니다. 장치를 준비 하는 동안 배포를 열어 둡니다. 이 문서의 뒷부분에 나오는 배포로 돌아갑니다.

## <a name="enable-tpm-passthrough"></a>TPM 통과 사용

Windows VM의 Linux에 대 한 IoT Edge에는 사용 하거나 사용 하지 않도록 설정할 수 있는 TPM 기능이 있습니다. 이 기능은 기본적으로 사용하지 않도록 설정됩니다. 이 기능을 사용 하도록 설정 하면 VM이 호스트 컴퓨터의 TPM에 액세스할 수 있습니다.

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

# <a name="physical-tpm"></a>[물리적 TPM](#tab/physical-tpm)

장치를 프로 비전 하려면 TPM 칩에 대 한 **인증 키** 와 장치에 대 한 **등록 ID** 가 필요 합니다. 서비스에서 연결을 시도할 때 장치를 인식할 수 있도록이 정보를 장치 프로 비전 서비스의 인스턴스에 제공 합니다.

인증 키는 각 TPM 칩 마다 고유 합니다. 이는 연결 된 TPM 칩 제조업체에서 가져옵니다. 예를 들어 인증 키의 SHA-256 해시를 만들어 TPM 디바이스의 고유한 등록 ID를 파생할 수 있습니다.

에서 Linux에 대 한 IoT Edge Windows TPM에서이 정보를 검색 하는 데 도움이 되는 PowerShell 스크립트를 제공 합니다. 스크립트를 사용하려면 디바이스에서 다음 단계를 수행합니다.

1. 관리자 권한 세션에서 PowerShell을 엽니다.

1. 명령을 실행합니다.

   ```powershell
   Get-EflowVmTpmProvisioningInfo | Format-List
   ```

# <a name="simulated-tpm"></a>[시뮬레이션 된 TPM](#tab/simulated-tpm)

사용 가능한 실제 TPM이 없고 이 프로비저닝 메서드를 테스트하려는 경우에는 디바이스에서 TPM을 시뮬레이션하면 됩니다.

IoT Hub 장치 프로 비전 서비스는 TPM을 시뮬레이트하고 인증 키 및 등록 ID를 반환 하는 샘플을 제공 합니다.

1. 원하는 언어에 따라 다음 목록에서 샘플 중 하나를 선택합니다.
1. 시뮬레이션 된 TPM을 실행 중이 고 **인증 키** 및 **등록 ID** 를 수집한 후 장치 프로 비전 서비스 샘플 단계를 중지 합니다. **Enter 키** 를 선택 하 여 샘플 응용 프로그램에서 등록을 실행 하지 마세요.
1. 이 시나리오 테스트를 완료할 때까지 시뮬레이션 된 TPM을 호스트 하는 창을 유지 합니다.
1. 이 문서로 돌아와서 장치 프로 비전 서비스 등록을 만들고 장치를 구성 합니다.

시뮬레이션된 TPM 샘플:

* [C](../iot-dps/quick-create-simulated-device-tpm.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm.md)
* [Node.JS](../iot-dps/quick-create-simulated-device-tpm.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm.md)

---

<!-- Create an enrollment for your device using TPM provisioning information H2 and content -->
[!INCLUDE [tpm-create-a-device-provision-service-enrollment.md](../../includes/tpm-create-a-device-provision-service-enrollment.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>클라우드 ID를 사용한 디바이스 프로비전

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Windows 디바이스에서 관리자 권한 PowerShell 세션을 엽니다.

1. 장치 프로 비전 서비스의 인스턴스에서 수집한 **범위 ID** 를 사용 하 여 장치를 프로 비전 합니다.

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "SCOPE_ID_HERE"
   ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **연결** 단계에서 디바이스를 프로비저닝합니다.

   1. **DpsTpm** 프로비저닝 메서드를 선택합니다.
   1. 장치 프로 비전 서비스의 인스턴스에서 검색 하는 **범위 ID** 를 제공 합니다.

      ![장치 프로 비전 서비스 및 TPM 증명을 사용 하 여 장치를 프로 비전 하는 방법을 보여 주는 스크린샷](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. **선택한 방법으로 프로비저닝** 을 선택합니다.

1. 장치에 IoT Edge를 설치 하 고 프로 비전 한 후 **마침** 을 선택 하 여 배포 마법사를 종료 합니다.

---

## <a name="verify-successful-installation"></a>성공적인 설치 확인

IoT Edge for Linux on Windows가 IoT Edge 디바이스에 성공적으로 설치되고 구성되었는지 확인합니다.

런타임이 성공적으로 시작 되 면 IoT hub로 이동 하 여 장치에 IoT Edge 모듈 배포를 시작할 수 있습니다.

장치 프로 비전 서비스에서 만든 개별 등록이 사용 되었는지 확인할 수 있습니다. Azure Portal에서 장치 프로 비전 서비스 인스턴스로 이동 합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

디바이스에서 다음 명령을 사용하여 IoT Edge가 성공적으로 설치되고 시작되는지 확인합니다.

1. PowerShell 세션에서 다음 명령을 사용 하 여 Windows VM에서 Linux 용 IoT Edge에 커넥트 합니다.

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >VM에 대 한 SSH를 허용 하는 유일한 계정은 VM을 만든 사용자입니다.

1. 로그인 한 후에는 다음 Linux 명령을 사용 하 여 실행 중인 IoT Edge 모듈 목록을 확인할 수 있습니다.

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

   * **IoT Edge 모듈 목록** 섹션에는 디바이스에서 실행되는 모듈이 표시됩니다. IoT Edge 서비스가 처음 시작되면 **edgeAgent** 모듈만 실행되는 것으로 표시됩니다. EdgeAgent 모듈은 기본적으로 실행 되며 장치에 배포 하는 다른 모듈을 설치 하 고 시작 하는 데 도움이 됩니다.
   * **IoT Edge 상태** 섹션은 서비스 상태를 표시 하 고 **활성 (실행 중)** 으로 보고 해야 합니다.

---

## <a name="next-steps"></a>다음 단계

장치 프로 비전 서비스 등록 프로세스를 통해 새 장치를 프로 비전 하는 동시에 장치 ID 및 장치 쌍 태그를 설정할 수 있습니다. 자동 장치 관리를 사용 하 여 이러한 값을 사용 하 여 개별 장치 또는 장치 그룹을 대상으로 지정할 수 있습니다.

Azure Portal 또는 [Azure CLI](how-to-deploy-cli-at-scale.md)를 [사용 하 여 규모에 IoT Edge 모듈을 배포 하 고 모니터링](how-to-deploy-at-scale.md) 하는 방법을 알아봅니다.
