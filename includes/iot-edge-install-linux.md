---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e4c78fe2037beb23f69700ae7a340a4d5c6cc160
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490570"
---
## <a name="install-iot-edge"></a>IoT Edge 설치

이 섹션에서는 IoT Edge 위해 Linux VM 또는 물리적 디바이스를 준비합니다. 그런 다음, IoT Edge 설치합니다.

IoT Edge 런타임을 설치할 준비가 되기 전에 디바이스에서 두 단계를 완료해야 합니다. 디바이스는 Microsoft 설치 패키지에 액세스해야 하며 컨테이너 엔진을 설치해야 합니다.

### <a name="access-the-microsoft-installation-packages"></a>Microsoft 설치 패키지 액세스

1. 디바이스의 운영 체제와 일치하는 리포지토리 구성 패키지를 다운로드합니다.

   * **Ubuntu Server 18.04**:

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/packages-microsoft-prod.deb > ./packages-microsoft-prod.deb
      ```

   * **Raspberry Pi OS Stretch**:

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/packages-microsoft-prod.deb > ./packages-microsoft-prod.deb
      ```

1. 구성 패키지를 설치하여 Microsoft의 패키지 리포지토리 및 GPG 공개 키를 추가합니다.

   ```bash
   sudo apt install ./packages-microsoft-prod.deb
   ```

> [!NOTE]
> Azure IoT Edge 소프트웨어 패키지에는 각 패키지(`usr/share/doc/{package-name}` 또는 `LICENSE` 디렉터리)에 있는 사용 조건이 적용됩니다. 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치하고 사용하면 이러한 사용 조건에 동의하는 것입니다. 사용 조건에 동의하지 않는 경우 해당 패키지를 사용하지 마세요.

### <a name="install-a-container-engine"></a>컨테이너 엔진 설치

Azure IoT Edge는 OCI 호환 컨테이너 런타임을 사용합니다. 프로덕션 시나리오의 경우 Moby 엔진을 사용하는 것이 좋습니다. Moby 엔진은 IoT Edge 공식적으로 지원되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

1. 디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

1. Moby 엔진을 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > Moby 컨테이너 엔진을 설치할 때 오류가 발생하면 Moby 호환성을 위해 Linux 커널을 확인합니다. 일부 포함된 디바이스의 제조업체는 컨테이너 엔진 호환성에 필요한 기능이 없는 사용자 지정 Linux 커널을 포함하는 디바이스 이미지를 제공합니다. Moby에서 제공한 [check-config 스크립트](https://github.com/moby/moby/blob/master/contrib/check-config.sh)를 사용하는 다음 명령을 실행하여 커널 구성을 확인합니다.
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > 스크립트의 출력에서 `Generally Necessary` 및 `Network Drivers` 아래의 모든 항목을 사용할 수 있는지 확인합니다. 기능이 누락된 경우 원본에서 커널을 다시 빌드하고 적절한 커널 .config 포함할 관련 모듈을 선택하여 사용하도록 설정합니다. 마찬가지로 또는 와 같은 커널 구성 생성기를 사용하는 경우 `defconfig` `menuconfig` 해당 기능을 찾아서 사용하도록 설정하고 그에 따라 커널을 다시 빌드합니다. 새로 수정된 커널을 배포한 후 check-config 스크립트를 다시 실행하여 필요한 모든 기능이 성공적으로 활성화되었는지 확인합니다.

### <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge 보안 디먼은 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

이 섹션의 단계는 인터넷에 연결된 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 보여 줍니다. 시험판 버전과 같은 특정 버전을 설치해야 하거나 오프라인 상태에서 설치해야 하는 경우 이 문서 뒷부분의 **오프라인 또는 특정 버전 설치** 단계를 따르세요.

디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

**libiothsm-std** 패키지와 함께 IoT Edge 버번 1.1.*을 설치합니다.

   ```bash
   sudo apt-get install iotedge
   ```

>[!NOTE]
>IoT Edge 버전 1.1은 IoT Edge의 장기 지원 분기입니다. 이전 버전을 실행하는 경우 이전 버전이 더 이상 지원되지 않으므로 최신 패치를 설치하거나 업데이트하는 것이 좋습니다.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge 서비스는 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 서비스는 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

IoT ID 서비스는 IoT Edge 버전 1.2와 함께 도입되었습니다. 이 서비스는 IoT Edge 및 IoT Hub와 통신해야 하는 기타 디바이스 구성 요소에 대한 ID 프로비저닝 및 관리를 다룹니다.

이 섹션의 단계는 인터넷에 연결된 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 보여 줍니다. 시험판 버전과 같은 특정 버전을 설치해야 하거나 오프라인 상태에서 설치해야 하는 경우 이 문서 뒷부분의 **오프라인 또는 특정 버전 설치** 단계를 따르세요.

>[!NOTE]
>이 섹션의 단계에서는 IoT Edge 버전 1.2를 설치하는 방법을 보여줍니다.
>
>이전 버전을 실행하는 IoT Edge 디바이스가 이미 있고 1.2로 업그레이드하려는 경우 [IoT Edge 보안 디먼 및 런타임 업데이트](../articles/iot-edge/how-to-update-iot-edge.md)의 단계를 사용합니다. 버전 1.2는 업그레이드에 특정 단계가 필요한 이전 버전의 IoT Edge와 충분히 다릅니다.

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

<!-- end 1.2 -->
::: moniker-end
