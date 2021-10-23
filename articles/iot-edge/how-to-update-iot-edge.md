---
title: 디바이스에서 IoT Edge 버전 업데이트 - Azure IoT Edge | Microsoft Docs
description: 최신 버전의 보안 디먼 및 IoT Edge 런타임을 실행하도록 IoT Edge 디바이스를 업데이트하는 방법
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 06/15/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cc123b47ed10252b050743955015b89d434a42b0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218076"
---
# <a name="update-iot-edge"></a>IoT Edge 업데이트

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge 서비스가 새 버전을 릴리스하면 최신 기능 및 개선된 보안을 사용하도록 IoT Edge 디바이스를 업데이트할 수 있습니다. 이 문서에서는 새 버전을 사용할 수 있을 때 IoT Edge 디바이스를 업데이트하는 방법에 대한 정보를 제공합니다.

새 버전으로 전환하려면 IoT Edge 디바이스의 두 가지 구성 요소를 업데이트해야 합니다. 첫 번째는 디바이스에서 실행되고 디바이스가 시작될 때 런타임 모듈을 시작하는 보안 디먼입니다. 현재, 보안 디먼은 디바이스 자체에서만 업데이트할 수 있습니다. 두 번째 구성 요소는 런타임으로, IoT Edge 허브 및 IoT Edge 에이전트 모듈로 구성됩니다. 배포 구성 방법에 따라 디바이스에서 또는 원격으로 런타임을 업데이트할 수 있습니다.

최신 버전의 Azure IoT Edge를 찾으려면 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 참조하세요.

## <a name="update-the-security-daemon"></a>보안 디먼 업데이트

IoT Edge 보안 디먼은 IoT Edge 디바이스에서 패키지 관리자를 사용하여 업데이트해야 하는 네이티브 구성 요소입니다.

`iotedge version` 명령을 사용하여 디바이스에서 실행 중인 보안 디먼의 버전을 확인합니다. Windows에서 Linux용 IoT Edge를 사용하는 경우 버전을 확인하려면 Linux 가상 머신에 SSH로 연결해야 합니다.

# <a name="linux"></a>[Linux](#tab/linux)

>[!IMPORTANT]
>버전 1.0 또는 1.1에서 버전 1.2로 디바이스를 업데이트하는 경우 설치 및 구성 프로세스에 차이점이 있어 추가 단계가 필요합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 단계([특수 사례: 1.0 또는 1.1에서 1.2로 업데이트](#special-case-update-from-10-or-11-to-12))를 참조하세요.

Linux x64 디바이스에서는 apt-get 또는 해당 패키지 관리자를 사용하여 보안 디먼을 최신 버전으로 업데이트합니다.

Microsoft에서 최신 리포지토리 구성을 가져옵니다.

* **Ubuntu Server 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspberry Pi OS Stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

생성된 목록을 복사합니다.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Microsoft GPG 퍼블릭 키를 설치합니다.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

apt를 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

사용할 수 있는 IoT Edge 버전을 확인합니다.

   ```bash
   apt list -a iotedge
   ```

최신 버전의 보안 디먼으로 업데이트하려는 경우 다음 명령(이 명령도 **libiothsm-std** 를 최신 버전으로 업데이트함)을 사용합니다.

   ```bash
   sudo apt-get install iotedge
   ```

특정 버전의 보안 디먼으로 업데이트하려는 경우 apt 목록 출력에서 버전을 지정합니다. **iotedge** 는 업데이트될 때마다 자동으로 **libiothsm-std** 패키지를 최신 버전으로 업데이트하려고 시도하므로 종속성 충돌이 발생할 수 있습니다. 최신 버전으로 전환하지 않는 경우 동일한 버전에 대해 두 패키지를 모두 대상으로 지정해야 합니다. 예를 들어 다음 명령은 특정 버전의 1.1 릴리스를 설치합니다.

   ```bash
   sudo apt-get install iotedge=1.1.1 libiothsm-std=1.1.1
   ```

설치하려는 버전이 apt-get을 통해 제공되지 않는 경우 curl을 사용하여 [IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases) 리포지토리의 어느 버전이든 대상으로 지정할 수 있습니다. 설치하려는 버전이 무엇이든 디바이스에 대해 적절한 **libiothsm-std** 및 **iotedge** 파일을 찾습니다. 파일별로 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다. 링크 주소를 사용하여 해당 구성 요소의 특정 버전을 설치합니다.

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
```
<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

사용할 수 있는 IoT Edge 버전을 확인합니다.

   ```bash
   apt list -a aziot-edge
   ```

최신 버전의 IoT Edge로 업데이트하려는 경우 다음 명령(이 명령도 ID 서비스를 최신 버전으로 업데이트함)을 사용합니다.

   ```bash
   sudo apt-get install aziot-edge
   ```
<!-- end 1.2 -->
:::moniker-end

# <a name="linux-on-windows"></a>[Windows의 Linux](#tab/linuxonwindows)

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>현재 Windows 가상 머신용 Linux에서 실행되는 IoT Edge 버전 1.2는 지원되지 않습니다.

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

>[!IMPORTANT]
>Windows 상의 Linux용 IoT Edge의 공개 미리 보기 버전에서 일반적으로 사용 가능한 버전으로 장치를 업데이트하는 경우에는 Azure IoT Edge를 제거하고 다시 설치해야 합니다.
>
>현재 공개 미리 보기 버전을 사용하고 있는지 확인하려면 Windows 장치에서 **설정** > **Apps** 로 이동합니다. 앱 및 기능 목록에서 **Azure IoT Edge** 를 찾습니다. 표시된 버전이 1.0.x인 경우, 공개 미리 보기 버전을 실행하고 있는 것입니다. 앱을 제거한 후 [Windows 상의 Linux용 IoT Edge를 설치하고 프로 비전](how-to-provision-single-device-linux-on-windows-symmetric.md)합니다. 표시된 버전이 1.1.x 인 경우, 일반적으로 사용 가능한 버전을 실행하고 있는 것이며, Microsoft 업데이트를 통해 업데이트를 받을 수 있습니다.

IoT Edge for Linux on Windows를 사용할 경우 IoT Edge는 Windows 디바이스에 호스트된 Linux 가상 머신에서 실행됩니다. 이 가상 머신은 IoT Edge와 함께 미리 설치되어 있으므로 IoT Edge 구성 요소를 수동으로 업데이트하거나 변경할 수 없습니다. 대신, 가상 머신은 자동으로 해당 구성 요소를 최신 상태로 유지할 수 있도록 Microsoft 업데이트를 통해 관리됩니다.

Windows 상의 Linux용 Azure IoT Edge의 최신 버전을 찾으려면, [EFLOW 릴리스](https://aka.ms/AzEFLOW-Releases)를 참조하세요.


Windows 업데이트에서 Linux용 IoT Edge를 받으려면 다른 Microsoft 제품에 대한 업데이트를 받도록 Windows 호스트를 구성해야 합니다. 다음 단계를 수행하여 이 옵션을 설정할 수 있습니다.

1. Windows 호스트에서 **설정** 을 엽니다.

1. **업데이트 및 보안** 을 선택합니다.

1. **고급 옵션** 을 선택합니다.

1. *Receive updates for other Microsoft products when you update Windows(Windows를 업데이트할 때 다른 Microsoft 제품에 대한 업데이트 받기)* 단추를 **켜기** 로 전환합니다.

:::moniker-end
<!-- end 1.1 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>현재 Windows 디바이스에서 실행되는 IoT Edge 버전 1.2에 대한 지원은 없습니다.

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge for Windows를 사용할 경우 IoT Edge가 Windows 디바이스에서 직접 실행됩니다.

`Update-IoTEdge` 명령을 사용하여 보안 디먼을 업데이트합니다. 이 스크립트는 보안 디먼의 최신 버전을 자동으로 가져옵니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

Update-IoTEdge 명령을 실행하면 두 개의 런타임 컨테이너 이미지와 함께 디바이스에서 보안 디먼이 제거되고 업데이트됩니다. config.yaml 파일뿐 아니라 Moby 컨테이너 엔진의 데이터도 디바이스에서 유지됩니다. 구성 정보를 보존하면 업데이트 프로세스 중에 디바이스의 연결 문자열 또는 디바이스 프로비저닝 서비스 정보를 다시 제공할 필요가 없습니다.

특정 버전의 보안 디먼으로 업데이트하려면 [IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)의 대상으로 지정할 1.1 릴리스 채널의 버전을 찾습니다. 해당 버전에서 **Microsoft-Azure-IoTEdge.cab** 파일을 다운로드합니다. 그런 다음 `-OfflineInstallationPath` 매개 변수를 사용하여 로컬 파일 위치를 가리킵니다. 예를 들면 다음과 같습니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath` 매개 변수는 제공된 디렉터리에서 **Microsoft-Azure-IoTEdge.cab** 이라는 파일을 찾습니다. 파일의 이름을 바꾸면 아키텍처 접미사가 있는 경우 해당 접미사는 제거됩니다.

디바이스를 오프라인으로 업데이트하려면 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)에서 대상으로 지정할 버전을 찾습니다. 해당 버전에서 *IoTEdgeSecurityDaemon.ps1* 및 *Microsoft-Azure-IoTEdge.cab* 파일을 다운로드합니다. 각 릴리스의 기능을 지원하기 위해 기능이 변경되기 때문에 사용하는 .cab 파일과 동일한 릴리스의 PowerShell 스크립트를 사용하는 것이 중요합니다.

다운로드한 .cab 파일에 아키텍처 접미사가 있는 경우 파일 이름을 **Microsoft-Azure-IoTEdge.cab** 로 바꿉니다.

오프라인 구성 요소를 사용하여 업데이트하려면 PowerShell 스크립트의 로컬 복사본을 [도트 소싱](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)합니다. 그런 다음 `-OfflineInstallationPath` 매개 변수를 `Update-IoTEdge` 명령의 일부로 사용하고 파일 디렉터리에 대한 절대 경로를 제공합니다. 예제:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

업데이트 옵션에 대한 자세한 내용은 `Get-Help Update-IoTEdge -full` 명령을 사용하거나 [Windows 컨테이너를 포함하는 IoT Edge용 PowerShell 스크립트](reference-windows-scripts.md)를 참조하세요.

:::moniker-end
<!-- end 1.1 -->

---

## <a name="update-the-runtime-containers"></a>런타임 컨테이너 업데이트

IoT Edge 에이전트 및 IoT Edge 허브 컨테이너를 업데이트하는 방법은 배포에서 롤링 태그(예: 1.1)를 사용할지 또는 특정 태그(예: 1.1.1)를 사용할지에 따라 다릅니다.

`iotedge logs edgeAgent` 또는 `iotedge logs edgeHub` 명령을 사용하여 현재 사용 중인 디바이스에 있는 IoT Edge 에이전트 및 IoT Edge 허브 모듈 버전을 확인합니다. Windows에서 Linux용 IoT Edge를 사용하는 경우 런타임 모듈 버전을 확인하려면 Linux 가상 머신에 SSH로 연결해야 합니다.

  ![로그의 컨테이너 버전 찾기](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge 태그 이해

IoT Edge 에이전트 및 IoT Edge 허브 이미지에는 연결된 IoT Edge 버전으로 태그가 지정됩니다. 런타임 이미지에 태그를 사용하는 방법에는 다음 두 가지가 있습니다.

* **롤링 태그** - 버전 번호의 처음 두 개 값만 사용하여 해당 숫자와 일치하는 최신 이미지를 가져옵니다. 예를 들어, 최신 1.1.x 버전을 가리키는 새 릴리스가 있을 때마다 1.1이 업데이트됩니다. IoT Edge 디바이스의 컨테이너 런타임이 이미지를 다시 끌어오면 런타임 모듈은 최신 버전으로 업데이트됩니다. Azure Portal에서 배포할 때는 기본적으로 롤링 태그가 사용됩니다. *이 접근 방법은 개발 목적으로 제안됩니다.*

* **특정 태그** - 버전 번호의 세 값을 모두 사용하여 이미지 버전을 명시적으로 설정합니다. 예를 들어, 1.1.0은 초기 릴리스 이후에 변경되지 않습니다. 업데이트할 준비가 되면 배포 매니페스트에서 새 버전 번호를 선언할 수 있습니다. *이 접근 방법은 프로덕션 목적으로 제안됩니다.*

### <a name="update-a-rolling-tag-image"></a>롤링 태그 이미지 업데이트

배포에 롤링 태그를 사용하는 경우(예: mcr.microsoft.com/azureiotedge-hub:**1.1**) 디바이스의 컨테이너 런타임이 강제로 최신 버전의 이미지를 가져오도록 해야 합니다.

IoT Edge 디바이스에서 이미지의 로컬 버전을 삭제합니다. Windows 머신에서 보안 디먼을 제거하면 런타임 이미지도 제거되므로 이 단계를 다시 수행할 필요가 없습니다.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.1
docker rmi mcr.microsoft.com/azureiotedge-agent:1.1
```

이미지를 제거하기 위해 강제 `-f` 플래그를 사용해야 할 수 있습니다.

IoT Edge 서비스는 최신 버전의 런타임 이미지를 끌어오고 해당 이미지를 디바이스에서 자동으로 다시 시작합니다.

### <a name="update-a-specific-tag-image"></a>특정 태그 이미지 업데이트

배포에서 특정 태그를 사용하는 경우(예: mcr.microsoft.com/azureiotedge-hub:**1.1.1**) 배포 매니페스트에서 태그를 업데이트하고 변경 내용을 디바이스에 적용하기만 하면 됩니다.

1. Azure Portal의 IoT Hub에서 IoT Edge 디바이스를 선택하고 **모듈 설정** 을 선택합니다.

1. **IoT Edge 모듈** 섹션에서 **런타임 설정** 을 선택합니다.

   ![런타임 설정 구성](./media/how-to-update-iot-edge/configure-runtime.png)

1. **런타임 설정** 에서 **Edge 허브** 의 **이미지** 값을 원하는 버전으로 업데이트합니다. 아직 **저장** 을 선택하지 않습니다.

   ![Edge 허브 이미지 버전 업데이트](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. **Edge 허브** 설정을 축소하거나 아래로 스크롤하고 **Edge 에이전트** 의 **이미지** 값을 원하는 동일 버전으로 업데이트합니다.

   ![Edge 허브 에이전트 버전 업데이트](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. **저장** 을 선택합니다.

1. **검토 + 만들기** 를 선택하고 배포를 검토한 다음 **만들기** 를 선택합니다.

## <a name="special-case-update-from-10-or-11-to-12"></a>특수 사례: 1.0 또는 1.1에서 1.2로 업데이트

>[!NOTE]
>Windows의 Linux용 IoT Edge 또는 Windows 컨테이너를 사용하는 경우 이 특수 사례 섹션이 적용되지 않습니다.

버전 1.2부터 IoT Edge 서비스는 새 패키지 이름을 사용하며 설치 및 구성 프로세스가 약간 달라졌습니다. 1\.0 또는 1.1 버전을 실행하는 IoT Edge 디바이스가 있는 경우 다음 지침을 사용하여 1.2로 업데이트하는 방법을 알아보세요.

>[!NOTE]
>현재 Windows 디바이스에서 실행되는 IoT Edge 버전 1.2에 대한 지원은 없습니다.

1\.2와 이전 버전 간의 주요 차이점은 다음과 같습니다.

* 패키지 이름이 **iotedge** 에서 **aziot-edge** 로 변경되었습니다.
* **libiothsm-std** 패키지가 더 이상 사용되지 않습니다. IoT Edge 릴리스의 일부로 제공된 표준 패키지를 사용한 경우 구성이 새 버전으로 이전될 수 있습니다. libiothsm-std의 다른 구현을 사용한 경우 디바이스 ID 인증서, 디바이스 CA, 신뢰 번들과 같은 사용자 제공 인증서를 다시 구성해야 합니다.
* 새 ID 서비스인 **aziot-identity-service** 가 1.2 릴리스의 일부로 도입되었습니다. 이 서비스는 IoT Edge 및 IoT Hub와 통신해야 하는 기타 디바이스 구성 요소(예: [Device Update for IoT Hub](../iot-hub-device-update/understand-device-update.md))에 대한 ID 프로비저닝 및 관리를 처리합니다.
* 기본 구성 파일이 새 이름과 위치를 갖게 되었습니다. 이전에 `/etc/iotedge/config.yaml`에 있었던 디바이스 구성 정보가 이제는 기본적으로 `/etc/aziot/config.toml`에 있을 것으로 예상됩니다. 구성 정보를 이전 위치와 구문에서 새 위치와 구문으로 이전하는 데 `iotedge config import` 명령을 사용하는 것이 도움이 될 수 있습니다.
  * import 명령은 디바이스의 TPM(신뢰할 수 있는 플랫폼 모듈)에 대한 액세스 규칙을 감지하거나 수정할 수 없습니다. 디바이스에서 TPM 증명을 사용하는 경우 aziottpm 서비스에 대한 액세스 권한을 부여하려면 /etc/udev/rules.d/tpmaccess.rules 파일을 수동으로 업데이트해야 합니다. 자세한 내용은 [TPM에 대한 IoT Edge 액세스 권한 부여](how-to-auto-provision-simulated-device-linux.md?view=iotedge-2020-11&preserve-view=true#give-iot-edge-access-to-the-tpm)를 참조하세요.
* 버전 1.2의 워크로드 API는 암호화된 비밀을 새 형식으로 저장합니다. 이전 버전에서 버전 1.2로 업그레이드하는 경우 기존 마스터 암호화 키를 가져옵니다. 워크로드 API는 가져온 암호화 키를 사용하여 이전 형식으로 저장된 비밀을 읽을 수 있습니다. 그러나 워크로드 API는 암호화된 비밀을 이전 형식으로 쓸 수 없습니다. 비밀이 모듈에서 다시 암호화되면 새 형식으로 저장됩니다. 버전 1.2에서 암호화된 비밀은 버전 1.1의 동일한 모듈에서 읽을 수 없습니다. 암호화된 데이터를 호스트 탑재 폴더 또는 볼륨에 유지하는 경우 업그레이드하기 *전* 에 항상 데이터의 백업 복사본을 만들어 필요한 경우 다운그레이드 기능을 유지합니다.

업데이트 프로세스를 자동화하기 전에 IoT Edge 버전 1.2가 테스트 머신에서 작동하는지 확인합니다.

준비가 되면 다음 단계에 따라 디바이스에서 IoT Edge를 업데이트합니다.

1. Microsoft에서 최신 리포지토리 구성을 가져옵니다.

   * **Ubuntu Server 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS Stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. 생성된 목록을 복사합니다.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Microsoft GPG 퍼블릭 키를 설치합니다.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

4. apt를 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

5. 구성 파일은 그대로 두고 이전 버전의 IoT Edge를 제거합니다.

   ```bash
   sudo apt-get remove iotedge
   ```

6. IoT ID 서비스와 함께 최신 버전의 IoT Edge를 설치합니다.

   ```bash
   sudo apt-get install aziot-edge
   ```

7. 이전 config.xml 파일을 새 형식으로 가져오고 구성 정보를 적용합니다.

   ```bash
   sudo iotedge config import
   ```

이제 디바이스에서 실행되는 IoT Edge 서비스가 업데이트되었으므로 이 문서의 단계에 따라 [런타임 컨테이너도 업데이트](#update-the-runtime-containers)합니다.

## <a name="special-case-update-to-a-release-candidate-version"></a>특수 사례: 릴리스 후보 버전으로 업데이트

>[!NOTE]
>Windows의 Linux용 IoT Edge 또는 Windows 컨테이너를 사용하는 경우 이 특수 사례 섹션이 적용되지 않습니다.

Azure IoT Edge는 IoT Edge 서비스의 새 버전을 정기적으로 릴리스합니다. 매번 안정적인 릴리스가 있기 전에는 하나 이상의 RC(릴리스 후보) 버전이 있습니다. RC 버전에는 릴리스에 대해 계획된 모든 기능이 포함되어 있지만 여전히 테스트 및 유효성 검사가 진행 중입니다. 새 기능을 일찍 테스트하려는 경우 RC 버전을 설치하고 GitHub를 통해 피드백을 제공할 수 있습니다.

릴리스 후보 버전은 동일한 릴리스 번호 매기기 규칙을 따르고 끝에 **-rc** 와 증분 숫자가 추가됩니다. 안정적인 버전과 동일한 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases) 목록에서 릴리스 후보를 확인할 수 있습니다. 예를 들어 **1.2.0** 전의 릴리스 후보 중 하나인 **1.2.0-rc4** 를 찾습니다. 또한 RC 버전에는 **시험판** 레이블 표시가 있는 것을 확인할 수 있습니다.

IoT Edge 에이전트 및 허브 모듈에는 동일한 규칙으로 태그가 지정된 RC 버전이 있습니다. 예를 들어 **mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4** 입니다.

릴리스 후보 버전은 미리 보기로 제공되는 것으로서 일반 설치 관리자가 대상으로 하는 최신 버전에 포함되지 않습니다. 대신 테스트 하려는 RC 버전의 자산을 수동으로 대상으로 지정해야 합니다. 대부분의 경우 RC 버전을 설치하거나 RC 버전으로 업데이트하는 것은 IoT Edge의 다른 특정 버전을 대상으로 지정하는 것과 동일합니다.

이 문서의 섹션을 활용하여 IoT Edge 디바이스를 특정 버전의 보안 디먼 또는 런타임 모듈로 업데이트하는 방법을 알아보세요.

기존 설치를 업그레이드하는 대신 IoT Edge를 설치하는 경우 [오프라인 또는 특정 버전 설치](how-to-provision-single-device-linux-symmetric.md#offline-or-specific-version-installation-optional)의 단계를 사용합니다.

## <a name="next-steps"></a>다음 단계

최신 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 확인합니다.

[IoT(사물 인터넷) 블로그](https://azure.microsoft.com/blog/topics/internet-of-things/)에서 최신 업데이트 및 알림을 받아 보세요.
