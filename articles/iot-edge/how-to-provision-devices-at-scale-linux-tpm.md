---
title: Linux에서 가상 TPM을 통해 디바이스 프로비전 - Azure IoT Edge
description: Linux 디바이스에서 시뮬레이션된 TPM을 사용하여 Azure IoT Edge용 Azure Device Provisioning Service 테스트
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 07/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 81c448b39dcbed56d0208f3e8931eacabd5331f0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663055"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-linux"></a>Linux에서 TPM을 사용하여 대규모로 IoT Edge 디바이스 만들기 및 프로비전

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

이 문서에서는 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하여 Linux IoT Edge 디바이스를 자동으로 프로비전하는 엔드투엔드 지침을 제공합니다. Azure IoT Hub DPS(Device Provisioning Service)를 Azure IoT Edge 디바이스를 자동으로 [프로비전할](../iot-dps/index.yml) 수 있습니다. 자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [프로비전 개요를](../iot-dps/about-iot-dps.md#provisioning-process) 검토하세요.

이 문서에서는 두 가지 방법을 간략하게 설명합니다. 솔루션의 아키텍처에 따라 기본 설정을 선택합니다.

1. 실제 TPM 하드웨어를 사용하여 Linux 디바이스를 자동으로 프로비전합니다. 예를 들어 [Infineon OPTIGA &trade; TPM SLB 9670 입니다.](https://devicecatalog.azure.com/devices/3f52cdee-bbc4-d74e-6c79-a2546f73df4e)
1. Hyper-V를 사용하도록 설정된 Windows 개발 머신에서 실행되는 시뮬레이션된 TPM을 사용하여 Linux VM(가상 머신)을 자동으로 프로비전합니다. 시뮬레이션된 TPM은 실제 TPM과 동일한 보안을 제공하지 않으므로 테스트 시나리오로만 권장됩니다.

지침은 방법론에 따라 다르므로 앞으로 올바른 탭에 있는지 확인합니다.

# <a name="physical-device"></a>[물리적 디바이스](#tab/physical-device)

작업은 다음과 같습니다.

1. TPM에 대한 프로비전 정보를 검색합니다.
1. IoT Hub Device Provisioning Service 인스턴스에서 디바이스에 대한 개별 등록을 만듭니다.
1. IoT Edge 런타임을 설치하고 디바이스를 IoT Hub에 연결합니다.

# <a name="virtual-machine"></a>[가상 머신](#tab/virtual-machine)

작업은 다음과 같습니다.

1. 하드웨어 보안을 위해 시뮬레이션된 TPM을 사용하여 Hyper-V에서 Linux 가상 머신을 만듭니다.
1. TPM에 대한 프로비전 정보를 검색합니다.
1. IoT Hub Device Provisioning Service 인스턴스에서 디바이스에 대한 개별 등록을 만듭니다.
1. IoT Edge 런타임을 설치하고 디바이스를 IoT Hub에 연결합니다.

---

## <a name="prerequisites"></a>사전 요구 사항

# <a name="physical-device"></a>[물리적 디바이스](#tab/physical-device)

* 활성 IoT 허브
* Azure의 IoT Hub Device Provisioning Service의 새 인스턴스로 IoT 허브에 연결되어 있습니다.
  * Device Provisioning Service 인스턴스가 없는 경우 IoT Hub Device Provisioning Service 빠른 시작의 [새 IoT Hub Device Provisioning Service 만들기](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) 및 [IoT Hub 및 Device Provisioning Service 연결](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) 섹션의 지침을 따를 수 있습니다.
  * Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

# <a name="virtual-machine"></a>[가상 머신](#tab/virtual-machine)

* 활성 IoT Hub
* Azure의 IoT Hub Device Provisioning Service의 새 인스턴스로 IoT 허브에 연결되어 있습니다.
  * Device Provisioning Service 인스턴스가 없는 경우 IoT Hub Device Provisioning Service 빠른 시작의 [새 IoT Hub Device Provisioning Service 만들기](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) 및 [IoT Hub 및 Device Provisioning Service 연결](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) 섹션의 지침을 따를 수 있습니다.
  * Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.
* [Hyper-V를 사용하도록 설정된](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) Windows 개발 컴퓨터. 이 문서에서는 Ubuntu Server VM을 실행하는 Windows 10을 사용합니다.

---

> [!NOTE]
> TPM 증명을 DPS와 함께 사용하는 경우 TPM 2.0이 필요합니다.
>
> TPM을 사용하는 경우 그룹화가 아닌 개별 DPS 등록만 만들 수 있습니다.

## <a name="set-up-your-device"></a>디바이스 설정

# <a name="physical-device"></a>[물리적 디바이스](#tab/physical-device)

TPM에서 실제 Linux 디바이스를 사용하는 경우 디바이스를 설정하는 추가 단계가 없습니다.

계속할 준비가 완료되었습니다.

# <a name="virtual-machine"></a>[가상 머신](#tab/virtual-machine)

이 섹션에서는 Hyper-V에서 새 Linux 가상 머신을 만듭니다. IoT Edge에서 자동 프로비저닝의 작동 방식을 테스트하기 위해 시뮬레이션된 TPM으로 이 가상 머신을 구성합니다.

> [!TIP]
> 가상 머신을 사용하는 경우 이 문서 전체에서 가상 머신을 여러 번 복사하여 붙여넣습니다. Hyper-V 관리자 연결 애플리케이션을 통해 복사 및 붙여넣기는 쉽지 않습니다. Hyper-V 관리자를 통해 한 번 가상 머신에 연결하여 IP 주소를 검색할 수 있습니다. 먼저 `sudo apt install net-tools`를 실행한 다음, `hostname -I`를 실행합니다. 그런 다음, IP 주소를 사용하여 SSH를 통해 연결할 수 있습니다(`ssh <username>@<ipaddress>`).

### <a name="create-a-virtual-switch"></a>가상 스위치 만들기

가상 스위치를 사용하면 가상 머신을 실제 네트워크에 연결할 수 있습니다.

1. Windows 컴퓨터에서 Hyper-V 관리자를 엽니다.

1. **작업** 메뉴에서 **가상 스위치 관리자** 를 선택합니다.

1. **외부** 가상 스위치를 선택한 다음, **가상 스위치 만들기** 를 선택합니다.

1. 새 가상 스위치에 이름을 지정합니다. 예를 들어 **EdgeSwitch 입니다.** 연결 형식이 **외부 네트워크** 에 설정되어 있는지 확인한 다음, **확인** 을 선택합니다.

1. 팝업은 네트워크 연결이 중단될 수 있다는 경고 메시지를 표시합니다. 계속하려면 **예** 를 선택합니다.

> [!TIP]
> 새 가상 스위치를 만드는 동안 오류가 표시되는 경우 다른 스위치가 이더넷 어댑터를 사용하지 않고 다른 스위치가 동일한 이름을 사용하지 않는지 확인합니다.

### <a name="create-the-virtual-machine"></a>가상 머신 만들기

부팅 가능한 이미지 파일에서 새 VM을 만듭니다.

1. 가상 머신에 사용하기 위해 디스크 이미지 파일을 다운로드하고 로컬로 저장합니다. 예를 들어 [Ubuntu server 18.04](http://releases.ubuntu.com/18.04/)가 있습니다. IoT Edge 디바이스에 지원되는 운영 체제에 대한 자세한 내용은 [Azure IoT Edge 지원 시스템](/support.md)을 참조하세요.

1. Hyper-V 관리자의 **작업** 메뉴에서 작업  >  **새**  >  **가상 머신을** 선택합니다. 

1. 다음 특정 구성으로 **새 Virtual Machine 마법사** 를 완료합니다.

   1. **세대 지정**: **2세대** 를 선택합니다. 2세대 가상 머신에는 가상 머신에서 IoT Edge를 실행하는 데 필요한 중첩된 가상화를 사용하도록 설정되어 있습니다.
   1. **네트워킹 구성**: 이전 섹션에서 만든 가상 스위치에 **연결** 값을 설정합니다.
   1. **설치 옵션**: **부팅 가능한 이미지 파일에서 운영 체제 설치** 를 선택하고, 로컬에 저장한 디스크 이미지 파일을 찾습니다.

1. 마법사에서 **마침** 을 선택하여 가상 머신을 만듭니다.

새 VM을 만드는 데 몇 분이 걸릴 수 있습니다.

### <a name="enable-virtual-tpm"></a>가상 TPM 사용

VM이 만들어지면 설정을 열어 디바이스를 자동으로 프로비전할 수 있는 가상 TPM을 사용하도록 설정합니다.

1. Hyper-V 관리자에서 VM을 마우스 오른쪽 단추로 클릭하고 **설정** 을 선택합니다.

1. **보안** 으로 이동합니다.

1. **보안 부팅 사용** 을 선택 취소합니다.

1. **신뢰할 수 있는 플랫폼 모듈 사용** 을 확인합니다.

1. **확인** 을 클릭합니다.

### <a name="start-the-virtual-machine"></a>가상 머신 시작합

가상 머신을 시작하여 설치 프로세스를 완료합니다.

1. Hyper-V 관리자에서 VM을 시작하고 연결합니다.

1. 가상 머신 내의 메시지에 따라 설치 프로세스를 완료하고 머신을 다시 부팅합니다.

설치가 완료되고 VM에 다시 로그인되면 계속할 준비가 된 것입니다.

---

## <a name="retrieve-provisioning-information-for-your-tpm"></a>TPM에 대한 프로비전 정보 검색

이 섹션에서는 TPM에 대한 **등록 ID** 및 **인증 키를** 검색하는 데 사용할 수 있는 도구를 빌드합니다.

1. 디바이스에 로그온한 다음 [Linux 개발 환경 설정의](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) 단계에 따라 C용 Azure IoT 디바이스 SDK를 설치하고 빌드합니다.

1. 다음 명령을 실행하여 TPM에 대한 디바이스 프로비저닝 정보를 검색하는 SDK 도구를 빌드합니다.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 출력 창에 디바이스의 **등록 ID** 및 **인증 키** 가 표시됩니다. 나중에 DPS에서 디바이스에 대한 개별 등록을 만들 때 사용할 이러한 값을 복사합니다.

> [!TIP]
> SDK 도구를 사용하여 정보를 검색하지 않으려면 프로비전 정보를 가져오는 다른 방법을 찾아야 합니다. 각 TPM 칩에 고유한 **인증 키는** 연결된 TPM 칩 제조업체에서 얻습니다. 예를 들어 인증 키의 SHA-256 해시를 만들어 TPM 디바이스에 고유한 **등록 ID** 를 파생할 수 있습니다.

등록 ID 및 인증 키가 있으면 계속할 준비가 된 것입니다.

## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

TPM에서 프로비전 정보를 검색하고 이를 사용하여 Device Provisioning Service에서 개별 등록을 만듭니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태** 를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형과 같이 솔루션에서 사용되는 메트릭을 기준으로 디바이스를 그룹화하도록 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

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

이 섹션에서는 IoT Edge 위해 Linux 가상 머신 또는 물리적 디바이스를 준비합니다. 그런 다음, IoT Edge 설치합니다.

IoT Edge 런타임을 설치할 준비가 되기 전에 디바이스에서 완료해야 하는 두 단계가 있습니다. 디바이스는 Microsoft 설치 패키지에 액세스해야 하며 컨테이너 엔진을 설치해야 합니다.

### <a name="access-the-microsoft-installation-packages"></a>Microsoft 설치 패키지 액세스

디바이스에 Microsoft 설치 패키지에 대한 액세스 권한이 있어야 합니다.

1. 디바이스의 운영 체제와 일치하는 리포지토리 구성을 설치합니다.

   * **Ubuntu Sever 18.04:**

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
> Azure IoT Edge 소프트웨어 패키지에는 각 패키지(`usr/share/doc/{package-name}` 또는 `LICENSE` 디렉터리)에 있는 사용 조건이 적용됩니다. 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치하고 사용하면 이러한 사용 조건에 동의하는 것입니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

### <a name="install-a-container-engine"></a>컨테이너 엔진 설치

Azure IoT Edge는 OCI 호환 컨테이너 런타임을 사용합니다. 프로덕션 시나리오의 경우 Moby 엔진을 사용하는 것이 좋습니다. Moby 엔진은 Azure IoT Edge에서 공식적으로 지원되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

1. 디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

1. Moby 엔진을 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > Moby 컨테이너 엔진 설치 시 오류가 발생하면 Linux 커널의 Moby 호환성을 확인합니다. 일부 포함된 디바이스의 제조업체는 컨테이너 엔진 호환성에 필요한 기능이 없는 사용자 지정 Linux 커널을 포함하는 디바이스 이미지를 제공합니다. Moby에서 제공하는 [check-config 스크립트를](https://github.com/moby/moby/blob/master/contrib/check-config.sh) 사용하는 다음 명령을 실행하여 커널 구성을 확인합니다.
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > 스크립트의 출력에서 `Generally Necessary` 및 `Network Drivers` 아래의 모든 항목을 사용할 수 있는지 확인합니다. 기능이 누락됐다면 소스에서 커널을 다시 빌드하고 적절한 커널 .config 내에 포함하기 위한 관련 모듈을 선택하여 활성화합니다. 마찬가지로 `defconfig` 또는 `menuconfig` 같은 커널 구성 생성기를 사용하는 경우 각각의 기능을 찾아 활성화하고 그에 따라 커널을 다시 빌드합니다. 새로 수정된 커널을 배포한 후에는 check-config 스크립트를 다시 실행하여 필요한 모든 기능이 성공적으로 활성화되었는지 확인합니다.

### <a name="install-iot-edge"></a>IoT Edge 설치

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge 보안 디먼은 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

이 섹션의 단계는 인터넷에 연결되어 있는 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 나타냅니다. 사전 릴리스 버전과 같은 특정 버전을 설치해야 하거나 오프라인 상태에서 설치해야 하는 경우 오프라인 또는 특정 버전 설치 단계를 수행합니다.

1. 디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

1. **libiothsm-std** 패키지와 함께 IoT Edge 버전 1.1*을 설치합니다.

   ```bash
   sudo apt-get install iotedge
   ```

   > [!NOTE]
   > *IoT Edge 버전 1.1은 IoT Edge 장기 지원 분기입니다. 이전 버전을 실행하는 경우 이전 버전이 더 이상 지원되지 않는 최신 패치를 설치하거나 최신 패치로 업데이트하는 것이 좋습니다.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 서비스는 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 서비스는 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

IoT ID 서비스는 IoT Edge 버전 1.2와 함께 도입되었습니다. 이 서비스는 IoT Edge 및 IoT Hub와 통신해야 하는 기타 디바이스 구성 요소에 대한 ID 프로비저닝 및 관리를 다룹니다.

이 섹션의 단계는 인터넷에 연결된 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 보여 줍니다. 사전 릴리스 버전과 같은 특정 버전을 설치해야 하거나 오프라인 상태에서 설치해야 하는 경우 오프라인 또는 특정 버전 설치 단계를 수행합니다.

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

런타임이 디바이스에 설치되면 디바이스에서 Device Provisioning Service 및 IoT Hub에 연결하는 데 사용하는 정보로 디바이스를 구성합니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. 이전에 수집된 DPS **ID 범위** 및 디바이스 **등록 ID를** 알고 있습니다.

1. IoT Edge 디바이스에서 구성 파일을 엽니다.

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

1. `scope_id` 및 `registration_id`의 값을 DPS 및 디바이스 정보로 업데이트합니다. `scope_id`는 DPS 인스턴스 개요 페이지의 **ID 범위입니다.**

1. 필요에 따라 `always_reprovision_on_startup` 또는 `dynamic_reprovisioning` 줄을 사용하여 디바이스의 다시 프로비저닝 동작을 구성합니다. 디바이스가 시작 시 다시 프로비전하도록 설정된 경우 항상 먼저 DPS를 사용하여 프로비저닝을 시도한 다음, 해당하는 경우 프로비저닝 백업으로 다시 전환합니다. 디바이스가 자신을 동적으로 프로비전하도록 설정된 경우 다시 프로비전 이벤트가 검색되면 다시 시작 및 다시 프로비전으로 IoT Edge. 자세한 내용은 [IoT Hub 디바이스 다시 프로비저닝 개념](../iot-dps/concepts-device-reprovision.md)을 참조하세요.

1. 파일을 저장하고 닫습니다.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. 이전에 수집된 DPS **ID 범위** 및 디바이스 **등록 ID를** 알고 있습니다.

1. IoT Edge 설치의 일부로 제공되는 템플릿 파일을 기반으로 디바이스에 대한 구성 파일을 만듭니다.

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

1. `id_scope` 및 `registration_id`의 값을 DPS 및 디바이스 정보로 업데이트합니다. `scope_id`는 DPS 인스턴스 개요 페이지의 **ID 범위입니다.**

1. 필요에 따라 파일의 자동 다시 프로비저닝 모드 섹션을 찾습니다. `auto_reprovisioning_mode` 매개 변수를 사용하여 디바이스의 다시 프로비저닝 동작을 `Dynamic`, `AlwaysOnStartup` 또는 `OnErrorOnly`로 구성합니다. 자세한 내용은 [IoT Hub 디바이스 다시 프로비저닝 개념](../iot-dps/concepts-device-reprovision.md)을 참조하세요.

1. 파일을 저장하고 닫습니다.

:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>TPM에 대한 IoT Edge 액세스 권한 부여

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge 런타임은 디바이스를 자동으로 프로비저닝하려면 TPM에 액세스해야 합니다.

`iotedge` 서비스에 루트 권한이 있도록 systemd 설정을 재정의하여 IoT Edge 런타임에 TPM 액세스 권한을 부여할 수 있습니다. 서비스 권한을 승격시키지 않으려는 경우 다음 단계를 사용하여 수동으로 TPM 액세스 권한을 제공할 수도 있습니다.

1. tpm0 및 tpmrm0에 IoT Edge 런타임 액세스 권한을 부여할 새 규칙을 만듭니다.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. 규칙 파일을 엽니다.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. 규칙 파일에 다음과 같은 액세스 정보를 복사합니다. 4\.12 이전 커널을 사용하는 디바이스에는 `tpmrm0`이 없을 수 있습니다. tpmrm0이 없는 디바이스는 해당 규칙을 안전하게 무시합니다.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="iotedge", MODE="0600"
   ```

1. 파일을 저장하고 종료합니다.

1. 새 규칙을 평가하려면 udev 시스템을 트리거합니다.

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

   올바른 사용 권한이 적용됐는지 확인할 수 없는 경우 머신을 재부팅하여 udev를 새로 고칩니다.

1. 디바이스에서 한 모든 구성 변경을 선택하도록 IoT Edge 런타임을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 런타임은 디바이스의 TPM에 대한 액세스 권한을 조정하는 TPM 서비스를 사용합니다. 이 서비스에서 디바이스를 자동으로 프로비저닝하려면 TPM에 액세스해야 합니다.

`aziottpm` 서비스에 루트 권한이 있도록 systemd 설정을 재정의하여 TPM 액세스 권한을 부여할 수 있습니다. 서비스 권한을 승격시키지 않으려는 경우 다음 단계를 사용하여 수동으로 TPM 액세스 권한을 제공할 수도 있습니다.

1. tpm0 및 tpmrm0에 IoT Edge 런타임 액세스 권한을 부여할 새 규칙을 만듭니다.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. 규칙 파일을 엽니다.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. 규칙 파일에 다음과 같은 액세스 정보를 복사합니다. 4\.12 이전 커널을 사용하는 디바이스에는 `tpmrm0`이 없을 수 있습니다. tpmrm0이 없는 디바이스는 해당 규칙을 안전하게 무시합니다.

   ```input
   # allow aziottpm access to tpm0 and tpmrm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0660"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="aziottpm", MODE="0660"
   ```

1. 파일을 저장하고 종료합니다.

1. 새 규칙을 평가하려면 udev 시스템을 트리거합니다.

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

   올바른 사용 권한이 적용됐는지 확인할 수 없는 경우 머신을 재부팅하여 udev를 새로 고칩니다.

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

프로비전 오류가 표시되면 구성 변경 내용이 아직 적용되지 않았을 수 있습니다. IoT Edge 디먼을 다시 시작합니다.

   ```bash
   sudo systemctl daemon-reload
   ```

또는 가상 머신을 다시 시작하여 변경 내용이 새로 시작 시 적용되는지 확인합니다.
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

프로비전 오류가 표시되면 구성 변경 내용이 아직 적용되지 않았을 수 있습니다. IoT Edge 디먼을 다시 시작합니다.

   ```bash
   sudo systemctl daemon-reload
   ```

또는 가상 머신을 다시 시작하여 변경 내용이 새로 시작 시 적용되는지 확인합니다.
:::moniker-end
<!-- end 1.2 -->

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하여 새 디바이스를 자동으로 프로비전했는지 확인할 수 있습니다. 이제 디바이스가 IoT Edge 모듈을 실행할 준비가 되었습니다.

실행 중인 모듈을 나열합니다.

```cmd/sh
iotedge list
```

Device Provisioning Service에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure Portal에서 Device Provisioning Service 인스턴스로 이동합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

DPS 등록 프로세스를 사용하면 새 디바이스를 프로비저닝하는 동시에 디바이스 ID 및 디바이스 쌍 태그를 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-cli-at-scale.md)하거나 [Azure Portal을 사용하여 대규모로 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-at-scale.md)하는 방법을 알아봅니다.
