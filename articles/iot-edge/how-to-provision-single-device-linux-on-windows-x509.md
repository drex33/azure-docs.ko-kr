---
title: X.509 인증서를 사용하여 Windows 디바이스에서 Linux용 IoT Edge 만들고 프로비전 - Azure IoT Edge | Microsoft Docs
description: X.509 인증서와 함께 수동 프로비저닝을 사용하여 IoT Hub Windows 디바이스에서 Linux용 단일 IoT Edge 만들고 프로비전합니다.
author: v-tcassi
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: v-tcassi
monikerRange: iotedge-2018-06
ms.openlocfilehash: 5ca5560df5f3afe8666b38f457c0371025d2bf23
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273941"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-using-x509-certificates"></a>X.509 인증서를 사용하여 Windows 디바이스에서 Linux용 IoT Edge 만들기 및 프로비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 Windows 디바이스에서 Linux용 IoT Edge 등록 및 프로비전하기 위한 엔드투엔드 지침을 제공합니다.

IoT 허브에 연결하는 모든 디바이스에는 클라우드-디바이스 또는 디바이스-클라우드 통신을 추적하는 데 사용되는 디바이스 ID가 있습니다. IoT 허브 호스트 이름, 디바이스 ID, 디바이스에서 IoT Hub 인증에 사용하는 정보를 포함하는 연결 정보로 디바이스를 구성합니다.

이 문서의 단계는 단일 디바이스를 IoT 허브에 연결하는 수동 프로비저닝이라는 프로세스를 안내합니다. 수동 프로비저닝을 위해 IoT Edge 디바이스를 인증하는 두 가지 옵션이 있습니다.

* **대칭 키:** IoT Hub 새 디바이스 ID를 만들 때 서비스에서 두 개의 키를 만듭니다. 디바이스에 키 중 하나를 저장하고 인증 시 IoT Hub 키를 제시합니다.

  이 인증 방식을 사용하면 빠르게 시작할 수 있지만 안전하지 않습니다.

* **X.509 자체 서명**: 두 개의 x.509 ID 인증서를 만들어 디바이스에 저장합니다. IoT Hub에서 새 디바이스 ID를 만들 때 두 인증서의 지문을 모두 제공합니다. 디바이스는 IoT Hub에 인증 시 인증서 하나를 제시되고 IoT Hub는 인증서가 지문과 일치하는지 확인합니다.

  이 인증 방법은 더 안전하며 프로덕션 시나리오에 권장됩니다.

이 문서에서는 X.509 인증서를 인증 방법으로 사용하는 방법을 다룹니다. 대칭 키를 사용하려면 대칭 [키를 사용하여 Windows 디바이스에서 Linux용 IoT Edge 만들기 및 프로비저닝을 참조하세요.](how-to-provision-single-device-linux-on-windows-symmetric.md)

> [!NOTE]
> 설정할 디바이스가 많고 각 디바이스를 수동으로 프로비저닝하지 않으려면 다음 문서 중 하나를 읽고 IoT Hub Device Provisioning Service에서 IoT Edge를 사용하는 방법을 알아보세요.
>
> * [X.509 인증서를 사용하여 대규모로 IoT Edge 디바이스 만들기 및 프로비전](how-to-provision-devices-at-scale-linux-on-windows-x509.md)
> * [TPM을 사용하여 대규모로 IoT Edge 디바이스 만들기 및 프로비전](how-to-auto-provision-tpm-linux-on-windows.md)
> * [대칭 키를 사용하여 대규모로 IoT Edge 디바이스 만들기 및 프로비전](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 IoT Edge 디바이스 등록 및 Windows Linux용 IoT Edge 설치에 대해 설명합니다. 이러한 작업에는 이러한 작업을 수행하는 데 사용되는 다양한 필수 구성 조건 및 유틸리티가 있습니다. 계속하기 전에 모든 필수 구성요소가 있는지 확인합니다.

### <a name="device-registration"></a>디바이스 등록

**디바이스를** 등록하는 단계에 Azure Portal , **Visual Studio Code** 또는 **Azure CLI** 사용할 수 있습니다. 각 유틸리티에는 고유한 필수 구성 조건이 있습니다.

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

### <a name="iot-edge-for-linux-on-windows-installation"></a>Windows 설치 시 Linux용 IoT Edge

다음과 같은 최소 시스템 요구 사항을 충족하는 Windows 디바이스:

* Windows 10 버전 1809 이상, 빌드 17763 이상

* Professional, Enterprise 또는 Server 버전

* 사용 가능한 최소 메모리: 1GB

* 사용 가능한 최소 디스크 공간: 10GB

* 가상화 지원
  * Windows 10에서 Hyper-V를 사용하도록 설정합니다. 자세한 내용은 [Hyper-V를 Windows 10에 설치](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)를 참조하세요.
  * Windows Server에서 Hyper-V 역할을 설치하고 기본 네트워크 스위치를 만듭니다. 자세한 내용은 [Azure IoT Edge for Linux on Windows의 중첩된 가상화](nested-virtualization.md)를 참조하세요.
  * 가상 머신에서 중첩 가상화를 구성합니다. 자세한 내용은 [중첩된 가상화](nested-virtualization.md)를 참조하세요.

* 네트워킹 지원
  * Windows Server에는 기본 스위치가 없습니다. EFLOW를 Windows Server 디바이스에 배포하려면 먼저 가상 스위치를 만들어야 합니다.  자세한 내용은 [Windows에서 Linux용 가상 스위치 만들기](how-to-create-virtual-switch.md)를 참조하세요.
  * Windows 데스크톱 버전에는 EFLOW 설치에 사용할 수 있는 기본 스위치가 있습니다. 필요한 경우 고유한 사용자 지정 가상 스위치를 만들 수 있습니다.

> [!TIP]
> Windows 배포의 Linux용 Azure IoT Edge에서 **GPU 가속 Linux 모듈** 을 사용하려는 경우 고려해야 할 몇 가지 구성 옵션이 있습니다.
>
> GPU 아키텍처에 따라 올바른 드라이버를 설치해야 하며 Windows 참가자 프로그램 빌드에 액세스해야 할 수도 있습니다. 구성 요구 사항을 확인하고 이러한 필수 조건을 충족하려면 [Windows의 Linux용 Azure IoT Edge에 대한 GPU 가속](gpu-acceleration.md)을 참조하세요.
>
> 지금 GPU 가속을 위한 필수 구성요약을 충족하는 데 시간을 할애해야 합니다. 설치하는 동안 GPU 가속을 원하는 경우 설치 프로세스를 다시 시작해야 합니다.

**PowerShell** 또는 Windows **관리 센터를** 사용하여 IoT Edge 디바이스를 관리할 수 있습니다. 각 유틸리티에는 고유한 필수 구성 조건이 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하려면 다음 단계를 사용하여 Windows Linux용 Azure IoT Edge를 설치하고 Linux 가상 머신을 배포할 대상 디바이스를 준비합니다.

1. 대상 디바이스의 실행 정책을 로 `AllSigned` 설정합니다. 다음 명령을 사용하여 관리자 권한 PowerShell 프롬프트에서 현재 실행 정책을 확인할 수 있습니다.

   ```powershell
   Get-ExecutionPolicy -List
   ```

   `local machine`의 실행 정책이 `AllSigned`가 아닌 경우 다음을 사용하여 실행 정책을 설정할 수 있습니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Windows PowerShell 모듈의 Linux용 Azure IoT Edge에 대한 자세한 내용은 [PowerShell 함수 참조를 참조하세요.](reference-iot-edge-for-linux-on-windows-functions.md)

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows 관리 센터를 사용하려면 다음 단계를 사용하여 Windows 관리 센터를 다운로드 및 설치하고 Windows 관리 센터 Azure IoT Edge 확장을 설치합니다.

   1. [Windows Admin Center 설치 관리자](https://aka.ms/wacdownload)를 다운로드 및 실행합니다. 설치 마법사의 프롬프트에 따라 Windows Admin Center를 설치합니다.

   1. 설치되면 지원되는 브라우저를 사용하여 Windows Admin Center를 엽니다. 지원되는 브라우저에는 Microsoft Edge(Windows 10 버전 1709 이상), Google Chrome 및 Microsoft Edge Insider가 있습니다.

   1. Windows Admin Center를 처음 사용하는 경우 사용할 인증서를 선택하라는 메시지가 표시됩니다. 인증서로 **Windows Admin Center 클라이언트** 를 선택합니다.

   1. Azure IoT Edge 확장을 설치합니다. Windows Admin Center 대시보드의 오른쪽 위에서 기어 아이콘을 선택합니다.

      ![대시보드의 오른쪽 위에 있는 기어 아이콘을 선택하여 PNG 설정에 액세스합니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. **설정** 메뉴의 **게이트웨이** 아래에서 **확장** 을 선택합니다.

   1. **사용 가능한 확장** 탭의 확장 목록에서 **Azure IoT Edge** 를 찾습니다. 이를 선택하고, 확장 목록 위의 **설치** 프롬프트를 선택합니다.

   1. 설치가 완료되면 **설치된 확장** 탭의 설치된 확장 목록에 Azure IoT Edge가 표시됩니다.

---

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

   ![Azure Portal에서 IoT Edge 디바이스 추가](./media/how-to-provision-single-device-linux-on-windows-x509/portal-add-iot-edge-device.png)

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
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
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

![Azure Portal에서 IoT 허브 내 모든 IoT Edge 디바이스를 확인할 수 있습니다.](./media/how-to-provision-single-device-linux-on-windows-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Visual Studio Code를 통해 x.509 인증서를 사용 하는 장치 등록은 지원 되지 않지만 필요한 경우에도 IoT Edge 장치를 볼 수 있습니다.

IoT Hub에 연결하는 모든 디바이스는 Visual Studio Code Explorer의 **Azure IoT Hub** 섹션에 나열됩니다. IoT Edge 디바이스는 아이콘이 다르고 **$edgeAgent** 및 **$edgeHub** 모듈이 각 IoT Edge 디바이스에 배포된다는 점에서 비 Edge 디바이스와 구분됩니다.

![VS Code를 사용하여 IoT 허브 내 모든 IoT Edge 디바이스 보기](./media/how-to-provision-single-device-linux-on-windows-x509/view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot hub device-identity list](/cli/azure/iot/hub/device-identity) 명령을 사용하여 IoT 허브의 모든 디바이스를 볼 수 있습니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge 디바이스로 등록된 모든 디바이스에서는 **capabilities.iotEdge** 속성이 **true** 로 설정됩니다.

---

## <a name="install-iot-edge-for-linux-on-windows"></a>Windows에서 Linux용 IoT Edge 설치

**PowerShell** 또는 **Windows 관리 센터** 를 사용 하 여 Windows에 Linux 용 IoT Edge를 설치할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Windows 대상 장치에 Linux 용 IoT Edge를 설치 합니다.

> [!NOTE]
> 다음 PowerShell 프로세스에서는 Windows의 Linux용 IoT Edge를 로컬 디바이스에 배포하는 방법을 간략하게 설명합니다. PowerShell을 사용하여 원격 대상 디바이스에 배포하려면 [원격 PowerShell](/powershell/module/microsoft.powershell.core/about/about_remote)을 사용하여 원격 디바이스에 대한 연결을 설정하고 해당 디바이스에서 이러한 명령을 원격으로 실행합니다.

1. 관리자 권한 PowerShell 세션에서 다음 명령을 각각 실행하여 IoT Edge for Linux on Windows를 다운로드합니다.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. IoT Edge for Linux on Windows를 디바이스에 설치합니다.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   설치 명령에 `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` 및 `VHDXDIR="<FULLY_QUALIFIED_PATH>"` 매개 변수를 추가하여 Windows의 Linux 설치 및 VHDX 디렉터리용 사용자 지정 IoT Edge를 지정할 수 있습니다.

1. 대상 디바이스의 실행 정책이 아직 설정되지 않은 경우 `AllSigned`로 설정합니다. 현재 실행 정책을 확인 하 고 실행 정책을로 설정 하는 명령은 PowerShell 필수 구성 요소를 참조 하세요 `AllSigned` .

1. IoT Edge for Linux on Windows 배포를 만듭니다. 이 배포는 IoT Edge 런타임이 미리 설치 된 상태로 제공 되는 Linux 가상 머신을 만듭니다.

   ```powershell
   Deploy-Eflow
   ```

   >[!TIP]
   >기본적으로 `Deploy-Eflow` 명령은 RAM 1GB, vCPU 코어 1개, 디스크 공간 16GB를 사용하여 Linux 가상 머신을 만듭니다. 그러나 VM에 필요한 리소스는 배포하는 워크로드에 따라 달라집니다. 워크로드를 지원하기에 충분 한 메모리가 없는 VM은 시작되지 않습니다.
   >
   >`Deploy-Eflow` 명령의 선택적 매개 변수를 사용하여 가상 컴퓨터의 사용 가능한 리소스를 사용자 지정할 수 있습니다.
   >
   >예를 들어, 아래 명령은 vCPU 코어 4개, RAM 4GB, 디스크 공간 20GB의 가상 머신을 만듭니다.
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >사용 가능한 모든 선택적 매개 변수에 대한 자세한 내용은 [Windows의 Linux용 IoT Edge용 PowerShell 함수](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)를 참조하세요.

   배포에 GPU를 할당하여 GPU 가속 Linux 모듈을 사용하도록 설정할 수 있습니다. 이러한 기능에 액세스하려면 [Windows의 Linux용 Azure IoT Edge용 GPU 가속](gpu-acceleration.md)에 설명된 필수 구성 요소를 설치해야 합니다. 배포 프로세스의 이 시점에서 이러한 필수 구성 요소만 설치하는 경우 처음부터 다시 시작해야 합니다.

   GPU 통과를 사용 하려면 **gpuName**, **gpuPassthroughType** 및 **gpuCount** 매개 변수를 명령에 추가 합니다 `Deploy-Eflow` . 사용 가능한 모든 선택적 매개 변수에 대한 자세한 내용은 [Windows의 Linux용 IoT Edge용 PowerShell 함수](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)를 참조하세요.

1. 'Y'를 입력하여 사용 조건에 동의합니다.

1. 기본 설정에 따라 'O' 또는 'R'을 입력하여 **선택적 진단 데이터** 를 설정하거나 해제합니다.

1. 배포가 완료되면 PowerShell 창에 **배포 성공** 이 보고됩니다.

   ![성공적으로 배포 되 면 PNG 메시지의 끝에 ' 배포 성공 ' 이라고 표시 됩니다.](./media/how-to-provision-single-device-linux-on-windows-x509/successful-powershell-deployment.png)

배포가 완료되면 디바이스를 프로비전할 준비가 되었습니다.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Windows Admin Center용 Azure IoT Edge 확장은 현재 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)입니다. 설치 및 관리 프로세스는 일반적으로 사용할 수 있는 기능과 다를 수 있습니다.

장치의 Windows에 Linux 용 Azure IoT Edge를 설치 합니다.

1. [Windows 설치 관리자에서 Linux 용 Azure IoT Edge](https://aka.ms/AzEflowMSI)를 다운로드 합니다.

1. Windows에서 Linux 용 Azure IoT Edge를 설치한 후 Windows 관리 센터를 엽니다.

   Windows Admin Center 시작 페이지의 연결 목록 아래에는 Windows Admin Center를 실행하는 PC를 나타내는 로컬 호스트 연결이 표시됩니다. 관리하는 추가 서버, PC 또는 클러스터도 여기에 표시됩니다.

   Windows Admin Center를 사용하여 Azure IoT Edge for Linux on Windows를 로컬 디바이스 또는 원격 관리 디바이스에 설치하고 관리할 수 있습니다. 이 가이드에서 로컬 호스트 연결은 Azure IoT Edge for Linux on Windows를 배포하기 위한 대상 디바이스 역할을 수행합니다.

1. 아래와 같이 **모든 연결** 아래에 로컬 장치가 나열 되어 있는지 확인 합니다.

   ![대상 장치가 표시 된 초기 Windows 관리 센터 대시보드, PNG.](./media/how-to-provision-single-device-linux-on-windows-x509/windows-admin-center-initial-dashboard.png)

   로컬 장치 대신 원격 대상 장치에 배포 하려는 경우 목록에 원하는 대상 장치가 표시 되지 않으면 지침에 따라 [장치를 추가](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)합니다.

1. **+ 추가** 를 선택 하 여 배포 만들기를 시작 합니다. 이 배포는 Linux 가상 머신을 만들고 IoT Edge 런타임을 설치 합니다.

1. **리소스 추가 또는 만들기** 창에서 **Azure IoT Edge** 타일을 찾습니다. **새로 만들기** 를 선택하여 새 Azure IoT Edge for Linux on Windows 인스턴스를 디바이스에 설치합니다.

   디바이스에서 실행되는 IoT Edge for Linux on Windows가 이미 있는 경우 **추가** 를 선택하여 기존 IoT Edge 디바이스에 연결하고 Windows Admin Center에서 해당 디바이스를 관리할 수 있습니다.

   ![Windows 관리 센터, PNG에서 Azure IoT에 지 타일 타일에서 새로 만들기를 선택 합니다.](./media/how-to-provision-single-device-linux-on-windows-x509/resource-creation-tiles.png)

1. **Azure IoT Edge for Linux on Windows 배포 만들기** 창이 열립니다. **1. 시작하기** 탭에서 최소 요구 사항을 검토하고 **다음** 을 선택합니다.

1. 사용 조건을 검토하고, **동의함** 을 선택하고, **다음** 을 선택합니다.

1. 기본 설정에 따라 **선택적 진단 데이터** 를 설정하거나 해제할 수 있습니다.

1. **다음: 배포** 를 선택합니다.

   ![선택적인 진단 데이터를 기본 설정인 PNG로 전환 하 고 나 서 다음: 배포 단추를 선택 합니다.](./media/how-to-provision-single-device-linux-on-windows-x509/select-next-deploy.png)

1. **2. 배포** 탭의 **대상 디바이스 선택** 아래에서 나열된 디바이스를 클릭하여 최소 요구 사항을 충족하는지 확인합니다. 상태가 '지원됨'으로 확인되면 **다음** 을 선택합니다.

   ![장치를 선택 하 여 지원 되는 PNG를 확인 합니다.](./media/how-to-provision-single-device-linux-on-windows-x509/evaluate-supported-device.png)

1. **2.2 설정** 탭에서 배포의 구성 설정을 검토합니다.

   >[!NOTE]
   >Windows의 Linux용 IoT Edge는 Linux 가상 머신에 내부 IP 주소를 할당하는 기본 스위치를 사용합니다. 이 내부 IP 주소는 Windows 시스템 외부에서 연결할 수 없습니다. Windows 컴퓨터에 로그온한 상태에서 가상 컴퓨터에 로컬로 연결할 수 있습니다.
   >
   >Windows Server를 사용 하는 경우 Windows에서 Linux 용 IoT Edge를 배포 하기 전에 [기본 스위치를 설정](how-to-create-virtual-switch.md) 합니다.

   배포에 GPU를 할당하여 GPU 가속 Linux 모듈을 사용하도록 설정할 수 있습니다. 이러한 기능에 액세스하려면 [Windows의 Linux용 Azure IoT Edge용 GPU 가속](gpu-acceleration.md)에 설명된 필수 구성 요소를 설치해야 합니다. 배포 프로세스의 이 시점에서 이러한 필수 구성 요소만 설치하는 경우 처음부터 다시 시작해야 합니다.

   배포에 할당하는 GPU 어댑터에 따라 사용 가능한 GPU 통과에는 **DDA(직접 디바이스 할당)** 및 **GPU-PV(GPU 반가상화)** 의 두 가지 옵션이 있습니다. 각 방법의 예는 다음과 같습니다.

   직접 디바이스 할당 방법의 경우 Linux 가상 머신에 할당할 GPU 프로세서 수를 선택합니다.

   ![직접 장치 할당 GPU를 사용 하는 구성 설정 (PNG)입니다.](./media/how-to-provision-single-device-linux-on-windows-x509/gpu-passthrough-direct-device-assignment.png)

   반가상화 방법의 경우 추가 설정이 필요하지 않습니다.

   ![준 가상화 GPU를 사용 하는 구성 설정 (PNG)입니다.](./media/how-to-provision-single-device-linux-on-windows-x509/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >하드웨어 디바이스 통과를 사용하도록 설정하면 보안 위험이 증가할 수 있습니다. Microsoft는 해당되는 경우 GPU 공급업체의 디바이스 완화 드라이버를 권장합니다. 자세한 내용은 [개별 디바이스 할당을 사용하여 그래픽 디바이스 배포](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)를 참조하세요.

   설정에 만족하면 **다음** 을 선택합니다.

1. **2.3 배포** 탭에서 배포 상황을 확인할 수 있습니다. 전체 프로세스에는 Azure IoT Edge for Linux on Windows 패키지 다운로드, 패키지 설치, 호스트 디바이스 구성 및 Linux 가상 머신 설정이 포함됩니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다. 성공적인 배포는 다음과 같습니다.

   ![성공적으로 배포 되 면 각 단계에 녹색 확인 표시와 ' 완료 ' 레이블 (PNG)이 표시 됩니다.](./media/how-to-provision-single-device-linux-on-windows-x509/successful-deployment.png)

배포가 완료되면 디바이스를 프로비전할 준비가 되었습니다. **다음: 연결** 을 선택하여 Azure IoT Edge 디바이스 프로비전을 처리하는 **3. 연결** 탭으로 이동합니다.

---

## <a name="configure-the-device-with-provisioning-information"></a>프로비저닝 정보를 사용하여 디바이스 구성

클라우드 id 및 인증 정보를 사용 하 여 장치를 설정할 준비가 되었습니다.

x.509 인증서를 사용 하 여 장치를 프로 비전 하려면 **IoT hub 이름**, **장치 id** 및 Windows 호스트 컴퓨터의 **id 인증서** 및 **개인 키** 에 대 한 절대 경로가 필요 합니다.

Windows Admin Center 또는 관리자 권한 PowerShell 세션을 사용하여 디바이스를 프로비전할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

대상 디바이스에서 디바이스 ID 인증서와 일치하는 프라이빗 키를 준비합니다. 두 파일의 절대 경로를 알고 있습니다.

대상 디바이스의 관리자 권한 PowerShell 세션에서 다음 명령을 실행합니다. 자리 표시자 텍스트를 고유한 값으로 바꿉니다.

```powershell
Provision-EflowVm -provisioningType ManualX509 -iotHubHostname "<HUB HOSTNAME>" -deviceId "<DEVICE ID>" -identityCertPath "<ABSOLUTE PATH TO IDENTITY CERT>" -identityPrivKeyPath "<ABSOLUTE PATH TO PRIVATE KEY>"
```

`Provision-EflowVM` 명령에 대한 자세한 내용은 [Windows의 Linux용 IoT Edge용 PowerShell 함수](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm)를 참조하세요.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **Azure IoT Edge 디바이스 프로비저닝** 창의 프로비저닝 방법 드롭다운에서 **ManualX509** 를 선택합니다.

   ![X.509 인증서를 사용하여 수동으로 프로비저닝 선택](./media/how-to-provision-single-device-linux-on-windows-x509/provisioning-with-selected-method-manual-x509.png)

1. 필수 매개 변수를 제공합니다.

   * **IoT Hub 호스트 이름**: 이 디바이스가 등록된 IoT Hub의 이름입니다.
   * **디바이스 ID**: 이 디바이스가 등록된 이름입니다.
   * **인증서 파일**: 디바이스 ID 인증서를 업로드합니다. 이 인증서는 가상 머신으로 이동되어 디바이스를 프로비저닝하는 데 사용됩니다.
   * **프라이빗 키 파일**: 일치하는 프라이빗 키 파일을 업로드합니다. 이 파일은 가상 머신으로 이동되어 디바이스를 프로비저닝하는 데 사용됩니다.

1. **선택한 방법으로 프로비저닝** 을 선택합니다.

1. 프로비전이 완료되면 **마침** 을 선택합니다. 기본 대시보드로 돌아갑니다. 이제 유형이 `IoT Edge Devices`인 새 디바이스가 표시됩니다. 연결할 IoT Edge 디바이스를 선택할 수 있습니다. **개요** 페이지에서 디바이스의 **IoT Edge 모듈 목록** 및 **IoT Edge 상태** 가 표시됩니다.

---

## <a name="verify-successful-configuration"></a>성공적인 구성 확인

IoT Edge for Linux on Windows가 IoT Edge 디바이스에 성공적으로 설치되고 구성되었는지 확인합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. PowerShell 세션에서 다음 명령을 사용하여 IoT Edge for Linux on Windows 가상 머신에 로그인합니다.

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >SSH를 가상 머신에 허용하는 유일한 계정은 가상 머신을 만든 사용자입니다.

1. 로그인하면 다음 Linux 명령을 사용하여 실행되는 IoT Edge 모듈의 목록을 확인할 수 있습니다.

   ```bash
   sudo iotedge list
   ```

1. IoT Edge 서비스 문제를 해결해야 하는 경우 다음 Linux 명령을 사용합니다.

    1. 서비스 로그를 검색 합니다.

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

   * **IoT Edge 모듈 목록** 섹션에는 디바이스에서 실행되는 모듈이 표시됩니다. IoT Edge 서비스가 처음 시작되면 **edgeAgent** 모듈만 실행되는 것으로 표시됩니다. edgeAgent 모듈은 기본적으로 실행되며, 디바이스에 배포하는 추가 모듈을 설치하고 시작하는 데 도움이 됩니다.

   * **IoT Edge 상태** 섹션에는 서비스 상태가 표시되며 **활성(실행 중)** 이 보고됩니다.

---

새 IoT Edge 디바이스를 만들면 Azure Portal에 상태 코드 `417 -- The device's deployment configuration is not set`이 표시됩니다. 이 상태는 정상이며 디바이스가 모듈 배포를 수신할 준비가 되었음을 의미합니다.

## <a name="next-steps"></a>다음 단계

* 디바이스에 모듈을 배포하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md)를 계속 진행하세요.
* [IoT Edge for Linux on Windows 가상 머신에서 인증서를 관리](how-to-manage-device-certificates.md)하고 파일을 호스트 OS에서 Linux 가상 머신으로 전송하는 방법을 알아봅니다.
* [프록시 서버를 통해 통신하도록 IoT Edge 디바이스를 구성](how-to-configure-proxy-support.md)하는 방법을 알아봅니다.
