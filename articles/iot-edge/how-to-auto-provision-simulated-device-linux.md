---
title: Linux VM에서 가상 TPM을 사용하여 디바이스 프로비저닝 - Azure IoT Edge
description: Linux VM에서 시뮬레이션된 TPM을 사용하여 Azure IoT Edge에 대한 Azure Device Provisioning Service 테스트
author: kgremban
ms.author: kgremban
ms.date: 04/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d667b2429c7911353df98795f7116d47f8f15d8a
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122538954"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Linux에서 TPM을 사용하여 IoT Edge 디바이스 만들기 및 프로비저닝

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

이 문서에서는 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하여 Linux IoT Edge 디바이스에서 자동 프로비저닝을 테스트하는 방법을 보여 줍니다. [Device Provisioning Service](../iot-dps/index.yml)를 사용하여 Azure IoT Edge 디바이스를 자동으로 프로비저닝할 수 있습니다. 자동 프로비저닝 프로세스에 익숙하지 않은 경우 계속하기 전에 [프로비저닝](../iot-dps/about-iot-dps.md#provisioning-process) 개요를 검토하세요.

작업은 다음과 같습니다.

1. 하드웨어 보안을 위해 시뮬레이션된 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하여 Hyper-v에서 Linux 가상 머신(VM)을 만듭니다.
1. IoT Hub DPS(Device Provisioning Service)의 인스턴스를 만듭니다.
1. 디바이스에 대한 개별 등록을 만듭니다.
1. IoT Edge 런타임을 설치하고 IoT Hub에 디바이스를 연결합니다.

> [!TIP]
> 이 문서에서는 TPM 시뮬레이터를 사용하여 DPS 프로비저닝을 테스트하는 방법을 설명하지만, 대부분의 내용이 IoT용 Azure Certified 디바이스인 [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board) 같은 물리적 TPM 하드웨어에 적용됩니다.
>
> 물리적 디바이스를 사용하는 경우 이 문서의 [물리적 디바이스에서 프로비저닝 정보 검색](#retrieve-provisioning-information-from-a-physical-device) 섹션으로 건너뛸 수 있습니다.

## <a name="prerequisites"></a>필수 요건

* [Hyper-V를 사용하도록 설정된](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) Windows 개발 컴퓨터. 이 문서에서는 Ubuntu Server VM을 실행하는 Windows 10을 사용합니다.
* 활성 IoT Hub

> [!NOTE]
> TPM 2.0은 DPS에서 TPM 증명을 사용할 때 필요하며 그룹이 아닌 개별 등록을 만드는 데만 사용할 수 있습니다.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>가상 TPM을 사용하여 Linux 가상 머신 만들기

이 섹션에서는 Hyper-V에서 새 Linux 가상 머신을 만듭니다. IoT Edge에서 자동 프로비저닝의 작동 방식을 테스트하기 위해 시뮬레이션된 TPM으로 이 가상 머신을 구성합니다.

### <a name="create-a-virtual-switch"></a>가상 스위치 만들기

가상 스위치를 사용하면 가상 머신을 실제 네트워크에 연결할 수 있습니다.

1. Windows 머신에서 Hyper-V 관리자를 엽니다.

2. **작업** 메뉴에서 **가상 스위치 관리자** 를 선택합니다.

3. **외부** 가상 스위치를 선택한 다음, **가상 스위치 만들기** 를 선택합니다.

4. 새 가상 스위치에 이름을 지정합니다(예를 들어 **EdgeSwitch**). 연결 형식이 **외부 네트워크** 에 설정되어 있는지 확인한 다음, **확인** 을 선택합니다.

5. 팝업은 네트워크 연결이 중단될 수 있다는 경고 메시지를 표시합니다. 계속하려면 **예** 를 선택합니다.

새 가상 스위치를 만드는 동안 오류가 표시되는 경우 다른 스위치가 이더넷 어댑터를 사용하고 있지 않은지 그리고 동일한 이름을 사용하지 않는지 확인합니다.

### <a name="create-virtual-machine"></a>가상 머신 만들기

1. 가상 머신에 사용하기 위해 디스크 이미지 파일을 다운로드하고 로컬로 저장합니다. 예를 들어 [Ubuntu server 18.04](http://releases.ubuntu.com/18.04/)가 있습니다. IoT Edge 디바이스에 지원되는 운영 체제에 대한 자세한 내용은 [Azure IoT Edge 지원 시스템](support.md)을 참조하세요.

2. 다시 Hyper-V 관리자의 **작업** 메뉴에서 **작업** > **새로 만들기** > **가상 머신** 을 선택합니다.

3. 다음 특정 구성으로 **새 Virtual Machine 마법사** 를 완료합니다.

   1. **세대 지정**: **2세대** 를 선택합니다. 2세대 가상 머신에는 가상 머신에서 IoT Edge를 실행하는 데 필요한 중첩된 가상화를 사용하도록 설정되어 있습니다.
   2. **네트워킹 구성**: 이전 섹션에서 만든 가상 스위치에 **연결** 값을 설정합니다.
   3. **설치 옵션**: **부팅 가능한 이미지 파일에서 운영 체제 설치** 를 선택하고, 로컬에 저장한 디스크 이미지 파일을 찾습니다.

4. 마법사에서 **마침** 을 선택하여 가상 머신을 만듭니다.

새 VM을 만드는 데 몇 분이 걸릴 수 있습니다.

### <a name="enable-virtual-tpm"></a>가상 TPM 사용

VM을 만든 다음에는 설정을 열어 디바이스를 자동 프로비저닝할 수 있도록 하는 가상 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하도록 설정합니다.

1. Hyper-V 관리자에서 VM을 마우스 오른쪽 단추로 클릭하고 **설정** 을 선택합니다.

2. **보안** 으로 이동합니다.

3. **보안 부팅 사용** 을 선택 취소합니다.

4. **신뢰할 수 있는 플랫폼 모듈 사용** 을 확인합니다.

5. **확인** 을 클릭합니다.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>가상 머신 시작 및 TPM 데이터 수집

가상 머신에서 디바이스의 **등록 ID** 및 **인증 키** 를 검색하는 데 사용할 수 있는 도구를 빌드합니다.

1. Hyper-V 관리자에서 VM을 시작하고 연결합니다.

1. 가상 머신 내의 메시지에 따라 설치 프로세스를 완료하고 머신을 다시 부팅합니다.

1. VM에 로그인한 다음, [Set up a Linux development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)(Linux 개발 환경 설정)의 단계에 따라 C용 Azure IoT 디바이스 SDK를 설치하고 빌드합니다.

   >[!TIP]
   >이 문서를 진행하는 동안 가상 머신에서 복사하여 붙여넣습니다. Hyper-V 관리자 연결 애플리케이션을 사용하면 이 작업을 쉽게 수행할 수 없습니다. Hyper-V 관리자를 통해 한 번 가상 머신에 연결하여 IP 주소를 검색할 수 있습니다. 먼저 `sudo apt install net-tools`를 실행한 다음, `hostname -I`를 실행합니다. 그런 다음, IP 주소를 사용하여 SSH를 통해 연결할 수 있습니다(`ssh <username>@<ipaddress>`).

1. 다음 명령을 실행하여 TPM에서 디바이스 프로비저닝 정보를 검색하는 SDK 도구를 빌드합니다.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 출력 창에 디바이스의 **등록 ID** 및 **인증 키** 가 표시됩니다. 이러한 값을 복사하여 나중에 디바이스에 대한 개별 등록을 만들 때 사용할 수 있습니다.

등록 ID와 인증 키가 있으면 [IoT Hub Device Provisioning Service 설정](#set-up-the-iot-hub-device-provisioning-service) 섹션을 계속 진행합니다.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>물리적 디바이스에서 프로비저닝 정보 검색

VM 대신 물리적 IoT Edge 디바이스를 사용하는 경우 디바이스의 프로비저닝 정보를 검색하는 데 사용할 수 있는 도구를 빌드합니다.

1. [Set up a Linux development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)(Linux 개발 환경 설정)의 단계에 따라 C용 Azure IoT 디바이스 SDK를 설치하고 빌드합니다.

1. 다음 명령을 실행하여 TPM 디바이스에서 디바이스 프로비저닝 정보를 검색하는 SDK 도구를 빌드합니다.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. **등록 ID** 및 **인증 키** 의 값을 복사합니다. 이러한 값을 사용하여 DPS에서 디바이스에 대한 개별 등록을 만듭니다.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service 설정

Azure에서 IoT Hub Device Provisioning Service의 새 인스턴스를 만들어 IoT hub에 연결합니다. [IoT Hub DPS 설정](../iot-dps/quick-setup-auto-provision.md) 지침을 따르면 됩니다.

Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

가상 머신에서 프로비전 정보를 검색하고 이를 사용하여 Device Provisioning Service에서 개별 등록을 만듭니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태** 를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

> [!TIP]
> Azure CLI에서 [등록](/cli/azure/iot/dps/enrollment)을 만들고 **edge-enabled** 플래그를 사용하여 디바이스가 IoT Edge 디바이스임을 지정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

2. **설정** 에서 **등록 관리** 를 선택합니다.

3. 등록을 구성하려면 **개별 등록 추가** 를 선택한 다음, 다음 단계를 완료합니다.  

   1. **메커니즘** 의 경우 **TPM** 을 선택합니다.

   2. 가상 머신에서 복사한 **인증 키** 및 **등록 ID** 를 제공합니다.

      > [!TIP]
      > 물리적 TPM 디바이스를 사용하는 경우 **인증 키** 가 각 TPM 칩에서 고유하고 연결된 TPM 칩 제조업체에서 가져왔는지 확인해야 합니다. 예를 들어 인증 키의 SHA-256 해시를 만들어 TPM 디바이스에 고유한 **등록 ID** 를 파생할 수 있습니다.

   3. 원하는 경우 디바이스에 대한 ID를 제공합니다. 디바이스 ID를 제공하지 않으면 등록 ID가 사용됩니다.

   4. 이 가상 머신이 IoT Edge 디바이스임을 선언하려면 **True** 를 선택합니다.

   5. 디바이스를 연결하려는 연결된 IoT Hub를 선택하거나 **Link to new IoT Hub**(새 IoT Hub에 연결)를 선택합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 허브 중 하나에 디바이스가 할당됩니다.

   6. 원하는 경우 **초기 디바이스 쌍 상태** 에 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 자세한 내용은 [대규모로 IoT Edge 모듈 배포](how-to-deploy-at-scale.md)를 참조하세요.

   7. **저장** 을 선택합니다.

이제 이 디바이스에 대한 등록이 있으므로 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비저닝할 수 있습니다.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

IoT Edge 런타임은 모든 IoT Edge 디바이스에 배포되며, 해당 구성 요소는 컨테이너에서 실행되며, Edge에서 코드를 실행할 수 있도록 디바이스에 추가 컨테이너의 배포를 허용합니다. IoT Edge 런타임을 가상 머신에 설치합니다.

[Azure IoT Edge 런타임 설치](how-to-install-iot-edge.md)의 단계를 수행한 다음, 이 문서로 돌아와서 디바이스를 프로비저닝합니다.

## <a name="configure-the-device-with-provisioning-information"></a>프로비저닝 정보를 사용하여 디바이스 구성

런타임이 디바이스에 설치되면 디바이스를 Device Provisioning Service 및 IoT Hub에 연결하는 데 사용되는 정보로 구성합니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. 이전 섹션에서 수집된 DPS **ID 범위** 및 디바이스 **등록 ID** 를 확인합니다.

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

1. `scope_id` 및 `registration_id`의 값을 DPS 및 디바이스 정보로 업데이트합니다.

1. 필요에 따라 `always_reprovision_on_startup` 또는 `dynamic_reprovisioning` 줄을 사용하여 디바이스의 다시 프로비저닝 동작을 구성합니다. 시작 시 디바이스가 다시 프로비저닝되도록 설정되어 있는 경우 항상 먼저 DPS를 사용하여 프로비저닝을 시도한 다음, 실패하는 경우 프로비저닝 백업으로 대체합니다. 디바이스가 동적으로 다시 프로비저닝되도록 설정된 경우 다시 프로비저닝 이벤트가 감지되면 IoT Edge가 다시 시작되고 다시 프로비저닝됩니다. 자세한 내용은 [IoT Hub 디바이스 다시 프로비저닝 개념](../iot-dps/concepts-device-reprovision.md)을 참조하세요.

1. 파일을 저장하고 닫습니다.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. 이전 섹션에서 수집된 DPS **ID 범위** 및 디바이스 **등록 ID** 를 확인합니다.

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

1. `id_scope` 및 `registration_id`의 값을 DPS 및 디바이스 정보로 업데이트합니다.

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

2. 규칙 파일을 엽니다.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

3. 규칙 파일에 다음과 같은 액세스 정보를 복사합니다. 4\.12 이전 커널을 사용하는 디바이스에는 `tpmrm0`이 없을 수 있습니다. tpmrm0이 없는 디바이스는 해당 규칙을 안전하게 무시합니다.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="iotedge", MODE="0600"
   ```

4. 파일을 저장하고 종료합니다.

5. 새 규칙을 평가하려면 udev 시스템을 트리거합니다.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

6. 규칙이 성공적으로 적용됐는지 확인합니다.

   ```bash
   ls -l /dev/tpm*
   ```

   성공한 출력은 다음과 같이 표시됩니다.

   ```output
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpm0
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   올바른 사용 권한이 적용됐는지 확인할 수 없는 경우 머신을 재부팅하여 udev를 새로 고칩니다.
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

2. 규칙 파일을 엽니다.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

3. 규칙 파일에 다음과 같은 액세스 정보를 복사합니다. 4\.12 이전 커널을 사용하는 디바이스에는 `tpmrm0`이 없을 수 있습니다. tpmrm0이 없는 디바이스는 해당 규칙을 안전하게 무시합니다.

   ```input
   # allow aziottpm access to tpm0 and tpmrm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0660"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="aziottpm", MODE="0660"
   ```

4. 파일을 저장하고 종료합니다.

5. 새 규칙을 평가하려면 udev 시스템을 트리거합니다.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

6. 규칙이 성공적으로 적용됐는지 확인합니다.

   ```bash
   ls -l /dev/tpm*
   ```

   성공한 출력은 다음과 같이 표시됩니다.

   ```output
   crw-rw---- 1 aziottpm root 10, 224 Jul 20 16:27 /dev/tpm0
   crw-rw---- 1 aziottpm root 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   올바른 사용 권한이 적용됐는지 확인할 수 없는 경우 머신을 재부팅하여 udev를 새로 고칩니다. 
:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>IoT Edge 다시 시작 및 설치 확인

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
디바이스에서 한 모든 구성 변경을 선택하도록 IoT Edge 런타임을 다시 시작합니다.

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
디바이스에서 변경된 구성을 적용합니다.

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
