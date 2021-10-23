---
title: Linux에서 가상 TPM을 사용 하 여 장치 프로 비전-Azure IoT Edge
description: Linux 장치에서 시뮬레이션 된 TPM을 사용 하 여 Azure IoT에 지에 대 한 Azure IoT 허브 장치 프로 비전 서비스를 테스트 합니다.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 07/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e55d4f2bcb6e8f86a45d22e4a3a16555fefe8f81
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246627"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-linux"></a>Linux에서 TPM을 사용 하 여 대규모로 IoT Edge 장치 만들기 및 프로 비전

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

이 문서에서는 TPM (신뢰할 수 있는 플랫폼 모듈)을 사용 하 여 Linux 장치용 Azure IoT Edge를 자동으로 프로 비전 하는 지침을 제공 합니다. [Azure IoT 허브 장치 프로 비전 서비스](../iot-dps/index.yml)를 사용 하 여 IoT Edge 장치를 자동으로 프로 비전 할 수 있습니다. Autoprovisioning 비전 프로세스에 익숙하지 않은 경우 계속 하기 전에 [프로 비전 개요](../iot-dps/about-iot-dps.md#provisioning-process) 를 검토 합니다.

이 문서에서는 두 가지 방법을 설명 합니다. 솔루션의 아키텍처에 따라 기본 설정을 선택 합니다.

- 물리적 TPM 하드웨어를 사용 하 여 Linux 장치를 Autoprovision 합니다. 예는 [BROADCOMM OPTIGA &trade; TPM SLB 9670](https://devicecatalog.azure.com/devices/3f52cdee-bbc4-d74e-6c79-a2546f73df4e)입니다.
- hyper-v를 사용 하는 Windows 개발 컴퓨터에서 실행 되는 시뮬레이션 된 TPM을 사용 하 여 Linux VM (가상 머신)을 Autoprovision 합니다. 이 방법론은 테스트 시나리오로만 사용 하는 것이 좋습니다. 시뮬레이션 된 TPM은 물리적 TPM과 동일한 보안을 제공 하지 않습니다.

지침은 사용자의 방법론에 따라 다르므로 앞으로 올바른 탭에 있는지 확인 합니다.

# <a name="physical-device"></a>[물리적 디바이스](#tab/physical-device)

작업은 다음과 같습니다.

1. TPM에 대 한 프로 비전 정보를 검색 합니다.
1. IoT Hub 장치 프로 비전 서비스의 인스턴스에서 장치에 대 한 개별 등록을 만듭니다.
1. IoT Edge 런타임을 설치 하 고 장치를 IoT hub에 연결 합니다.

# <a name="virtual-machine"></a>[가상 머신](#tab/virtual-machine)

작업은 다음과 같습니다.

1. 하드웨어 보안을 위해 시뮬레이트된 TPM을 사용 하 여 Hyper-v에서 Linux VM을 만듭니다.
1. TPM에 대 한 프로 비전 정보를 검색 합니다.
1. IoT Hub 장치 프로 비전 서비스의 인스턴스에서 장치에 대 한 개별 등록을 만듭니다.
1. IoT Edge 런타임을 설치 하 고 장치를 IoT hub에 연결 합니다.

---

## <a name="prerequisites"></a>사전 요구 사항

# <a name="physical-device"></a>[물리적 디바이스](#tab/physical-device)

* 활성 IoT 허브
* Azure에서 IoT Hub에 연결 된 IoT Hub 장치 프로 비전 서비스의 인스턴스입니다.
  * 장치 프로 비전 서비스 인스턴스가 없는 경우 IoT Hub 장치 프로 비전 서비스 빠른 시작의 두 섹션에 있는 지침을 따르세요.
     - [새 IoT Hub 장치 프로 비전 서비스 만들기](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)
     - [IoT hub 및 장치 프로 비전 서비스 연결](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)
  * 장치 프로 비전 서비스를 실행 한 후 개요 페이지에서 **ID 범위** 값을 복사 합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

# <a name="virtual-machine"></a>[가상 머신](#tab/virtual-machine)

* 활성 IoT 허브
* Azure에서 IoT Hub에 연결 된 IoT Hub 장치 프로 비전 서비스의 인스턴스입니다.
  * 장치 프로 비전 서비스 인스턴스가 없는 경우 IoT Hub device 프로 비전 서비스 빠른 시작의 두 섹션에 설명 된 지침을 따르세요.
    - [새 IoT Hub 장치 프로 비전 서비스 만들기](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)
    - [IoT hub 및 장치 프로 비전 서비스 연결](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)
  * 장치 프로 비전 서비스를 실행 한 후 개요 페이지에서 **ID 범위** 값을 복사 합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.
* [Hyper-V를 사용하도록 설정된](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) Windows 개발 컴퓨터. 이 문서에서는 Ubuntu Server VM을 실행하는 Windows 10을 사용합니다.

---

> [!NOTE]
> Tpm 2.0는 장치 프로 비전 서비스에 TPM 증명을 사용 하는 경우에 필요 합니다.
>
> TPM을 사용 하는 경우 그룹, 장치 프로 비전 서비스 등록만 만들 수 있습니다.

## <a name="set-up-your-device"></a>디바이스 설정

# <a name="physical-device"></a>[물리적 디바이스](#tab/physical-device)

TPM을 사용 하 여 물리적 Linux 장치를 사용 하는 경우 장치를 설정 하는 추가 단계가 없습니다.

계속할 준비가 되었습니다.

# <a name="virtual-machine"></a>[가상 머신](#tab/virtual-machine)

이 섹션에서는 Hyper-v에서 새 Linux VM을 만듭니다. 자동 프로비저닝이 IoT Edge와 작동 하는 방식을 테스트 하기 위해 시뮬레이트된 TPM으로이 VM을 구성 합니다.

> [!TIP]
> VM을 사용 하는 경우이 문서 전체에서 VM을 여러 번 복사 하 여 붙여 넣습니다. Hyper-v 관리자 연결 응용 프로그램을 통해 복사 및 붙여넣기는 쉽지 않습니다. Hyper-v 관리자를 통해 VM에 연결 하 여 해당 IP 주소를 검색 하는 것이 좋습니다. 먼저를 실행 하 `sudo apt install net-tools` 고를 실행 `hostname -I` 합니다. 그런 다음, IP 주소를 사용하여 SSH를 통해 연결할 수 있습니다(`ssh <username>@<ipaddress>`).

### <a name="create-a-virtual-switch"></a>가상 스위치 만들기

가상 스위치를 사용 하면 VM을 실제 네트워크에 연결할 수 있습니다.

1. Windows 컴퓨터에서 hyper-v 관리자를 엽니다.

1. **작업** 메뉴에서 **가상 스위치 관리자** 를 선택 합니다.

1. **외부** 가상 스위치를 선택 하 고 **가상 스위치 만들기** 를 선택 합니다.

1. 새 가상 스위치에 이름을 지정 합니다. 예를 들어 **EdgeSwitch** 를 사용 합니다. 연결 유형이 **외부 네트워크** 로 설정 되었는지 확인 하 고 **확인** 을 선택 합니다.

1. 네트워크 연결이 중단 될 수 있다는 팝업이 표시 됩니다. 계속하려면 **예** 를 선택합니다.

> [!TIP]
> 새 가상 스위치를 만드는 동안 오류가 표시 되는 경우 다른 스위치에서 이더넷 어댑터를 사용 하 고 있지 않은지와 동일한 이름을 사용 하는 다른 스위치가 없는지 확인 하십시오.

### <a name="create-the-virtual-machine"></a>가상 머신 만들기

부팅 가능 이미지 파일에서 새 VM을 만듭니다.

1. VM에 사용할 디스크 이미지 파일을 다운로드 하 여 로컬로 저장 합니다. 예를 들면 [Ubuntu Server 18.04](http://releases.ubuntu.com/18.04/)입니다. IoT Edge 디바이스에 지원되는 운영 체제에 대한 자세한 내용은 [Azure IoT Edge 지원 시스템](./support.md)을 참조하세요.

1. Hyper-v 관리자의   >    >  **작업** 메뉴에서 작업 새 **가상 컴퓨터** 를 선택 합니다.

1. 다음 특정 구성으로 **새 Virtual Machine 마법사** 를 완료합니다.

   1. **세대 지정**: **2세대** 를 선택합니다. 2 세대 Vm에는 VM에서 IoT Edge를 실행 하는 데 필요한 중첩 된 가상화가 사용 하도록 설정 되어 있습니다.
   1. **네트워킹 구성**: 이전 섹션에서 만든 가상 스위치에 **연결** 값을 설정합니다.
   1. **설치 옵션**: **부팅 가능한 이미지 파일에서 운영 체제 설치** 를 선택하고, 로컬에 저장한 디스크 이미지 파일을 찾습니다.

1. 마법사에서 **마침** 을 선택 하 여 VM을 만듭니다.

새 VM을 만드는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="enable-virtual-tpm"></a>가상 TPM 사용

VM을 만든 후 해당 설정을 열어 장치를 autoprovision 수 있는 가상 TPM을 사용 하도록 설정 합니다.

1. hyper-v 관리자에서 VM을 마우스 오른쪽 단추로 클릭 하 고 **설정** 를 선택 합니다.

1. **보안** 으로 이동 합니다.

1. **보안 부팅 사용** 을 선택 취소 합니다.

1. **신뢰할 수 있는 플랫폼 모듈 사용** 을 선택 합니다.

1. **확인** 을 선택합니다.

### <a name="start-the-virtual-machine"></a>가상 머신 시작합

VM을 시작 하 여 설치 프로세스를 완료 합니다.

1. Hyper-V 관리자에서 VM을 시작하고 연결합니다.

1. VM 내의 프롬프트에 따라 설치 프로세스를 완료 하 고 컴퓨터를 다시 부팅 합니다.

설치가 완료 되 고 VM에 다시 로그인 하면 계속할 준비가 된 것입니다.

---

## <a name="retrieve-provisioning-information-for-your-tpm"></a>TPM에 대 한 프로 비전 정보 검색

이 섹션에서는 TPM에 대 한 등록 ID 및 인증 키를 검색 하는 데 사용할 수 있는 도구를 빌드합니다.

1. 장치에 로그인 하 고 [Linux 개발 환경 설정](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) 의 단계에 따라 C 용 Azure IoT 장치 SDK를 설치 하 고 빌드합니다.

1. TPM에 대 한 장치 프로 비전 정보를 검색 하는 SDK 도구를 빌드하려면 다음 명령을 실행 합니다.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 출력 창에 디바이스의 **등록 ID** 및 **인증 키** 가 표시됩니다. 장치 프로 비전 서비스에서 장치에 대 한 개별 등록을 만들 때 나중에 사용 하기 위해 이러한 값을 복사 합니다.

> [!TIP]
> SDK 도구를 사용 하 여 정보를 검색 하지 않으려는 경우 프로 비전 정보를 얻는 다른 방법을 찾아야 합니다. 각 TPM 칩에 고유한 인증 키는 연결 된 TPM 칩 제조업체에서 가져옵니다. TPM 장치에 대 한 고유한 등록 ID를 파생할 수 있습니다. 예를 들어 인증 키의 SHA-256 해시를 만들 수 있습니다.

등록 ID 및 인증 키를 입력 한 후에는 계속할 준비가 된 것입니다.

## <a name="create-a-device-provisioning-service-enrollment"></a>장치 프로 비전 서비스 등록 만들기

TPM에서 프로 비전 정보를 검색 하 고 해당 정보를 사용 하 여 장치 프로 비전 서비스에 개별 등록을 만듭니다.

장치 프로 비전 서비스에서 등록을 만들 때 **초기 장치 쌍 상태** 를 선언할 수 있습니다. 장치 쌍에서 지역, 환경, 위치 또는 장치 유형 등 솔루션에 사용 된 메트릭으로 장치를 그룹화 하도록 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

> [!TIP]
> 이 문서의 단계는 Azure Portal에 대 한 것 이지만 Azure CLI를 사용 하 여 개별 등록를 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)를 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub device 프로 비전 서비스 인스턴스로 이동 합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. **개별 등록 추가** 를 선택 하 고 다음 단계를 완료 하 여 등록을 구성 합니다.

   1. **메커니즘** 의 경우 **TPM** 을 선택합니다.

   1. VM 또는 물리적 장치에서 복사한 **인증 키** 와 **등록 ID** 를 제공 합니다.

   1. 원하는 경우 장치에 대 한 ID를 제공 합니다. 디바이스 ID를 제공하지 않으면 등록 ID가 사용됩니다.

   1. VM 또는 물리적 장치가 IoT Edge 장치 임을 선언 하려면 **True** 를 선택 합니다.

   1. 장치를 연결 하려는 연결 된 IoT hub를 선택 하거나 **새 IoT Hub에 연결** 을 선택 합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 허브 중 하나에 디바이스가 할당됩니다.

   1. 원하는 경우 **초기 장치 쌍 상태** 에 태그 값을 추가 합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 자세한 내용은 [대규모로 IoT Edge 모듈 배포](how-to-deploy-at-scale.md)를 참조하세요.

   1. **저장** 을 선택합니다.

이제 이 디바이스에 대한 등록이 있으므로 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비저닝할 수 있습니다.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

이 섹션에서는 IoT Edge에 대 한 Linux VM 또는 물리적 장치를 준비 합니다. 그런 다음 IoT Edge를 설치 합니다.

IoT Edge 런타임을 설치할 준비가 되기 전에 장치에서 두 단계를 완료 해야 합니다. 장치에서 Microsoft 설치 패키지에 액세스할 수 있어야 하 고 컨테이너 엔진이 설치 되어 있어야 합니다.

### <a name="access-the-microsoft-installation-packages"></a>Microsoft 설치 패키지 액세스

장치에 Microsoft 설치 패키지에 대 한 액세스 권한이 있어야 합니다.

1. 장치의 운영 체제와 일치 하는 리포지토리 구성을 설치 합니다.

   * **Ubuntu Server 18.04**:

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
      ```

   * **Raspberry Pi OS Stretch**:

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
      ```

1. 생성된 목록을 sources.list.d 디렉터리에 복사합니다.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Microsoft GPG 공개 키를 설치합니다.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trust.gpg.d/
   ```

> [!NOTE]
> Azure IoT Edge 소프트웨어 패키지에는 각 패키지(`usr/share/doc/{package-name}` 또는 `LICENSE` 디렉터리)에 있는 사용 조건이 적용됩니다. 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치하고 사용하면 이러한 사용 조건에 동의하는 것입니다. 사용 조건에 동의 하지 않는 경우 해당 패키지를 사용 하지 마세요.

### <a name="install-a-container-engine"></a>컨테이너 엔진 설치

IoT Edge은 OCI 호환 컨테이너 런타임에 의존 합니다. 프로덕션 시나리오의 경우 Moby 엔진을 사용 하는 것이 좋습니다. Moby 엔진은 IoT Edge에서 공식적으로 지원 되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

1. 디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

1. Moby 엔진을 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > Moby 컨테이너 엔진을 설치할 때 오류가 발생 하는 경우에는 Linux 커널을 Moby 호환성을 확인 하세요. 일부 포함된 디바이스의 제조업체는 컨테이너 엔진 호환성에 필요한 기능이 없는 사용자 지정 Linux 커널을 포함하는 디바이스 이미지를 제공합니다. Moby에서 제공한 [check-config 스크립트](https://github.com/moby/moby/blob/master/contrib/check-config.sh)를 사용하는 다음 명령을 실행하여 커널 구성을 확인합니다.
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > 스크립트의 출력에서 `Generally Necessary` 및 `Network Drivers` 아래의 모든 항목을 사용할 수 있는지 확인합니다. 누락 된 기능을 사용 하는 경우 원본에서 커널을 다시 작성 하 고 적절 한 커널 .config에 포함 하기 위해 관련 모듈을 선택 하 여 사용 하도록 설정 합니다. 마찬가지로 또는와 같은 커널 구성 생성기를 사용 하는 `defconfig` 경우 `menuconfig` 해당 기능을 찾아서 사용 하도록 설정 하 고 그에 따라 커널을 다시 빌드합니다. 새로 수정 된 커널을 배포한 후에는 확인-구성 스크립트를 다시 실행 하 여 필요한 모든 기능이 성공적으로 설정 되었는지 확인 합니다.

### <a name="install-iot-edge"></a>IoT Edge 설치

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge 보안 디먼은 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

이 섹션의 단계는 인터넷에 연결 된 장치에 최신 버전을 설치 하는 일반적인 프로세스를 나타냅니다. 시험판 버전과 같은 특정 버전을 설치 해야 하거나 오프 라인 상태에서 설치 해야 하는 경우 오프 라인 또는 특정 버전의 설치 단계를 따르세요.

1. 디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

1. **Libiothsm** 패키지와 함께 IoT Edge 버전 1.1 *을 설치 합니다.

   ```bash
   sudo apt-get install iotedge
   ```

   > [!NOTE]
   > * IoT Edge 버전 1.1은 IoT Edge의 장기 지원 분기입니다. 이전 버전을 실행 하는 경우 최신 패치를 설치 하거나 업데이트 하는 것이 좋습니다. 이전 버전은 더 이상 지원 되지 않기 때문입니다.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 서비스는 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 서비스는 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

IoT ID 서비스는 IoT Edge 버전 1.2와 함께 도입되었습니다. 이 서비스는 IoT Edge 및 IoT Hub와 통신 해야 하는 기타 장치 구성 요소에 대 한 id 프로 비전 및 관리를 처리 합니다.

이 섹션의 단계는 인터넷에 연결된 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 보여 줍니다. 시험판 버전과 같은 특정 버전을 설치 해야 하거나 오프 라인 상태에서 설치 해야 하는 경우 오프 라인 또는 특정 버전의 설치 단계를 따르세요.

디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

사용할 수 있는 IoT Edge 및 IoT ID 서비스 버전을 확인합니다.

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

최신 버전의 IoT Edge 및 IoT id 서비스 패키지를 설치하려면 다음 명령을 사용합니다.

   ```bash
   sudo apt-get install aziot-edge
   ```

또는 최신 버전이 아닌 다른 IoT Edge 버전을 설치하도록 선택한 경우 `aziot-edge` 및 `aziot-identity-service` 서비스 모두에 동일한 버전을 설치해야 합니다.

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-the-device-with-provisioning-information"></a>프로비저닝 정보를 사용하여 디바이스 구성

장치에 런타임을 설치한 후 장치 프로 비전 서비스에 연결 하 고 IoT Hub 하는 데 사용 하는 정보를 사용 하 여 장치를 구성 합니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. 이전에 수집 된 장치 프로 비전 서비스 **Id 범위** 및 장치 **등록 id** 를 확인 합니다.

1. IoT Edge 장치에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 파일의 프로비저닝 구성 섹션을 찾습니다. TPM 프로비저닝에 대한 줄의 주석 처리를 제거하고, 다른 모든 프로비저닝 줄이 주석 처리되었는지 확인합니다.

   `provisioning:` 줄은 앞에 공백이 없어야 하며, 중첩된 항목은 공백 두 개만큼 들여써야 합니다.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. 및의 값을 `scope_id` `registration_id` 장치 프로 비전 서비스 및 장치 정보로 업데이트 합니다. `scope_id`값은 장치 프로 비전 서비스 인스턴스의 개요 페이지에서 **ID 범위** 입니다.

1. 필요에 따라 `always_reprovision_on_startup` 또는 `dynamic_reprovisioning` 줄을 사용하여 디바이스의 다시 프로비저닝 동작을 구성합니다. 시작 시 장치가 다시 구축로 설정 된 경우 항상 장치 프로 비전 서비스를 사용 하 여 프로 비전을 시도한 후에 실패 하는 경우 프로 비전 백업으로 대체 합니다. 장치를 동적으로 프로 비전 하도록 설정 된 경우에는 IoT Edge가 다시 시작 되 고 다시 프로 비전 이벤트가 감지 되 면 다시 구축 됩니다. 자세한 내용은 [IoT Hub 디바이스 다시 프로비저닝 개념](../iot-dps/concepts-device-reprovision.md)을 참조하세요.

1. 파일을 저장하고 닫습니다.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. 이전에 수집 된 장치 프로 비전 서비스 **Id 범위** 및 장치 **등록 id** 를 확인 합니다.

1. IoT Edge 설치의 일부로 제공 되는 템플릿 파일을 기반으로 장치에 대 한 구성 파일을 만듭니다.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. IoT Edge 디바이스에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. 파일의 프로비저닝 구성 섹션을 찾습니다. TPM 프로비저닝에 대한 줄의 주석 처리를 제거하고, 다른 모든 프로비저닝 줄이 주석 처리되었는지 확인합니다.

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. 및의 값을 `id_scope` `registration_id` 장치 프로 비전 서비스 및 장치 정보로 업데이트 합니다. `scope_id`값은 장치 프로 비전 서비스 인스턴스의 개요 페이지에서 **ID 범위** 입니다.

1. 필요에 따라 파일의 autoreprovisioning mode 섹션을 찾습니다. `auto_reprovisioning_mode` 매개 변수를 사용하여 디바이스의 다시 프로비저닝 동작을 `Dynamic`, `AlwaysOnStartup` 또는 `OnErrorOnly`로 구성합니다. 자세한 내용은 [IoT Hub 디바이스 다시 프로비저닝 개념](../iot-dps/concepts-device-reprovision.md)을 참조하세요.

1. 파일을 저장하고 닫습니다.

:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>TPM에 대한 IoT Edge 액세스 권한 부여

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge 런타임은 디바이스를 자동으로 프로비저닝하려면 TPM에 액세스해야 합니다.

`iotedge` 서비스에 루트 권한이 있도록 systemd 설정을 재정의하여 IoT Edge 런타임에 TPM 액세스 권한을 부여할 수 있습니다. 서비스 권한을 승격시키지 않으려는 경우 다음 단계를 사용하여 수동으로 TPM 액세스 권한을 제공할 수도 있습니다.

1. 및에 대 한 IoT Edge 런타임 액세스를 제공 하는 새 규칙을 만듭니다 `tpm0` `tpmrm0` .

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. 규칙 파일을 엽니다.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. 규칙 파일에 다음과 같은 액세스 정보를 복사합니다. `tpmrm0`4.12 이전의 커널을 사용 하는 장치에는 없을 수 있습니다. 가 없는 장치 `tpmrm0` 는 해당 규칙을 안전 하 게 무시 합니다.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="iotedge", MODE="0600"
   ```

1. 파일을 저장하고 종료합니다.

1. 시스템을 트리거하여 `udev` 새 규칙을 평가 합니다.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. 규칙이 성공적으로 적용됐는지 확인합니다.

   ```bash
   ls -l /dev/tpm*
   ```

   성공한 출력은 다음과 같이 표시됩니다.

   ```output
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpm0
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   올바른 권한이 적용 되어 있지 않으면 컴퓨터를 다시 부팅 하 여 새로 고치십시오 `udev` .

1. 디바이스에서 한 모든 구성 변경을 선택하도록 IoT Edge 런타임을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 런타임은 장치의 TPM에 대 한 액세스를 브로커 하는 TPM 서비스에 의존 합니다. 이 서비스에서 디바이스를 자동으로 프로비저닝하려면 TPM에 액세스해야 합니다.

`aziottpm` 서비스에 루트 권한이 있도록 systemd 설정을 재정의하여 TPM 액세스 권한을 부여할 수 있습니다. 서비스 권한을 승격시키지 않으려는 경우 다음 단계를 사용하여 수동으로 TPM 액세스 권한을 제공할 수도 있습니다.

1. 및에 대 한 IoT Edge 런타임 액세스를 제공 하는 새 규칙을 만듭니다 `tpm0` `tpmrm0` .

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. 규칙 파일을 엽니다.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. 규칙 파일에 다음과 같은 액세스 정보를 복사합니다. `tpmrm0`4.12 이전의 커널을 사용 하는 장치에는 없을 수 있습니다. 가 없는 장치 `tpmrm0` 는 해당 규칙을 안전 하 게 무시 합니다.

   ```input
   # allow aziottpm access to tpm0 and tpmrm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0660"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="aziottpm", MODE="0660"
   ```

1. 파일을 저장하고 종료합니다.

1. 시스템을 트리거하여 `udev` 새 규칙을 평가 합니다.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. 규칙이 성공적으로 적용됐는지 확인합니다.

   ```bash
   ls -l /dev/tpm*
   ```

   성공한 출력은 다음과 같이 표시됩니다.

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   올바른 권한이 적용 되어 있지 않으면 컴퓨터를 다시 부팅 하 여 새로 고치십시오 `udev` .

1. 디바이스에서 변경된 구성을 적용합니다.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>IoT Edge 다시 시작 및 설치 확인

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
아직 수행 하지 않은 경우 IoT Edge 런타임을 다시 시작 하 여 장치에서 수행 된 모든 구성 변경 내용을 선택 합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

IoT Edge 런타임이 실행되고 있는지 확인합니다.

   ```bash
   sudo systemctl status iotedge
   ```

디먼 로그를 검사합니다.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

프로 비전 오류가 표시 되 면 구성 변경 내용이 아직 적용 되지 않은 것일 수 있습니다. IoT Edge 디먼을 다시 시작합니다.

   ```bash
   sudo systemctl daemon-reload
   ```

또는 VM을 다시 시작 하 여 변경 내용이 새로 시작 될 때 적용 되는지 확인 합니다.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
아직 수행 하지 않은 경우 장치에 대 한 구성 변경 내용을 적용 합니다.

   ```bash
   sudo iotedge config apply
   ```

IoT Edge 런타임이 실행되고 있는지 확인합니다.

   ```bash
   sudo iotedge system status
   ```

디먼 로그를 검사합니다.

   ```cmd/sh
   sudo iotedge system logs
   ```

프로 비전 오류가 표시 되 면 구성 변경 내용이 아직 적용 되지 않은 것일 수 있습니다. IoT Edge 디먼을 다시 시작합니다.

   ```bash
   sudo systemctl daemon-reload
   ```

또는 VM을 다시 시작 하 여 변경 내용이 새로 시작 될 때 적용 되는지 확인 합니다.
:::moniker-end
<!-- end 1.2 -->

런타임이 성공적으로 시작 되 면 IoT hub로 이동 하 여 새 장치가 자동으로 프로 비전 되었는지 확인할 수 있습니다. 이제 디바이스가 IoT Edge 모듈을 실행할 준비가 되었습니다.

실행 중인 모듈을 나열합니다.

```cmd/sh
iotedge list
```

장치 프로 비전 서비스에서 만든 개별 등록이 사용 되었는지 확인할 수 있습니다. Azure Portal에서 장치 프로 비전 서비스 인스턴스로 이동 합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

장치 프로 비전 서비스 등록 프로세스를 통해 새 장치를 프로 비전 하는 동시에 장치 ID 및 장치 쌍 태그를 설정할 수 있습니다. 자동 장치 관리를 사용 하 여 이러한 값을 사용 하 여 개별 장치 또는 장치 그룹을 대상으로 지정할 수 있습니다.

Azure Portal 또는 [Azure CLI](how-to-deploy-cli-at-scale.md)를 [사용 하 여 규모에 IoT Edge 모듈을 배포 하 고 모니터링](how-to-deploy-at-scale.md) 하는 방법을 알아봅니다.