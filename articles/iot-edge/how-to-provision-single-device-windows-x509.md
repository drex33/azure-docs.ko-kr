---
title: x.509 인증서를 사용 하 여 Windows에 IoT Edge 장치 만들기 및 프로 비전-Azure IoT Edge | Microsoft Docs
description: x.509 인증서를 사용한 수동 프로 비전을 사용 하 여 IoT Hub에서 단일 Windows IoT Edge 장치 만들기 및 프로 비전
author: v-tcassi
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/30/2021
ms.author: v-tcassi
monikerRange: iotedge-2018-06
ms.openlocfilehash: b9b8a1e3c17ce441dd7469547da83791815dc330
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273754"
---
# <a name="create-and-provision-an-iot-edge-device-on-windows-using-x509-certificates"></a>x.509 인증서를 사용 하 여 Windows에 IoT Edge 장치 만들기 및 프로 비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 Windows IoT Edge 장치를 등록 하 고 프로 비전 하는 종단 간 지침을 제공 합니다.

>[!NOTE]
>Windows 컨테이너를 사용하는 Azure IoT Edge는 Azure IoT Edge 버전 1.2부터 지원되지 않습니다.
>
>Window 디바이스에서 IoT Edge를 실행하는 새로운 방법을 사용하려면 [Azure IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md)를 참조하세요.

IoT 허브에 연결하는 모든 디바이스에는 클라우드-디바이스 또는 디바이스-클라우드 통신을 추적하는 데 사용되는 디바이스 ID가 있습니다. IoT 허브 호스트 이름, 디바이스 ID, 디바이스에서 IoT Hub 인증에 사용하는 정보를 포함하는 연결 정보로 디바이스를 구성합니다.

이 문서의 단계는 단일 디바이스를 IoT 허브에 연결하는 수동 프로비저닝이라는 프로세스를 안내합니다. 수동 프로비저닝을 위해 IoT Edge 디바이스를 인증하는 두 가지 옵션이 있습니다.

* **대칭 키**: IoT Hub에서 새 장치 id를 만들면 서비스에서 두 개의 키를 만듭니다. 디바이스에 키 중 하나를 저장하고 인증 시 IoT Hub 키를 제시합니다.

  이 인증 방식을 사용하면 빠르게 시작할 수 있지만 안전하지 않습니다.

* **X.509 자체 서명**: 두 개의 x.509 ID 인증서를 만들어 디바이스에 저장합니다. IoT Hub에서 새 디바이스 ID를 만들 때 두 인증서의 지문을 모두 제공합니다. 디바이스는 IoT Hub에 인증 시 인증서 하나를 제시되고 IoT Hub는 인증서가 지문과 일치하는지 확인합니다.

  이 인증 방법은 보다 안전 하며 프로덕션 시나리오에 권장 됩니다.

이 문서에서는 인증 방법으로 x.509 인증서를 사용 하는 방법을 설명 합니다. 대칭 키를 사용 하려는 경우 [대칭 키를 사용 하 여 Windows에서 IoT Edge 장치 만들기 및 프로 비전](how-to-provision-single-device-windows-symmetric.md)을 참조 하세요.

> [!NOTE]
> 설정할 디바이스가 많고 각 디바이스를 수동으로 프로비저닝하지 않으려면 다음 문서 중 하나를 읽고 IoT Hub Device Provisioning Service에서 IoT Edge를 사용하는 방법을 알아보세요.
>
> * [X.509 인증서를 사용 하 여 대규모로 IoT Edge 장치 만들기 및 프로 비전](how-to-provision-devices-at-scale-windows-x509.md)
> * [TPM을 사용 하 여 대규모로 IoT Edge 장치 만들기 및 프로 비전](how-to-auto-provision-simulated-device-windows.md)
> * [대칭 키를 사용 하 여 대규모로 IoT Edge 장치 만들기 및 프로 비전](how-to-provision-devices-at-scale-windows-symmetric.md)

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 IoT Edge 장치를 등록 하 고 IoT Edge 설치 하는 방법을 설명 합니다. 이러한 작업을 수행 하는 데 사용 되는 다양 한 필수 조건 및 유틸리티가 있습니다. 계속 하기 전에 모든 필수 구성 요소가 포함 되어 있는지 확인 합니다.

### <a name="device-registration"></a>디바이스 등록

**Azure Portal**, **Visual Studio Code** 또는 **Azure CLI** 를 사용 하 여 장치를 등록 하는 단계를 수행할 수 있습니다. 각 유틸리티에는 고유한 필수 구성 요소가 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 구독의 무료 또는 표준 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Azure 구독의 평가판 또는 표준 [IoT 허브](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code용 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

> [!NOTE]
> 현재 Visual Studio Code용 Azure IoT 확장은 x.509 인증서를 통한 디바이스 등록을 지원하지 않습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Azure 구독의 무료 또는 표준 [IoT Hub](../iot-hub/iot-hub-create-using-cli.md).
* 사용자 환경의 [Azure CLI](/cli/azure/install-azure-cli). Azure CLI 버전이 2.0.70 이상이어야 합니다. `az --version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다.

---

### <a name="iot-edge-installation"></a>IoT Edge 설치

Windows 디바이스

Windows 컨테이너를 사용하는 IoT Edge에는 최신 [Windows 장기 지원 빌드](/windows/release-information/)인 Windows 버전 1809/빌드 17763이 필요합니다. 지원되는 SKU 목록은 [지원되는 시스템 목록](support.md#operating-systems)을 검토해야 합니다.

## <a name="generate-device-identity-certificates"></a>디바이스 ID 인증서 생성

X.509 인증서를 사용하여 수동으로 프로비저닝하려면 IoT Edge 버전 1.0.10 이상이 필요합니다.

X.509 인증서를 사용하여 IoT Edge 디바이스를 프로비저닝하는 경우에는 ‘디바이스 ID 인증서’를 사용합니다. 이 인증서는 IoT Edge 디바이스를 프로비저닝하고 Azure IoT Hub에서 디바이스를 인증하는 데만 사용됩니다. 다른 인증서에 서명하지 않는 리프 인증서입니다. 디바이스 ID 인증서는 IoT Edge 디바이스가 확인을 위해 모듈 또는 다운스트림 디바이스에 제시하는 CA(인증 기관) 인증서와는 별개입니다.

X.509 인증서 인증의 경우 각 디바이스의 인증 정보는 디바이스 ID 인증서에서 가져온 ‘지문’ 형태로 제공됩니다. 디바이스 등록 시 IoT Hub에 이 지문이 제공되어 서비스가 연결될 때 디바이스를 인식할 수 있습니다.

IoT Edge 디바이스에서 CA 인증서를 사용하는 방법에 대한 자세한 내용은 [Azure IoT Edge에서 인증서를 사용하는 방법 이해](iot-edge-certs.md)를 참조하세요.

X.509를 사용한 수동 프로비저닝에는 다음 파일이 필요합니다.

* .cer 또는. pem 형식의 일치하는 프라이빗 키 인증서를 포함하는 두 개의 디바이스 ID 인증서.

  하나의 인증서/키 파일 집합이 IoT Edge 런타임에 제공됩니다. 디바이스 ID 인증서를 만들 때 IoT 허브의 디바이스에 원하는 디바이스 ID로 인증서 일반 이름(CN)을 설정합니다.

* 디바이스 ID 인증서에서 가져온 지문.

  지문 값은 SHA-1 해시의 경우 40(16진수), SHA-256 해시의 경우 64(16진수)입니다. 디바이스 등록 시 IoT Hub에 두 지문이 모두 제공됩니다.

인증서가 없는 경우 [IoT Edge 디바이스 기능을 테스트하기 위한 데모 인증서](how-to-create-test-certificates.md)를 만들 수 있습니다. 이 문서의 안내에 따라 인증서 생성 스크립트를 설정하고 루트 CA 인증서를 만든 다음 두 개의 IoT Edge 디바이스 ID 인증서를 만듭니다.

인증서에서 지문을 검색하는 한 가지 방법은 다음 openssl 명령을 사용하는 것입니다.

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

## <a name="register-your-device"></a>디바이스 등록

사용자의 기본 설정에 따라 **Azure Portal**, **Visual Studio Code** 또는 **Azure CLI** 를 사용 하 여 장치를 등록할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal의 IoT 허브에서 IoT Edge 디바이스는 에지가 사용 설정되지 않은 IoT 디바이스와 별도로 만들어지고 관리됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

1. 왼쪽 창의 메뉴에서 **IoT Edge** 를 선택한 다음 **IoT Edge 디바이스 추가** 를 선택합니다.

   ![Azure Portal에서 IoT Edge 디바이스 추가](./media/how-to-provision-single-device-windows-x509/portal-add-iot-edge-device.png)

1. **디바이스 만들기** 페이지에서 다음 정보를 입력합니다.

   * 설명 디바이스 ID를 만듭니다. 나중에 사용할 수 있도록이 장치 ID를 기록해 둡니다.
   * 인증 유형으로 **X.509 자체 서명** 을 선택합니다.
   * 기본 및 보조 ID 인증서 지문을 제공합니다. 지문 값은 SHA-1 해시의 경우 40(16진수), SHA-256 해시의 경우 64(16진수)입니다.

1. **저장** 을 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

현재 Visual Studio Code용 Azure IoT 확장은 x.509 인증서를 통한 디바이스 등록을 지원하지 않습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) 명령을 사용하여 IoT 허브에서 새 디바이스 ID를 만듭니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity create --device-id [device_id] --hub-name [hub_name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA_thumbprint] --secondary-thumbprint [SHA_thumbprint]
   ```

이 명령에는 다음 매개 변수가 포함됩니다.

* `--device-id` 또는 `-d`: IoT 허브에 고유한 설명 이름을 제공합니다. 다음 섹션에서 사용할 수 있도록 디바이스 ID를 기록해 둡니다.
* `hub-name` 또는 `-n`: IoT 허브의 이름을 제공합니다.
* `--edge-enabled` 또는 `--ee`: 디바이스가 IoT Edge 디바이스임을 선언합니다.
* `--auth-method` 또는 `--am`: 디바이스에서 사용하려는 인증 유형을 선언합니다. 이 경우에는 x.509 인증서 지문을 사용합니다.
* `--primary-thumbprint` 또는 `--ptp`: 기본 키로 사용할 x.509 인증서 지문을 제공합니다.
* `--secondary-thumbprint` 또는 `--stp`: 보조 키로 사용할 x.509 인증서 지문을 제공합니다.

---

이제 IoT Hub에 등록 된 장치가 있으므로 IoT Edge 런타임의 설치 및 프로 비전을 완료 하는 데 사용 하는 정보를 검색 합니다.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>등록 된 장치 보기 및 프로 비전 정보 검색

X.509 인증서 인증을 사용 하는 장치에는 IoT hub 이름, 장치 이름 및 인증서 파일을 사용 하 여 IoT Edge 런타임의 설치 및 프로 비전을 완료 해야 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

IoT Hub에 연결된 에지를 사용할 수 있는 모든 디바이스는 **IoT Edge** 페이지에 나열되어 있습니다.

![Azure Portal에서 IoT 허브 내 모든 IoT Edge 디바이스를 확인할 수 있습니다.](./media/how-to-provision-single-device-windows-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Visual Studio Code를 통해 x.509 인증서를 사용 하는 장치 등록은 지원 되지 않지만 필요한 경우에도 IoT Edge 장치를 볼 수 있습니다.

IoT Hub에 연결하는 모든 디바이스는 Visual Studio Code Explorer의 **Azure IoT Hub** 섹션에 나열됩니다. IoT Edge 디바이스는 아이콘이 다르고 **$edgeAgent** 및 **$edgeHub** 모듈이 각 IoT Edge 디바이스에 배포된다는 점에서 비 Edge 디바이스와 구분됩니다.

![VS Code를 사용하여 IoT 허브 내 모든 IoT Edge 디바이스 보기](./media/how-to-provision-single-device-windows-x509/view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot hub device-identity list](/cli/azure/iot/hub/device-identity) 명령을 사용하여 IoT 허브의 모든 디바이스를 볼 수 있습니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity list --hub-name [hub_name]
   ```

IoT Edge 디바이스로 등록된 모든 디바이스에서는 **capabilities.iotEdge** 속성이 **true** 로 설정됩니다.

---

## <a name="install-iot-edge"></a>IoT Edge 설치

이 섹션에서는 IoT Edge에 대 한 Windows 장치를 준비 합니다. 그런 다음 IoT Edge를 설치 합니다.

Azure IoT Edge는 OCI 호환 컨테이너 런타임을 사용합니다. [Moby의](https://github.com/moby/moby)moby 기반 엔진은 설치 스크립트에 포함 되어 있으며,이는 엔진을 설치 하는 추가 단계가 없음을 의미 합니다.

IoT Edge 런타임을 설치 하려면:

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

## <a name="provision-the-device-with-its-cloud-identity"></a>클라우드 ID를 사용한 디바이스 프로비저닝

이제 컨테이너 엔진과 IoT Edge 런타임이 디바이스에 설치되었으므로 다음 단계를 수행할 수 있습니다. 이 단계에서는 클라우드 ID 및 인증 정보를 사용하여 디바이스를 설정합니다.

1. IoT Edge 디바이스에서 관리자 권한으로 PowerShell을 실행합니다.

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 명령을 사용하여 머신에 IoT Edge 런타임을 구성합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * 오프라인 설치 또는 특정 버전 설치 시에는 IoTEdgeSecurityDaemon.ps1 스크립트를 디바이스에 다운로드한 경우 스크립트의 로컬 복사본을 참조해야 합니다.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 메시지가 표시되면 다음 정보를 제공합니다.

   * **IotHubHostName**: 디바이스가 연결될 IoT 허브의 호스트 이름입니다. 예: `{IoT_hub_name}.azure-devices.net`.
   * **DeviceId**: 디바이스를 등록할 때 제공한 ID입니다.
   * **X509IdentityCertificate**: 디바이스에 있는 ID 인증서의 절대 경로입니다. 예: `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: 제공된 ID 인증서의 프라이빗 키 파일에 대한 절대 경로입니다. 예: `C:\path\identity_key.pem`.

디바이스를 수동으로 프로비저닝하는 경우 추가 매개 변수를 사용하여 다음과 같이 프로세스를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 특정 edgeAgent 컨테이너 이미지를 선언하고, 프라이빗 레지스트리에 있는 경우 자격 증명 제공

추가 매개 변수에 대한 자세한 내용은 [Windows 컨테이너를 포함한 IoT Edge용 PowerShell 스크립트](reference-windows-scripts.md)를 참조하세요.

---

## <a name="offline-or-specific-version-installation-optional"></a>오프라인 설치 또는 특정 버전 설치(선택 사항)

이 섹션의 단계는 표준 설치 단계에서 다루지 않는 시나리오를 위한 단계입니다. 다음을 포함할 수 있습니다.

* 오프라인 상태에서 IoT Edge 설치
* 릴리스 후보 버전 설치
* 최신 버전이 아닌 버전 설치

설치 중에 다음 3개의 파일이 다운로드됩니다.

* 설치 지침을 포함하는 PowerShell 스크립트
* IoT Edge 보안 디먼(iotedged), Moby 컨테이너 엔진, Moby CLI를 포함하는 Microsoft Azure IoT Edge cab
* Visual C++ 재배포 가능 패키지(VC 런타임) 설치 프로그램

설치하는 동안 디바이스가 오프라인 상태가 되거나 특정 버전의 IoT Edge를 설치하려는 경우 디바이스에 미리 해당 파일을 다운로드할 수 있습니다. 설치할 때 다운로드한 파일이 포함된 디렉터리의 설치 스크립트를 가리킵니다. 설치 관리자는 먼저 디렉터리를 확인한 다음, 디렉터리에 없는 구성 요소만 다운로드합니다. 파일을 오프라인에서 모두 사용할 수 있는 경우 인터넷 연결 없이 설치할 수 있습니다.

1. 이전 버전 및 최신 IoT Edge 설치 파일은 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 참조하세요.

2. 설치하려는 버전을 찾고 릴리스 정보의 **자산** 섹션에서 다음 파일을 IoT 디바이스로 다운로드합니다.

   * IoTEdgeSecurityDaemon.ps1
   * 1\.1 릴리스 채널의 Microsoft-Azure-IoTEdge-amd64.cab.

   각 릴리스의 기능을 지원하기 위해 기능이 변경되기 때문에 사용하는 .cab 파일과 동일한 릴리스의 PowerShell 스크립트를 사용하는 것이 중요합니다.

3. 다운로드한 .cab 파일에 아키텍처 접미사가 있는 경우 파일 이름을 **Microsoft-Azure-IoTEdge.cab** 로 바꿉니다.

4. 필요에 따라 Visual C++ 재배포 가능 패키지의 설치 프로그램을 다운로드합니다. 예를 들어 PowerShell 스크립트는 [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe) 버전을 사용합니다. 설치 프로그램을 IoT 디바이스에서 IoT Edge 파일이 있는 곳과 동일한 폴더에 저장합니다.

5. 오프라인 구성 요소를 사용하여 설치하려면 PowerShell 스크립트의 로컬 복사본을 [도트 소싱](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)합니다.

6. `-OfflineInstallationPath` 매개 변수를 사용하여 [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) 명령을 실행합니다. 파일 디렉터리에 대한 절대 경로를 제공합니다. 예제:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   배포 명령은 제공된 로컬 파일 디렉터리에 있는 모든 구성 요소를 사용합니다. .cab 파일 또는 Visual C++ 설치 프로그램이 없으면 배포 명령이 다운로드를 시도합니다.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

디바이스에서 IoT Edge 설치를 제거하려면 다음 명령을 사용합니다.

Windows 디바이스에서 IoT Edge 설치를 제거하려는 경우 관리 PowerShell 창에서 [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) 명령을 사용합니다. 이 명령은 기존 구성 및 Moby 엔진 데이터와 함께 IoT Edge 런타임을 제거합니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

제거 옵션에 대한 자세한 내용은 `Get-Help Uninstall-IoTEdge -full` 명령을 사용하세요.

## <a name="next-steps"></a>다음 단계

디바이스에 모듈을 배포하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md)를 계속 진행하세요.
