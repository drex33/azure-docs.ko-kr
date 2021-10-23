---
title: Windows에서 가상 TPM을 사용 하 여 장치 프로 비전-Azure IoT Edge | Microsoft Docs
description: Windows 장치에서 시뮬레이션 된 TPM을 사용 하 여 Azure IoT에 지에 대 한 Azure device 프로 비전 서비스 테스트
author: kgremban
ms.author: kgremban
ms.date: 10/06/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0b3d6bba1e99131b5a03b8ba5acfb760566437b1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234758"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-windows"></a>Windows의 TPM을 사용 하 여 대규모로 IoT Edge 장치 만들기 및 프로 비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 TPM (신뢰할 수 있는 플랫폼 모듈)을 사용 하 여 Windows 장치의 Azure IoT Edge를 자동으로 프로 비전 하는 지침을 제공 합니다. [Azure IoT 허브 장치 프로 비전 서비스](../iot-dps/index.yml)를 사용 하 여 IoT Edge 장치를 자동으로 프로 비전 할 수 있습니다. Autoprovisioning 비전 프로세스에 익숙하지 않은 경우 계속 하기 전에 [프로 비전 개요](../iot-dps/about-iot-dps.md#provisioning-process) 를 검토 합니다.

이 문서에서는 두 가지 방법을 설명 합니다. 솔루션의 아키텍처에 따라 기본 설정을 선택 합니다.

- 물리적 TPM 하드웨어를 사용 하 여 Windows 장치를 Autoprovision 합니다.
- 시뮬레이션 된 TPM을 실행 하는 Windows 장치를 Autoprovision 합니다. 이 방법론은 테스트 시나리오로만 권장 됩니다. 시뮬레이션 된 TPM은 물리적 TPM과 동일한 보안을 제공 하지 않습니다.

지침은 사용자의 방법론에 따라 다르므로 앞으로 올바른 탭에 있는지 확인 합니다.

작업은 다음과 같습니다.

# <a name="physical-tpm"></a>[물리적 TPM](#tab/physical-tpm)

* 장치의 프로 비전 정보를 검색 합니다.
* 디바이스에 대한 개별 등록을 만듭니다.
* IoT Edge 런타임을 설치하고 IoT Hub에 디바이스를 연결합니다.

# <a name="simulated-tpm"></a>[시뮬레이션 된 TPM](#tab/simulated-tpm)

* 시뮬레이션 된 TPM을 설정 하 고 프로 비전 정보를 검색 합니다.
* 디바이스에 대한 개별 등록을 만듭니다.
* IoT Edge 런타임을 설치하고 IoT Hub에 디바이스를 연결합니다.

---

## <a name="prerequisites"></a>사전 요구 사항

실제 TPM 및 가상 TPM 솔루션에 대 한 필수 구성 요소는 동일 합니다.

* Windows 개발 컴퓨터. 이 문서에서는 Windows 10을 사용합니다.
* 활성 IoT 허브
* Azure에서 IoT Hub에 연결 된 IoT Hub 장치 프로 비전 서비스의 인스턴스입니다.
  * 장치 프로 비전 서비스 인스턴스가 없는 경우 IoT Hub device 프로 비전 서비스 빠른 시작의 두 섹션에 설명 된 지침을 따르세요.
    - [새 IoT Hub 장치 프로 비전 서비스 만들기](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)
    - [IoT hub 및 장치 프로 비전 서비스 연결](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)
  * 장치 프로 비전 서비스를 실행 한 후 개요 페이지에서 **ID 범위** 값을 복사 합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

> [!NOTE]
> Tpm 2.0는 장치 프로 비전 서비스에 TPM 증명을 사용 하는 경우에 필요 합니다.
>
> TPM을 사용 하는 경우 그룹, 장치 프로 비전 서비스 등록만 만들 수 있습니다.

## <a name="set-up-your-tpm"></a>TPM 설정

# <a name="physical-tpm"></a>[물리적 TPM](#tab/physical-tpm)

이 섹션에서는 TPM에 대 한 등록 ID 및 인증 키를 검색 하는 데 사용할 수 있는 도구를 빌드합니다.

1. [Windows 개발 환경 설정](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) 의 단계에 따라 C 용 Azure IoT 장치 SDK를 설치 하 고 빌드합니다.

1. 관리자 권한 PowerShell 세션에서 다음 명령을 실행 하 여 TPM에 대 한 장치 프로 비전 정보를 검색 하는 SDK 도구를 빌드합니다.

   ```powershell
   cd azure-iot-sdk-c\cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client\tools\tpm_device_provision
   make
   .\tpm_device_provision
   ```

1. 출력 창에 디바이스의 **등록 ID** 및 **인증 키** 가 표시됩니다. 장치 프로 비전 서비스에서 장치에 대 한 개별 등록을 만들 때 나중에 사용 하기 위해 이러한 값을 복사 합니다.

> [!TIP]
> SDK 도구를 사용 하 여 정보를 검색 하지 않으려는 경우 프로 비전 정보를 얻는 다른 방법을 찾아야 합니다. 각 TPM 칩에 고유한 인증 키는 연결 된 TPM 칩 제조업체에서 가져옵니다. TPM 장치에 대 한 고유한 등록 ID를 파생할 수 있습니다. 예를 들어 인증 키의 SHA-256 해시를 만들 수 있습니다.

등록 ID 및 인증 키를 입력 한 후에는 계속할 준비가 된 것입니다.

# <a name="simulated-tpm"></a>[시뮬레이션 된 TPM](#tab/simulated-tpm)

사용 가능한 실제 TPM이 없고 이 프로비저닝 메서드를 테스트하려는 경우에는 디바이스에서 TPM을 시뮬레이션하면 됩니다.

IoT Hub 장치 프로 비전 서비스는 TPM을 시뮬레이트하고 인증 키 및 등록 ID를 반환 하는 샘플을 제공 합니다.

1. 원하는 언어에 따라 다음 목록에서 샘플 중 하나를 선택 합니다.
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

## <a name="create-a-device-provisioning-service-enrollment"></a>장치 프로 비전 서비스 등록 만들기

TPM의 프로 비전 정보를 사용 하 여 장치 프로 비전 서비스에 개별 등록을 만듭니다.

장치 프로 비전 서비스에서 등록을 만들 때 **초기 장치 쌍 상태** 를 선언할 수 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

> [!TIP]
> 이 문서의 단계는 Azure Portal에 대 한 것 이지만 Azure CLI를 사용 하 여 개별 등록를 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)를 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub device 프로 비전 서비스 인스턴스로 이동 합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. **개별 등록 추가** 를 선택 하 고 다음 단계를 완료 하 여 등록을 구성 합니다.

   1. **메커니즘** 의 경우 **TPM** 을 선택합니다.

   1. VM (가상 머신) 또는 물리적 장치에서 복사한 **인증 키** 와 **등록 ID** 를 제공 합니다.

   1. 원하는 경우 장치에 대 한 ID를 제공 합니다. 장치 ID를 제공 하지 않으면 **등록 id** 가 사용 됩니다.

   1. VM 또는 물리적 장치가 IoT Edge 장치 임을 선언 하려면 **True** 를 선택 합니다.

   1. 장치를 연결 하려는 연결 된 IoT hub를 선택 하거나 **새 IoT Hub에 연결** 을 선택 합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 허브 중 하나에 디바이스가 할당됩니다.

   1. 원하는 경우 **초기 장치 쌍 상태** 에 태그 값을 추가 합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 자세한 내용은 [대규모로 IoT Edge 모듈 배포](how-to-deploy-at-scale.md)를 참조하세요.

   1. **저장** 을 선택합니다.

이제 이 디바이스에 대한 등록이 있으므로 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비저닝할 수 있습니다.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

이 섹션에서는 IoT Edge에 대 한 Windows VM 또는 물리적 장치를 준비 합니다. 그런 다음 IoT Edge를 설치 합니다.

IoT Edge 런타임을 설치할 준비가 되기 전에 장치에서 완료 해야 하는 단계가 하나 있습니다. 장치에 컨테이너 엔진이 설치 되어 있어야 합니다.

### <a name="install-iot-edge"></a>IoT Edge 설치

IoT Edge 보안 디먼은 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

이 섹션의 단계는 인터넷에 연결 된 장치에 최신 버전을 설치 하는 일반적인 프로세스를 나타냅니다. 시험판 버전과 같은 특정 버전을 설치 해야 하거나 오프 라인 상태에서 설치 해야 하는 경우 오프 라인 또는 특정 버전의 설치 단계를 따르세요.

1. PowerShell을 관리자 권한으로 실행합니다.

   Powershell (x86)이 아닌 PowerShell의 AMD64 세션을 사용 합니다. 사용 중인 세션 형식을 잘 모르는 경우 다음 명령을 실행합니다.

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. 다음 작업을 수행하는 [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) 명령을 실행합니다.

   * Windows 머신이 지원되는 버전에 해당하는지 확인합니다.
   * 컨테이너 기능을 사용하도록 설정합니다.
   * Moby 엔진과 IoT Edge 런타임을 다운로드 합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. 메시지가 표시되면 디바이스를 다시 시작합니다.

장치에 IoT Edge를 설치 하는 경우 다른 매개 변수를 사용 하 여 프로세스를 다음과 같이 수정할 수 있습니다.

* 프록시 서버를 통과 하는 직접 트래픽
* 오프라인 설치 시 설치 프로그램이 로컬 디렉터리를 가리키도록 합니다.

이러한 다른 매개 변수에 대 한 자세한 내용은 [Windows 컨테이너와 IoT Edge에 대 한 PowerShell 스크립트](reference-windows-scripts.md)를 참조 하세요.

## <a name="configure-the-device-with-provisioning-information"></a>프로비저닝 정보를 사용하여 디바이스 구성

장치에 런타임을 설치한 후 장치 프로 비전 서비스에 연결 하 고 IoT Hub 하는 데 사용 하는 정보를 사용 하 여 장치를 구성 합니다.

1. 이전 섹션에서 수집 된 장치 프로 비전 서비스 **Id 범위** 및 장치 **등록 id** 를 확인 합니다.

1. 관리자 모드에서 PowerShell 창을 엽니다. PowerShell (x86)이 아닌 IoT Edge를 설치 하는 경우에는 PowerShell의 AMD64 세션을 사용 해야 합니다.

1. 이 `Initialize-IoTEdge` 명령은 컴퓨터에 IoT Edge 런타임을 구성 합니다. 이 명령은 Windows 컨테이너를 통한 수동 프로비저닝으로 기본 설정됩니다. `-Dps`수동 프로 비전 대신 장치 프로 비전 서비스를 사용 하려면 플래그를 사용 합니다.

   `{scope_id}` 및 `{registration_id}`의 자리 표시자 값을 이전에 수집한 데이터로 바꿉니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작 되 면 IoT hub로 이동 하 여 장치에 IoT Edge 모듈 배포를 시작 합니다. 디바이스에서 다음 명령을 사용하여 런타임이 성공적으로 설치되고 시작되는지 확인합니다.

1. IoT Edge 서비스의 상태를 확인합니다.

    ```powershell
    Get-Service iotedge
    ```

1. 최근 5분 간의 서비스 로그를 검사합니다.

    ```powershell
    . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
    ```

1. 실행 중인 모듈을 나열합니다.

    ```powershell
    iotedge list
    ```

## <a name="next-steps"></a>다음 단계

장치 프로 비전 서비스 등록 프로세스를 통해 새 장치를 프로 비전 하는 동시에 장치 ID 및 장치 쌍 태그를 설정할 수 있습니다. 자동 장치 관리를 사용 하 여 이러한 값을 사용 하 여 개별 장치 또는 장치 그룹을 대상으로 지정할 수 있습니다.

Azure Portal 또는 [Azure CLI](how-to-deploy-cli-at-scale.md)를 [사용 하 여 규모에 IoT Edge 모듈을 배포 하 고 모니터링](how-to-deploy-at-scale.md) 하는 방법을 알아봅니다.