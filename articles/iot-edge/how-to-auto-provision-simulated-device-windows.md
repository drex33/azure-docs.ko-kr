---
title: DPS를 사용하여 자동으로 Windows 디바이스 프로비전 - Azure IoT Edge | Microsoft Docs
description: Windows 컴퓨터에서 시뮬레이션된 디바이스를 사용하여 Device Provisioning Service로 Azure IoT Edge에 대한 자동 디바이스 프로비전 테스트
author: kgremban
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 01d0fc11b6aaa7e3a0a874bf60912883619f9ae8
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273441"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Windows에서 가상 TPM을 사용하여 시뮬레이션된 IoT Edge 디바이스 만들기 및 프로비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Edge를 사용하지 않는 디바이스와 마찬가지로 [Device Provisioning Service](../iot-dps/index.yml)를 사용하여 Azure IoT Edge 디바이스를 자동 프로비전할 수 있습니다. 자동 프로비저닝 프로세스에 익숙하지 않은 경우 계속하기 전에 [프로비저닝](../iot-dps/about-iot-dps.md#provisioning-process) 개요를 검토하세요.

DPS는 개별 등록 및 그룹 등록 모두에서 IoT Edge 디바이스에 대한 대칭 키 증명을 지원합니다. 그룹 등록의 경우 대칭 키 증명에서 "IoT Edge 디바이스임" 옵션을 true로 선택하면 해당 등록 그룹에 등록된 모든 디바이스가 IoT Edge 디바이스로 표시됩니다.

이 문서에서는 다음 단계를 사용하여 시뮬레이션된 IoT Edge 디바이스에서 자동 프로비전을 테스트하는 방법을 보여 줍니다.

* IoT Hub DPS(Device Provisioning Service)의 인스턴스를 만듭니다.
* 하드웨어 보안을 위해 시뮬레이션된 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하여 Windows 컴퓨터에서 시뮬레이션된 디바이스를 만듭니다.
* 디바이스에 대한 개별 등록을 만듭니다.
* IoT Edge 런타임을 설치하고 IoT Hub에 디바이스를 연결합니다.

> [!TIP]
> 이 문서에서는 가상 디바이스에서 TPM 증명을 사용하여 자동 프로비전을 테스트하는 방법에 대해 설명하지만, 물리적 TPM 하드웨어를 사용하는 경우에도 대부분 적용됩니다.

## <a name="prerequisites"></a>필수 구성 요소

* Windows 개발 컴퓨터. 이 문서에서는 Windows 10을 사용합니다.
* 활성 IoT Hub

> [!NOTE]
> TPM 2.0은 DPS에서 TPM 증명을 사용할 때 필요하며 그룹이 아닌 개별 등록을 만드는 데만 사용할 수 있습니다.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service 설정

Azure에서 IoT Hub Device Provisioning Service의 새 인스턴스를 만들어 IoT hub에 연결합니다. [IoT Hub DPS 설정](../iot-dps/quick-setup-auto-provision.md) 지침을 따르면 됩니다.

Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

> [!TIP]
> 물리적 TPM 디바이스를 사용하는 경우 각 TPM 칩에서 고유하고 연결된 TPM 칩 제조업체에서 가져온 **인증 키** 를 확인해야 합니다. 예를 들어 인증 키의 SHA-256 해시를 만들어 TPM 디바이스에 고유한 **등록 ID** 를 파생할 수 있습니다.
>
> [Azure Portal을 사용하여 디바이스 등록을 관리하는 방법](../iot-dps/how-to-manage-enrollments.md) 문서의 지침에 따라 DPS에서 등록을 만든 다음, 이 문서의 [IoT Edge 런타임 설치](#install-the-iot-edge-runtime) 섹션으로 계속 진행합니다.

## <a name="simulate-a-tpm-device"></a>TPM 디바이스 시뮬레이션

Windows 개발 머신에서 시뮬레이션된 TPM 디바이스를 만듭니다. 디바이스의 **등록 ID** 및 **인증 키** 를 검색하고, 이를 사용하여 DPS에서 개별 등록 항목을 만듭니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태** 를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭을 기준으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

시뮬레이션된 디바이스를 만드는 데 사용할 SDK 언어를 선택하고 개별 등록을 만들 때까지 단계를 진행합니다.

개별 등록을 만들 때 **True** 를 선택하여 Windows 개발 컴퓨터에서 시뮬레이션된 TPM 다바이스가 **IoT Edge 디바이스** 임을 선언합니다.

> [!TIP]
> Azure CLI에서 [등록](/cli/azure/iot/dps/enrollment) 또는 [등록 그룹](/cli/azure/iot/dps/enrollment-group)을 만들고, **edge-enabled** 플래그를 사용하여 디바이스 또는 디바이스 그룹이 IoT Edge 디바이스임을 지정할 수 있습니다.

개별 등록을 만드는 방법을 알아보려면 [시뮬레이션된 TPM 디바이스 프로비전 빠른 시작 가이드를 참조하세요.](../iot-dps/quick-create-simulated-device-tpm.md)


개별 등록을 만든 후 **등록 ID** 값을 저장합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

IoT Edge 런타임은 모든 IoT Edge 디바이스에 배포되며, 해당 구성 요소는 컨테이너에서 실행되며, Edge에서 코드를 실행할 수 있도록 디바이스에 추가 컨테이너의 배포를 허용합니다. 시뮬레이션된 TPM을 실행하는 디바이스에 IoT Edge 런타임을 설치합니다.

[Azure IoT Edge 런타임 설치](how-to-install-iot-edge.md)의 단계를 수행한 다음, 이 문서로 돌아와서 디바이스를 프로비전합니다.

> [!TIP]
> 설치 및 테스트를 수행하는 동안 TPM 시뮬레이터를 실행하는 창을 열린 상태로 유지합니다.

## <a name="configure-the-device-with-provisioning-information"></a>프로비전 정보를 사용하여 디바이스 구성

런타임이 디바이스에 설치되면 디바이스를 Device Provisioning Service 및 IoT Hub에 연결하는 데 사용되는 정보로 구성합니다.

1. 이전 섹션에서 수집한 DPS **ID 범위** 및 디바이스 **등록 ID** 를 확인합니다.

1. 관리자 모드에서 PowerShell 창을 엽니다. PowerShell(x86)이 아닌 IoT Edge를 설치하는 경우 PowerShell의 AMD64 세션을 사용해야 합니다.

1. **Deploy-IoTEdge** 명령은 지원되는 버전의 Windows 컴퓨터인지 확인하고, 컨테이너 기능을 설정한 다음, moby 런타임 및 IoT Edge 런타임을 다운로드합니다. 명령은 기본적으로 Windows 컨테이너를 사용하도록 설정됩니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. 이 시점에서 IoT Core 디바이스가 자동으로 다시 시작될 수 있습니다. Windows 10 또는 Windows Server 디바이스에서 다시 시작하라는 메시지를 표시할 수 있습니다. 그렇다면 지금 디바이스를 다시 시작합니다. 디바이스가 준비되면 관리자 권한으로 PowerShell을 다시 실행합니다.

1. **Initialize IoTEdge** 명령은 사용자의 머신에서 IoT Edge 런타임을 구성합니다. 이 명령은 Windows 컨테이너를 통한 수동 프로비저닝으로 기본 설정됩니다. 수동 프로비전 대신 Device Provisioning Service를 사용하려면 `-Dps` 플래그를 사용합니다.

   `{scope_id}` 및 `{registration_id}`의 자리 표시자 값을 이전에 수집한 데이터로 바꿉니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하고 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다. 디바이스에서 다음 명령을 사용하여 런타임이 성공적으로 설치되고 시작되는지 확인합니다.  

IoT Edge 서비스의 상태를 확인합니다.

```powershell
Get-Service iotedge
```

최근 5분 간의 서비스 로그를 검사합니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

실행 중인 모듈을 나열합니다.

```powershell
iotedge list
```

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 디바이스를 프로비전할 때 디바이스 ID 및 디바이스 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-cli-at-scale.md)하거나 [Azure Portal을 사용하여 대규모 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-at-scale.md)하는 방법을 알아봅니다.
