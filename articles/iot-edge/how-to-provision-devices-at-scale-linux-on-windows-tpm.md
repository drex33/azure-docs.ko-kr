---
title: TPM Azure IoT Edge를 사용 하 여 Windows 장치에서 Linux 용 IoT Edge 프로 비전 Microsoft Docs
description: Windows 장치의 Linux에서 시뮬레이션 된 TPM을 사용 하 여 Azure IoT에 지에 대 한 Azure device 프로 비전 서비스 테스트
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 07/08/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d6943fa78960760a665f0abad51f0839da9d63bb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663040"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-at-scale-using-a-tpm"></a>TPM을 사용 하 여 확장 Windows 장치에서 Linux 용 IoT Edge 만들기 및 프로 비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 TPM (신뢰할 수 있는 플랫폼 모듈)을 사용 하 여 Windows 장치에서 Linux 용 IoT Edge를 자동 프로 비전 하는 종단 간 지침을 제공 합니다. [Azure IoT 허브 장치 프로 비전 서비스](../iot-dps/index.yml) (DPS)를 사용 하 여 Azure IoT에 지 장치를 자동으로 프로 비전 할 수 있습니다. 자동 프로 비전 프로세스에 익숙하지 않은 경우 계속 하기 전에 [프로 비전 개요](../iot-dps/about-iot-dps.md#provisioning-process) 를 검토 합니다.

이 문서에서는 두 가지 방법을 설명 합니다. 솔루션의 아키텍처에 따라 기본 설정을 선택 합니다.

1. 실제 TPM 하드웨어를 사용 하 여 Windows 장치에서 Linux를 자동 프로 비전 합니다.
1. 시뮬레이션 된 TPM을 사용 하 여 Windows 장치에서 Linux를 자동 프로 비전 합니다. 이 방법은 시뮬레이션 된 TPM이 물리적 TPM과 동일한 보안을 제공 하지 않기 때문에 테스트 시나리오로만 권장 됩니다.

작업은 다음과 같습니다.

# <a name="physical-tpm"></a>[물리적 TPM](#tab/physical-tpm)

* IoT Edge for Linux on Windows를 설치합니다.
* 디바이스에서 TPM 정보를 검색합니다.
* 디바이스에 대한 개별 등록을 만듭니다.
* TPM 정보를 사용하여 디바이스를 프로비저닝합니다.

# <a name="simulated-tpm"></a>[시뮬레이션 된 TPM](#tab/simulated-tpm)

* IoT Edge for Linux on Windows를 설치합니다.
* 시뮬레이션 된 TPM을 설정 하 고 프로 비전 정보를 검색 합니다.
* 디바이스에 대한 개별 등록을 만듭니다.
* TPM 정보를 사용하여 디바이스를 프로비저닝합니다.

---

## <a name="prerequisites"></a>필수 구성 요소

클라우드 리소스:

* 활성 IoT Hub
* Azure의 IoT Hub Device Provisioning Service의 새 인스턴스로 IoT 허브에 연결되어 있습니다.
  * 장치 프로 비전 서비스 인스턴스가 없는 경우 [새 IoT Hub Device Provisioning Service 만들기](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) 의 지침에 따라 IoT Hub Device Provisioning Service 빠른 시작의 [IoT Hub 및 장치 프로 비전 서비스를 연결할](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) 수 있습니다.
  * Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

개발 머신:

* 다음과 같은 최소 시스템 요구 사항을 충족하는 Windows 디바이스:
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
   > Windows 배포의 Linux용 Azure IoT Edge에서 **GPU 가속 Linux 모듈** 을 사용하려는 경우 고려해야 할 몇 가지 구성 옵션이 있습니다. GPU 아키텍처에 따라 올바른 드라이버를 설치해야 하며 Windows 참가자 프로그램 빌드에 액세스해야 할 수도 있습니다. 구성 요구 사항을 확인하고 이러한 필수 조건을 충족하려면 [Windows의 Linux용 Azure IoT Edge에 대한 GPU 가속](gpu-acceleration.md)을 참조하세요.

**PowerShell** 또는 **Windows 관리 센터** 를 사용 하 여 IoT Edge 장치를 관리할 수 있습니다. 각 유틸리티에는 고유한 필수 구성 요소가 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하려는 경우 다음 단계를 사용 하 여 linux 가상 컴퓨터의 배포 및 Windows에 linux 용 Azure IoT Edge 설치를 위한 대상 장치를 준비 합니다.

1. 대상 장치에 대 한 실행 정책을로 설정 `AllSigned` 합니다. 다음 명령을 사용 하 여 관리자 권한 PowerShell 프롬프트에서 현재 실행 정책을 확인할 수 있습니다.

   ```powershell
   Get-ExecutionPolicy -List
   ```

   `local machine`의 실행 정책이 `AllSigned`가 아닌 경우 다음을 사용하여 실행 정책을 설정할 수 있습니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Windows PowerShell 모듈의 Linux에 대 한 Azure IoT Edge에 대 한 자세한 내용은 [PowerShell 함수 참조](reference-iot-edge-for-linux-on-windows-functions.md)를 참조 하세요.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows 관리 센터를 사용 하려는 경우 다음 단계를 사용 하 여 Windows 관리 센터를 다운로드 및 설치 하 고 Windows 관리 센터 Azure IoT Edge 확장을 설치 합니다.

   1. [Windows Admin Center 설치 관리자](https://aka.ms/wacdownload)를 다운로드 및 실행합니다. 설치 마법사의 프롬프트에 따라 Windows Admin Center를 설치합니다.

   1. 설치되면 지원되는 브라우저를 사용하여 Windows Admin Center를 엽니다. 지원되는 브라우저에는 Microsoft Edge(Windows 10 버전 1709 이상), Google Chrome 및 Microsoft Edge Insider가 있습니다.

   1. Windows Admin Center를 처음 사용하는 경우 사용할 인증서를 선택하라는 메시지가 표시됩니다. 인증서로 **Windows Admin Center 클라이언트** 를 선택합니다.

   1. Azure IoT Edge 확장을 설치합니다. Windows Admin Center 대시보드의 오른쪽 위에서 기어 아이콘을 선택합니다.

      ![대시보드의 오른쪽 위에 있는 기어 아이콘을 선택 하 여 PNG 설정에 액세스 합니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-gear-icon.png)

   1. **설정** 메뉴의 **게이트웨이** 아래에서 **확장** 을 선택합니다.

   1. **사용 가능한 확장** 탭의 확장 목록에서 **Azure IoT Edge** 를 찾습니다. 이를 선택하고, 확장 목록 위의 **설치** 프롬프트를 선택합니다.

   1. 설치가 완료되면 **설치된 확장** 탭의 설치된 확장 목록에 Azure IoT Edge가 표시됩니다.

---

> [!NOTE]
> Tpm 2.0은 DPS에서 TPM 증명을 사용 하는 경우에 필요 합니다.
>
> TPM을 사용 하는 경우에만 개별 그룹, DPS 등록 만들 수 있습니다.

## <a name="install-iot-edge"></a>IoT Edge 설치

대상 디바이스에 Windows의 Linux용 Azure IoT Edge를 배포합니다.

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

1. IoT Edge for Linux on Windows 배포를 만듭니다. 이 배포는 Linux 가상 머신을 만들고 IoT Edge 런타임을 설치 합니다.

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

   배포에 GPU를 할당하여 GPU 가속 Linux 모듈을 사용하도록 설정할 수 있습니다. 이러한 기능에 액세스하려면 [Windows의 Linux용 Azure IoT Edge용 GPU 가속](gpu-acceleration.md)에 설명된 필수 구성 요소를 설치해야 합니다.

   GPU 통과를 사용 하려면 **gpuName**, **gpuPassthroughType** 및 **gpuCount** 매개 변수를 명령에 추가 합니다 `Deploy-Eflow` . 사용 가능한 모든 선택적 매개 변수에 대한 자세한 내용은 [Windows의 Linux용 IoT Edge용 PowerShell 함수](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)를 참조하세요.

   >[!WARNING]
   >하드웨어 디바이스 통과를 사용하도록 설정하면 보안 위험이 증가할 수 있습니다. Microsoft는 해당되는 경우 GPU 공급업체의 디바이스 완화 드라이버를 권장합니다. 자세한 내용은 [개별 디바이스 할당을 사용하여 그래픽 디바이스 배포](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)를 참조하세요.

1. 'Y'를 입력하여 사용 조건에 동의합니다.

1. 기본 설정에 따라 'O' 또는 'R'을 입력하여 **선택적 진단 데이터** 를 설정하거나 해제합니다.

1. 배포가 완료되면 PowerShell 창에 **배포 성공** 이 보고됩니다.

   ![성공적으로 배포 되 면 PNG 메시지의 끝에 ' 배포 성공 ' 이라고 표시 됩니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-powershell-deployment.png)

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

   ![대상 장치가 표시 된 초기 Windows 관리 센터 대시보드, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/windows-admin-center-initial-dashboard.png)

   로컬 장치 대신 원격 대상 장치에 배포 하려는 경우 목록에 원하는 대상 장치가 표시 되지 않으면 지침에 따라 [장치를 추가](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)합니다.

1. **+ 추가** 를 선택 하 여 배포 만들기를 시작 합니다. 이 배포는 Linux 가상 머신을 만들고 IoT Edge 런타임을 설치 합니다.

1. **리소스 추가 또는 만들기** 창에서 **Azure IoT Edge** 타일을 찾습니다. **새로 만들기** 를 선택하여 새 Azure IoT Edge for Linux on Windows 인스턴스를 디바이스에 설치합니다.

   디바이스에서 실행되는 IoT Edge for Linux on Windows가 이미 있는 경우 **추가** 를 선택하여 기존 IoT Edge 디바이스에 연결하고 Windows Admin Center에서 해당 디바이스를 관리할 수 있습니다.

   ![Windows 관리 센터, PNG에서 Azure IoT에 지 타일 타일에서 새로 만들기를 선택 합니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/resource-creation-tiles.png)

1. **Azure IoT Edge for Linux on Windows 배포 만들기** 창이 열립니다. **1. 시작하기** 탭에서 최소 요구 사항을 검토하고 **다음** 을 선택합니다.

1. 사용 조건을 검토하고, **동의함** 을 선택하고, **다음** 을 선택합니다.

1. 기본 설정에 따라 **선택적 진단 데이터** 를 설정하거나 해제할 수 있습니다.

1. **다음: 배포** 를 선택합니다.

   ![선택적인 진단 데이터를 기본 설정인 PNG로 전환 하 고 나 서 다음: 배포 단추를 선택 합니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-next-deploy.png)

1. **2. 배포** 탭의 **대상 디바이스 선택** 아래에서 나열된 디바이스를 클릭하여 최소 요구 사항을 충족하는지 확인합니다. 상태가 '지원됨'으로 확인되면 **다음** 을 선택합니다.

   ![장치를 선택 하 여 지원 되는 PNG를 확인 합니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/evaluate-supported-device.png)

1. **2.2 설정** 탭에서 배포의 구성 설정을 검토합니다.

   >[!NOTE]
   >Windows의 Linux용 IoT Edge는 Linux 가상 머신에 내부 IP 주소를 할당하는 기본 스위치를 사용합니다. 이 내부 IP 주소는 Windows 시스템 외부에서 연결할 수 없습니다. Windows 컴퓨터에 로그온한 상태에서 가상 컴퓨터에 로컬로 연결할 수 있습니다.
   >
   >Windows Server를 사용 하는 경우 Windows에서 Linux 용 IoT Edge를 배포 하기 전에 [기본 스위치를 설정](how-to-create-virtual-switch.md) 합니다.

   배포에 GPU를 할당하여 GPU 가속 Linux 모듈을 사용하도록 설정할 수 있습니다. 이러한 기능에 액세스하려면 [Windows의 Linux용 Azure IoT Edge용 GPU 가속](gpu-acceleration.md)에 설명된 필수 구성 요소를 설치해야 합니다. 배포 프로세스의 이 시점에서 이러한 필수 구성 요소만 설치하는 경우 처음부터 다시 시작해야 합니다.

   배포에 할당하는 GPU 어댑터에 따라 사용 가능한 GPU 통과에는 **DDA(직접 디바이스 할당)** 및 **GPU-PV(GPU 반가상화)** 의 두 가지 옵션이 있습니다. 각 방법의 예는 다음과 같습니다.

   직접 디바이스 할당 방법의 경우 Linux 가상 머신에 할당할 GPU 프로세서 수를 선택합니다.

   ![직접 장치 할당 GPU를 사용 하는 구성 설정 (PNG)입니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-direct-device-assignment.png)

   반가상화 방법의 경우 추가 설정이 필요하지 않습니다.

   ![준 가상화 GPU를 사용 하는 구성 설정 (PNG)입니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >하드웨어 디바이스 통과를 사용하도록 설정하면 보안 위험이 증가할 수 있습니다. Microsoft는 해당되는 경우 GPU 공급업체의 디바이스 완화 드라이버를 권장합니다. 자세한 내용은 [개별 디바이스 할당을 사용하여 그래픽 디바이스 배포](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)를 참조하세요.

   설정에 만족하면 **다음** 을 선택합니다.

1. **2.3 배포** 탭에서 배포 상황을 확인할 수 있습니다. 전체 프로세스에는 Azure IoT Edge for Linux on Windows 패키지 다운로드, 패키지 설치, 호스트 디바이스 구성 및 Linux 가상 머신 설정이 포함됩니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다. 성공적인 배포는 다음과 같습니다.

   ![성공적으로 배포 되 면 각 단계에 녹색 확인 표시와 ' 완료 ' 레이블 (PNG)이 표시 됩니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-deployment.png)

1. 배포가 완료되면 디바이스를 프로비전할 준비가 되었습니다. **다음: 연결** 을 선택하여 Azure IoT Edge 디바이스 프로비전을 처리하는 **3. 연결** 탭으로 이동합니다.

계속하기 전에 TPM을 사용하여 디바이스 프로비저닝을 준비하기 위해 수행해야 하는 몇 가지 단계가 있으므로 **프로비저닝** 페이지에서 배포 마법사의 다음 단계를 중지합니다. Windows Admin Center 창을 계속 열어 둡니다. 이 문서를 마친 후 프로비저닝 단계를 완료하기 위해 다시 이 창으로 돌아가야 합니다.

---

## <a name="enable-tpm-passthrough"></a>TPM 통과 사용

IoT Edge for Linux on Windows 가상 머신에는 사용하도록 또는 사용하지 않도록 설정할 수 있는 TPM 기능이 있습니다. 이 기능은 기본적으로 사용하지 않도록 설정됩니다. 이 기능을 사용하도록 설정하면 가상 머신이 호스트 머신의 TPM에 액세스할 수 있습니다.

1. 관리자 권한 세션에서 PowerShell을 엽니다.

1. 아직 디바이스에서 실행 정책을 `AllSigned`로 설정하지 않은 경우 지금 설정합니다. 그래야만 IoT Edge for Linux on Windows PowerShell 함수를 실행할 수 있습니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. TPM 기능을 켭니다.

   ```powershell
   Set-EflowVmFeature -feature 'DpsTpm' -enable
   ```

## <a name="retrieve-the-tpm-information-from-your-device"></a>디바이스에서 TPM 정보 검색

# <a name="physical-tpm"></a>[물리적 TPM](#tab/physical-tpm)

디바이스를 프로비저닝하려면 TPM 칩에서 정보를 수집하여 DPS(Device Provisioning Service) 인스턴스에 제공해야만 이 서비스가 연결을 시도할 때 디바이스를 인식할 수 있습니다.

먼저 **인증 키** 를 확인해야 합니다. 이 키는 각 TPM 칩에서 고유하고 연결된 TPM 칩 제조업체에서 가져옵니다. 그런 다음, 디바이스의 **등록 ID** 를 제공해야 합니다. 예를 들어 인증 키의 SHA-256 해시를 만들어 TPM 디바이스의 고유한 등록 ID를 파생할 수 있습니다.

에서 Linux에 대 한 IoT Edge Windows TPM에서이 정보를 검색 하는 데 도움이 되는 PowerShell 스크립트를 제공 합니다. 스크립트를 사용하려면 디바이스에서 다음 단계를 수행합니다.

1. 관리자 권한 세션에서 PowerShell을 엽니다.

1. [Iotedge-eflow](https://github.com/Azure/iotedge-eflow) 리포지토리를 복제 합니다.

   ```powershell
   git clone https://github.com/Azure/iotedge-eflow.git
   ```

1. 다운로드 한 모듈을 가져옵니다.

   ```powershell
   Import-Module <path>\iotedge-eflow\samples\scripts\EflowTpmProvisioningInfo.ps1
   ```

1. 명령을 실행합니다.

   ```powershell
   Get-EflowVmTpmProvisioningInformation
   ```

# <a name="simulated-tpm"></a>[시뮬레이션 된 TPM](#tab/simulated-tpm)

사용 가능한 실제 TPM이 없고 이 프로비저닝 메서드를 테스트하려는 경우에는 디바이스에서 TPM을 시뮬레이션하면 됩니다.

IoT Hub Device Provisioning Service TPM을 시뮬레이션 하 고 **인증 키** 및 **등록 ID** 를 반환 하는 샘플을 제공 합니다.

1. 원하는 언어에 따라 다음 목록에서 샘플 중 하나를 선택합니다.
1. 시뮬레이션 된 TPM을 실행 중이 고 **인증 키** 및 **등록 ID** 를 수집한 후 DPS 샘플 단계를 중지 합니다. *Enter* 키를 눌러 샘플 애플리케이션에서 등록을 실행하지 마세요.
1. 이 시나리오 테스트를 완료할 때까지 시뮬레이션 된 TPM을 호스트 하는 창을 유지 합니다.
1. 이 문서로 돌아와서 DPS 등록을 만들고 디바이스를 구성합니다.

시뮬레이션된 TPM 샘플:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.JS](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

---

## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

TPM의 프로 비전 정보를 사용 하 여 장치 프로 비전 서비스에 개별 등록을 만듭니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태** 를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

> [!TIP]
> 이 문서의 단계는 Azure Portal에 대 한 것 이지만 Azure CLI를 사용 하 여 개별 등록를 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)를 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동 합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. **개별 등록 추가** 를 선택 하 고 다음 단계를 완료 하 여 등록을 구성 합니다.

   1. **메커니즘** 의 경우 **TPM** 을 선택합니다.

   1. 가상 컴퓨터 또는 물리적 장치에서 복사한 **인증 키** 와 **등록 ID** 를 제공 합니다.

   1. 원하는 경우 디바이스에 대한 ID를 제공합니다. 디바이스 ID를 제공하지 않으면 등록 ID가 사용됩니다.

   1. 가상 컴퓨터 또는 물리적 장치가 IoT Edge 장치 임을 선언 하려면 **True** 를 선택 합니다.

   1. 장치를 연결 하려는 연결 된 IoT hub를 선택 하거나 **새 IoT Hub에 연결** 을 선택 합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 허브 중 하나에 디바이스가 할당됩니다.

   1. 원하는 경우 **초기 디바이스 쌍 상태** 에 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 자세한 내용은 [대규모로 IoT Edge 모듈 배포](how-to-deploy-at-scale.md)를 참조하세요.

   1. **저장** 을 선택합니다.

이제이 장치에 대 한 등록이 있으므로 IoT Edge 런타임에서 프로 비전 할 수 있습니다.

## <a name="configure-the-device-with-provisioning-information"></a>프로비저닝 정보를 사용하여 디바이스 구성

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Windows 디바이스에서 관리자 권한 PowerShell 세션을 엽니다.

1. Device Provisioning Service 인스턴스에서 수집한 **범위 ID** 를 사용하여 디바이스를 프로비저닝합니다.

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "<scope id>"
   ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **연결** 단계에서 디바이스를 프로비저닝합니다.

   1. **DpsTpm** 프로비저닝 메서드를 선택합니다.
   1. Device Provisioning Service의 인스턴스에서 검색하는 **범위 ID** 를 제공합니다.

   ![DPS 및 TPM 증명을 사용하여 디바이스를 프로비저닝합니다.](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. **선택한 방법으로 프로비저닝** 을 선택합니다.

1. IoT Edge가 성공적으로 디바이스에 설치되고 프로비저닝되었으면 **마침** 을 선택하여 배포 마법사를 종료합니다.

---

## <a name="verify-successful-configuration"></a>성공적인 구성 확인

IoT Edge for Linux on Windows가 IoT Edge 디바이스에 성공적으로 설치되고 구성되었는지 확인합니다.

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하고 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다.

Device Provisioning Service에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure Portal에서 Device Provisioning Service 인스턴스로 이동합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

디바이스에서 다음 명령을 사용하여 IoT Edge가 성공적으로 설치되고 시작되는지 확인합니다.

1. PowerShell 세션에서 다음 명령을 사용하여 IoT Edge for Linux on Windows 가상 머신에 연결합니다.

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

    1. 서비스 문제를 해결해야 할 경우 서비스 로그를 검색합니다.

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

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 디바이스를 프로비전할 때 디바이스 ID 및 디바이스 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-cli-at-scale.md)하거나 [Azure Portal을 사용하여 대규모 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-at-scale.md)하는 방법을 알아봅니다.