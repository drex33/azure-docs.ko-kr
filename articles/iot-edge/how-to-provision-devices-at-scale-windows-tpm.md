---
title: Windows 가상 TPM을 통해 디바이스 프로비전 - Azure IoT Edge | Microsoft Docs
description: Windows 디바이스에서 시뮬레이션된 TPM을 사용하여 Azure IoT Edge용 Azure Device Provisioning Service 테스트
author: kgremban
ms.author: kgremban
ms.date: 10/06/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: f130adffa4f5d542082b3ab6271952a867c30cdb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663054"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-windows"></a>Windows TPM을 사용하여 대규모로 IoT Edge 디바이스 만들기 및 프로비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하여 Windows IoT Edge 디바이스를 자동 프로비전하는 엔드 투 엔드 지침을 제공합니다. Azure IoT Hub DPS(Device Provisioning Service)를 Azure IoT Edge 디바이스를 자동으로 [프로비전할](../iot-dps/index.yml) 수 있습니다. 자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [프로비전 개요를](../iot-dps/about-iot-dps.md#provisioning-process) 검토하세요.

이 문서에서는 두 가지 방법을 간략하게 설명합니다. 솔루션의 아키텍처에 따라 기본 설정을 선택합니다.

1. 물리적 TPM 하드웨어를 사용하여 Windows 디바이스를 자동 프로비전합니다.
1. 시뮬레이션된 TPM을 실행하는 Windows 디바이스를 자동으로 프로비전합니다. 시뮬레이션된 TPM은 실제 TPM과 동일한 보안을 제공하지 않으므로 이 방법론은 테스트 시나리오로만 권장됩니다.

지침은 방법론에 따라 다르므로 앞으로 올바른 탭에 있는지 확인합니다.

작업은 다음과 같습니다.

# <a name="physical-tpm"></a>[물리적 TPM](#tab/physical-tpm)

* 디바이스의 프로비전 정보를 검색합니다.
* 디바이스에 대한 개별 등록을 만듭니다.
* IoT Edge 런타임을 설치하고 IoT Hub에 디바이스를 연결합니다.

# <a name="simulated-tpm"></a>[시뮬레이션된 TPM](#tab/simulated-tpm)

* 시뮬레이션된 TPM을 설정하고 프로비전 정보를 검색합니다.
* 디바이스에 대한 개별 등록을 만듭니다.
* IoT Edge 런타임을 설치하고 IoT Hub에 디바이스를 연결합니다.

---

## <a name="prerequisites"></a>사전 요구 사항

필수 구성 조건은 실제 TPM 및 가상 TPM 솔루션에 대해 동일합니다.

* Windows 개발 컴퓨터. 이 문서에서는 Windows 10을 사용합니다.
* 활성 IoT Hub
* Azure의 IoT Hub Device Provisioning Service의 새 인스턴스로 IoT 허브에 연결되어 있습니다.
  * Device Provisioning Service 인스턴스가 없는 경우 IoT Hub Device Provisioning Service 빠른 시작의 [새 IoT Hub Device Provisioning Service 만들기](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) 및 [IoT Hub 및 Device Provisioning Service 연결](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) 섹션의 지침을 따를 수 있습니다.
  * Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

> [!NOTE]
> TPM 증명을 DPS와 함께 사용하는 경우 TPM 2.0이 필요합니다.
>
> TPM을 사용하는 경우 그룹화가 아닌 개별 DPS 등록만 만들 수 있습니다.

## <a name="set-up-your-tpm"></a>TPM 설정

# <a name="physical-tpm"></a>[물리적 TPM](#tab/physical-tpm)

이 섹션에서는 TPM에 대한 **등록 ID** 및 **인증 키를** 검색하는 데 사용할 수 있는 도구를 빌드합니다.

1. Windows 개발 [환경 설정의](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) 단계에 따라 C용 Azure IoT 디바이스 SDK를 설치하고 빌드합니다.

1. 관리자 권한 PowerShell 세션에서 다음 명령을 실행하여 TPM에 대한 디바이스 프로비저닝 정보를 검색하는 SDK 도구를 빌드합니다.

   ```powershell
   cd azure-iot-sdk-c\cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client\tools\tpm_device_provision
   make
   .\tpm_device_provision
   ```

1. 출력 창에 디바이스의 **등록 ID** 및 **인증 키** 가 표시됩니다. 나중에 DPS에서 디바이스에 대한 개별 등록을 만들 때 사용할 이러한 값을 복사합니다.

> [!TIP]
> SDK 도구를 사용하여 정보를 검색하지 않으려면 프로비전 정보를 가져오는 다른 방법을 찾아야 합니다. 각 TPM 칩에 고유한 **인증 키는** 연결된 TPM 칩 제조업체에서 얻습니다. 예를 들어 인증 키의 SHA-256 해시를 만들어 TPM 디바이스에 고유한 **등록 ID** 를 파생할 수 있습니다.

등록 ID 및 인증 키가 있으면 계속할 준비가 된 것입니다.

# <a name="simulated-tpm"></a>[시뮬레이션된 TPM](#tab/simulated-tpm)

사용 가능한 실제 TPM이 없고 이 프로비저닝 메서드를 테스트하려는 경우에는 디바이스에서 TPM을 시뮬레이션하면 됩니다.

IoT Hub Device Provisioning Service TPM을 시뮬레이션하고 **인증 키** 및 **등록 ID를** 반환하는 샘플을 제공합니다.

1. 원하는 언어에 따라 다음 목록에서 샘플 중 하나를 선택합니다.
1. 시뮬레이션된 TPM이 실행되고 **인증 키** 및 **등록 ID** 를 수집한 후 DPS 샘플 단계를 Stop following. *Enter* 키를 눌러 샘플 애플리케이션에서 등록을 실행하지 마세요.
1. 이 시나리오 테스트를 완료할 때까지 시뮬레이션된 TPM을 호스트하는 창을 계속 실행합니다.
1. 이 문서로 돌아와서 DPS 등록을 만들고 디바이스를 구성합니다.

시뮬레이션된 TPM 샘플:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.JS](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

---

## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

TPM의 프로비전 정보를 사용하여 Device Provisioning Service에서 개별 등록을 만듭니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태** 를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

> [!TIP]
> 이 문서의 단계는 Azure Portal 위한 것이지만 Azure CLI 사용하여 개별 등록을 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)를 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. **개별 등록 추가를** 선택한 다음, 다음 단계를 완료하여 등록을 구성합니다.

   1. **메커니즘** 의 경우 **TPM** 을 선택합니다.

   1. 가상 머신 또는 물리적 디바이스에서 복사한 **인증 키** 및 **등록 ID를** 제공합니다.

   1. 원하는 경우 디바이스에 대한 ID를 제공합니다. 디바이스 ID를 제공하지 않으면 등록 ID가 사용됩니다.

   1. 가상 머신 또는 물리적 디바이스가 IoT Edge 디바이스임을 선언하려면 **True를** 선택합니다.

   1. 디바이스를 연결하려는 연결된 IoT Hub를 선택하거나 **새 IoT Hub 연결을** 선택합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 허브 중 하나에 디바이스가 할당됩니다.

   1. 원하는 경우 **초기 디바이스 쌍 상태** 에 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 자세한 내용은 [대규모로 IoT Edge 모듈 배포](how-to-deploy-at-scale.md)를 참조하세요.

   1. **저장** 을 선택합니다.

이제 이 디바이스에 대한 등록이 있으므로 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비저닝할 수 있습니다.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

이 섹션에서는 IoT Edge Windows 가상 머신 또는 물리적 디바이스를 준비합니다. 그런 다음, IoT Edge 설치합니다.

IoT Edge 런타임을 설치할 준비가 되기 전에 디바이스에서 완료해야 하는 단계가 하나 있습니다. 디바이스에 컨테이너 엔진이 설치되어 있어야 합니다.

### <a name="install-iot-edge"></a>IoT Edge 설치

IoT Edge 보안 디먼은 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

이 섹션의 단계는 인터넷에 연결되어 있는 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 나타냅니다. 사전 릴리스 버전과 같은 특정 버전을 설치해야 하거나 오프라인 상태에서 설치해야 하는 경우 오프라인 또는 특정 버전 설치 단계를 수행합니다.

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

런타임이 디바이스에 설치되면 디바이스를 Device Provisioning Service 및 IoT Hub에 연결하는 데 사용되는 정보로 구성합니다.

1. 이전 섹션에서 수집한 DPS **ID 범위** 및 디바이스 **등록 ID** 를 확인합니다.

1. 관리자 모드에서 PowerShell 창을 엽니다. PowerShell(x86)이 아닌 IoT Edge를 설치하는 경우 PowerShell의 AMD64 세션을 사용해야 합니다.

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
