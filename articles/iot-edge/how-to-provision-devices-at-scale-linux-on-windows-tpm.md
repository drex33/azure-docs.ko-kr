---
title: TPM Azure IoT Edge를 사용 하 여 Windows 장치에서 Linux 용 IoT Edge 프로 비전 Microsoft Docs
description: Windows 장치의 Linux에서 시뮬레이션 된 TPM을 사용 하 여 Azure IoT에 지에 대 한 Azure device 프로 비전 서비스를 테스트 합니다.
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 07/08/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 048c4a2671940356faad4eed810925806612ebd1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246702"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-at-scale-by-using-a-tpm"></a>TPM을 사용 하 여 규모에 따라 Windows 장치에서 Linux 용 IoT Edge 만들기 및 프로 비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 TPM (신뢰할 수 있는 플랫폼 모듈)을 사용 하 여 Windows 장치에서 Linux 용 Azure IoT Edge를 자동 프로 비전 비전 하기 위한 지침을 제공 합니다. [Azure IoT 허브 장치 프로 비전 서비스](../iot-dps/index.yml)를 사용 하 여 Azure IoT에 지 장치를 자동으로 프로 비전 할 수 있습니다. Autoprovisioning 비전 프로세스에 익숙하지 않은 경우 계속 하기 전에 [프로 비전 개요](../iot-dps/about-iot-dps.md#provisioning-process) 를 검토 합니다.

이 문서에서는 두 가지 방법을 설명 합니다. 솔루션의 아키텍처에 따라 기본 설정을 선택 합니다.

- 물리적 TPM 하드웨어를 사용 하 여 Windows 장치에서 Linux를 Autoprovision 합니다.
- 시뮬레이션 된 TPM을 사용 하 여 Windows 장치에서 Linux를 Autoprovision 합니다. 이 방법론은 테스트 시나리오로만 권장 됩니다. 시뮬레이션 된 TPM은 물리적 TPM과 동일한 보안을 제공 하지 않습니다.

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

* 활성 IoT 허브
* Azure에서 IoT Hub에 연결 된 IoT Hub 장치 프로 비전 서비스의 인스턴스입니다.
  * 장치 프로 비전 서비스 인스턴스가 없는 경우 IoT Hub 장치 프로 비전 서비스 빠른 시작의 두 섹션에 있는 지침을 따르세요.
    - [새 IoT Hub 장치 프로 비전 서비스 만들기](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)
    - [IoT hub 및 장치 프로 비전 서비스 연결](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)
  * 장치 프로 비전 서비스를 실행 한 후 개요 페이지에서 **ID 범위** 값을 복사 합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

개발 머신:

* 다음과 같은 최소 시스템 요구 사항을 충족하는 Windows 디바이스:
  * Windows 10 버전 1809 이상, 빌드 17763 이상
  * Professional, Enterprise 또는 Server 버전
  * 사용 가능한 최소 메모리: 1gb
  * 사용 가능한 최소 디스크 공간: 10gb
  * 가상화 지원
    * Windows 10에서 Hyper-V를 사용하도록 설정합니다. 자세한 내용은 [Hyper-V를 Windows 10에 설치](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)를 참조하세요.
    * Windows Server에서 Hyper-V 역할을 설치하고 기본 네트워크 스위치를 만듭니다. 자세한 내용은 [Azure IoT Edge for Linux on Windows의 중첩된 가상화](nested-virtualization.md)를 참조하세요.
    * VM (가상 컴퓨터)에서 중첩 된 가상화를 구성 합니다. 자세한 내용은 [중첩 된 가상화](nested-virtualization.md)를 참조 하세요.
  * 네트워킹 지원
    * Windows 서버에 기본 스위치가 제공 되지 않습니다. EFLOW를 Windows Server 디바이스에 배포하려면 먼저 가상 스위치를 만들어야 합니다. 자세한 내용은 [Windows에서 Linux 용 가상 스위치 만들기](how-to-create-virtual-switch.md)를 참조 하세요.
    * Windows 데스크톱 버전에는 EFLOW 설치에 사용할 수 있는 기본 스위치가 있습니다. 필요한 경우 고유한 사용자 지정 가상 스위치를 만들 수 있습니다.

   > [!TIP]
   > Windows 배포에서 linux 용 IoT Edge의 GPU 가속 linux 모듈을 사용 하려는 경우 몇 가지 구성 옵션을 고려해 야 합니다. GPU 아키텍처에 따라 올바른 드라이버를 설치 해야 하며, Windows Insider Program 빌드에 액세스 해야 할 수 있습니다. 구성 요구 사항을 확인 하 고 이러한 필수 조건을 충족 하려면 [Windows에서 Linux 용 IoT Edge의 GPU 가속](gpu-acceleration.md)을 참조 하세요.

PowerShell 또는 Windows 관리 센터를 사용 하 여 IoT Edge 장치를 관리할 수 있습니다. 각 유틸리티에는 고유한 필수 구성 요소가 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하려는 경우 다음 단계를 수행 하 여 linux VM의 배포 및 Windows에 linux 용 IoT Edge를 설치 하기 위한 대상 장치를 준비 합니다.

1. 대상 장치에 대 한 실행 정책을로 설정 `AllSigned` 합니다. 다음 명령을 사용 하 여 관리자 권한 PowerShell 프롬프트에서 현재 실행 정책을 확인할 수 있습니다.

   ```powershell
   Get-ExecutionPolicy -List
   ```

   의 실행 정책이이 `local machine` 아닌 경우 `AllSigned` 다음 명령을 사용 하 여 실행 정책을 설정할 수 있습니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Windows PowerShell 모듈의 Linux 용 IoT Edge에 대 한 자세한 내용은 [PowerShell 함수 참조](reference-iot-edge-for-linux-on-windows-functions.md)를 참조 하세요.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows 관리 센터를 사용 하려는 경우 다음 단계에 따라 Windows 관리 센터를 다운로드 및 설치 하 고 Windows 관리 센터 Azure IoT Edge 확장을 설치 합니다.

   1. [Windows Admin Center 설치 관리자](https://aka.ms/wacdownload)를 다운로드 및 실행합니다. 설치 마법사의 프롬프트에 따라 Windows Admin Center를 설치합니다.

   1. 설치가 완료 되 면 지원 되는 브라우저를 사용 하 여 Windows 관리 센터를 엽니다. 지원되는 브라우저에는 Microsoft Edge(Windows 10 버전 1709 이상), Google Chrome 및 Microsoft Edge Insider가 있습니다.

   1. Windows 관리 센터를 처음 사용 하는 경우 사용할 인증서를 선택 하 라는 메시지가 표시 됩니다. 인증서로 **Windows Admin Center 클라이언트** 를 선택합니다.

   1. Azure IoT Edge 확장을 설치합니다. Windows 관리 센터 대시보드의 오른쪽 위 모퉁이에서 기어 아이콘을 선택 합니다.

      ![설정에 액세스 하기 위해 대시보드의 오른쪽 위 모퉁이에서 기어 아이콘을 선택 하는 것을 보여 주는 스크린샷](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-gear-icon.png)

   1. **설정** 메뉴의 **게이트웨이** 아래에서 **확장** 을 선택합니다.

   1. **사용 가능한 확장** 탭의 확장 목록에서 **Azure IoT Edge** 를 찾습니다. 이를 선택하고, 확장 목록 위의 **설치** 프롬프트를 선택합니다.

   1. 설치가 완료되면 **설치된 확장** 탭의 설치된 확장 목록에 Azure IoT Edge가 표시됩니다.

---

> [!NOTE]
> Tpm 2.0는 장치 프로 비전 서비스에 TPM 증명을 사용 하는 경우에 필요 합니다.
>
> TPM을 사용 하는 경우 그룹, 장치 프로 비전 서비스 등록만 만들 수 있습니다.

## <a name="install-iot-edge"></a>IoT Edge 설치

대상 장치의 Windows에 Linux 용 IoT Edge를 배포 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

대상 장치의 Windows에 Linux 용 IoT Edge를 설치 합니다.

> [!NOTE]
> 다음 PowerShell 프로세스는 로컬 장치에서 Windows에 Linux 용 IoT Edge를 배포 하는 방법을 간략하게 설명 합니다. PowerShell을 사용 하 여 원격 대상 장치에 배포 하려면 원격 [powershell](/powershell/module/microsoft.powershell.core/about/about_remote) 을 사용 하 여 원격 장치에 대 한 연결을 설정 하 고 해당 장치에서 이러한 명령을 원격으로 실행할 수 있습니다.

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

1. 대상 장치에 대 한 실행 정책을 아직 설정 `AllSigned` 하지 않은 경우로 설정 합니다. 현재 실행 정책을 확인 하 고 실행 정책을로 설정 하는 명령은 PowerShell 필수 구성 요소를 참조 하세요 `AllSigned` .

1. IoT Edge for Linux on Windows 배포를 만듭니다. 배포는 Linux VM을 만들고 IoT Edge 런타임을 설치 합니다.

   ```powershell
   Deploy-Eflow
   ```

   >[!TIP]
   >기본적으로이 `Deploy-Eflow` 명령은 1GB RAM, 1 개 vCPU 코어 및 16gb의 디스크 공간을 사용 하 여 LINUX VM을 만듭니다. 그러나 VM에 필요한 리소스는 배포하는 워크로드에 따라 달라집니다. 워크 로드를 지원 하기에 충분 한 메모리가 없는 VM은 시작 되지 않습니다.
   >
   >명령의 선택적 매개 변수를 사용 하 여 VM의 사용 가능한 리소스를 사용자 지정할 수 있습니다 `Deploy-Eflow` .
   >
   >예를 들어 다음 명령은 4 개의 vCPU 코어, 4gb RAM 및 20gb의 디스크 공간을 사용 하 여 VM을 만듭니다.
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >사용 가능한 모든 선택적 매개 변수에 대한 자세한 내용은 [Windows의 Linux용 IoT Edge용 PowerShell 함수](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)를 참조하세요.

   배포에 GPU를 할당하여 GPU 가속 Linux 모듈을 사용하도록 설정할 수 있습니다. 이러한 기능에 액세스 하려면 [Windows에서 Linux 용 Azure IoT Edge에 대 한 GPU 가속](gpu-acceleration.md)에 자세히 설명 된 필수 구성 요소를 설치 합니다.

   GPU 통과를 사용하려면 **gpuName,** **gpuPassthroughType** 및 **gpuCount** 매개 변수를 `Deploy-Eflow` 명령에 추가합니다. 사용 가능한 모든 선택적 매개 변수에 대한 자세한 내용은 [Windows의 Linux용 IoT Edge용 PowerShell 함수](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)를 참조하세요.

   >[!WARNING]
   >하드웨어 디바이스 통과를 사용하도록 설정하면 보안 위험이 증가할 수 있습니다. Microsoft는 해당되는 경우 GPU 공급업체의 디바이스 완화 드라이버를 권장합니다. 자세한 내용은 [개별 디바이스 할당을 사용하여 그래픽 디바이스 배포를](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)참조하세요.

1. 사용 조건에 동의하려면 **Y를** 입력합니다.

1. 기본 설정에 따라 **선택적 진단 데이터를** 켜거나 끄려면 **O** 또는 **R을** 입력합니다.

1. 배포가 완료되면 PowerShell 창에 **배포 성공이** 보고됩니다.

   ![메시지 끝에 '배포 성공'이라는 성공적인 배포를 보여 주는 스크린샷.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-powershell-deployment.png)

배포가 완료되면 디바이스를 프로비전할 준비가 된 것입니다.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Windows Admin Center용 Azure IoT Edge 확장은 현재 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)입니다. 설치 및 관리 프로세스는 일반적으로 사용 가능한 기능과 다를 수 있습니다.

IoT Edge for Linux on Windows를 디바이스에 설치합니다.

1. Windows [설치 관리자 에서 Linux용 IoT Edge 다운로드합니다.](https://aka.ms/AzEflowMSI)

1. Windows Linux용 IoT Edge 설치한 후 Windows 관리 센터를 엽니다.

   Windows 관리 센터 시작 페이지의 연결 목록에 Windows 관리 센터를 실행하는 PC를 나타내는 로컬 호스트 연결이 표시됩니다. 관리하는 다른 서버, PC 또는 클러스터도 여기에 표시됩니다.

   Windows 관리 센터를 사용하여 로컬 디바이스 또는 원격 관리 디바이스의 Windows Linux용 IoT Edge 설치하고 관리할 수 있습니다. 이 가이드에서 로컬 호스트 연결은 Windows Linux용 IoT Edge 배포를 위한 대상 디바이스로 사용됩니다.

1. 표시된 것처럼 로컬 디바이스가 모든 연결 아래에 나열되어 있는지 **확인합니다.**

   ![대상 디바이스가 나열된 초기 Windows 관리 센터 대시보드를 보여 주는 스크린샷](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/windows-admin-center-initial-dashboard.png)

   로컬 디바이스 대신 원격 대상 디바이스에 배포하려는데 목록에 원하는 대상 디바이스가 표시되지 않는 경우 지침에 따라 [디바이스를 추가합니다.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)

1. **+ 추가를** 선택하여 배포 만들기를 시작합니다. 배포는 Linux VM을 만들고 IoT Edge 런타임을 설치합니다.

1. **리소스 추가 또는 만들기** 창에서 **Azure IoT Edge** 타일을 찾습니다. **새로 만들기를** 선택하여 디바이스의 Windows Linux용 IoT Edge 새 인스턴스를 설치합니다.

   디바이스에서 실행 중인 Windows Linux용 IoT Edge 이미 있는 경우 **추가를** 선택하여 기존 IoT Edge 디바이스에 연결하고 Windows 관리 센터에서 관리합니다.

   ![Windows 관리 센터의 Azure IoT Edge 타일에서 새로 만들기를 선택하는 스크린샷](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/resource-creation-tiles.png)

1. **Windows 배포에서 Linux용 Azure IoT Edge 만들기** 창이 나타납니다. **1. 시작하기** 탭에서 최소 요구 사항을 검토하고 **다음** 을 선택합니다.

1. 사용 조건을 검토하고 **동의를** 선택한 **후 다음을** 선택합니다.

1. 기본 설정에 따라 **선택적 진단 데이터를** 켜거나 끄기로 전환합니다.

1. **다음: 배포** 를 선택합니다.

   ![선택적 진단 데이터를 기본 설정으로 전환한 후 다음: 배포 단추를 선택하는 것을 보여 주는 스크린샷.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-next-deploy.png)

1. **2. 배포** 탭의 **대상 디바이스 선택에서** 나열된 디바이스를 선택하여 최소 요구 사항을 충족하는지 확인합니다. 상태가 지원됨으로 확인되면 **다음을** 선택합니다.

   ![디바이스를 선택하여 디바이스가 지원되는지 확인하는 스크린샷.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/evaluate-supported-device.png)

1. **2.2 설정** 탭에서 배포의 구성 설정을 검토합니다.

   >[!NOTE]
   >Windows Linux용 IoT Edge Linux VM에 내부 IP 주소를 할당하는 기본 스위치를 사용합니다. 이 내부 IP 주소는 Windows 컴퓨터 외부에서 연결할 수 없습니다. Windows 머신에 로그 온하는 동안 로컬로 VM에 연결할 수 있습니다.
   >
   >Windows Server를 사용하는 경우 Windows Linux용 IoT Edge 배포하기 전에 [기본 스위치를 설정합니다.](how-to-create-virtual-switch.md)

   배포에 GPU를 할당하여 GPU 가속 Linux 모듈을 사용하도록 설정할 수 있습니다. 이러한 기능에 액세스하려면 Windows [linux용 Azure IoT Edge에 대한 GPU 가속에](gpu-acceleration.md)자세히 설명된 필수 구성 요소 를 설치해야 합니다. 배포 프로세스의 이 시점에서 이러한 필수 조건만 설치하는 경우 처음부터 다시 시작해야 합니다.

   GPU 통과에 사용할 수 있는 두 가지 옵션은 배포에 할당하는 GPU 어댑터에 따라 **DDA(직접 디바이스 할당)** 및 **GPU 반가상화(GPU-PV)입니다.**

   직접 디바이스 할당 방법의 경우 Linux VM에 할당할 GPU 프로세서 수를 선택합니다.

   ![직접 디바이스 할당 GPU가 사용하도록 설정된 구성 설정을 보여 주는 스크린샷.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-direct-device-assignment.png)

   paravirtualization 메서드의 경우 다른 설정이 필요하지 않습니다.

   ![paravirtualization GPU가 사용하도록 설정된 구성 설정을 보여 주는 스크린샷](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >하드웨어 디바이스 통과를 사용하도록 설정하면 보안 위험이 증가할 수 있습니다. Microsoft는 해당되는 경우 GPU 공급업체의 디바이스 완화 드라이버를 권장합니다. 자세한 내용은 [직접 디바이스 할당을 사용하여 그래픽 디바이스 배포를 참조하세요.](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)

   설정에 만족하면 **다음을** 선택합니다.

1. **2.3 배포** 탭에서 배포 상황을 확인할 수 있습니다. 전체 프로세스에는 Windows 패키지에서 Linux용 IoT Edge 다운로드, 패키지 설치, 호스트 디바이스 구성 및 Linux VM 설정이 포함됩니다. 이 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다. 성공적인 배포가 표시됩니다.

   ![각 단계가 녹색 확인 표시 및 '완료' 레이블로 표시된 성공적인 배포를 보여 주는 스크린샷.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-deployment.png)

1. 배포가 완료되면 디바이스를 프로비전할 준비가 된 것입니다. **다음: 커넥트** 선택하여 **3으로 진행합니다. 커넥트** 탭 - IoT Edge 디바이스 프로비저닝을 처리합니다.

계속하기 전에 TPM을 사용하여 **프로비저닝할 디바이스를** 준비하기 위해 수행해야 하는 몇 가지 단계가 있으므로 프로비전 페이지에서 배포 마법사를 Stop following. 프로비전 단계를 완료하려면 이 문서의 끝에 반환되므로 Windows 관리 센터 창을 열어 주세요.

---

## <a name="enable-tpm-passthrough"></a>TPM 통과 사용

Windows VM의 Linux용 IoT Edge 사용하거나 사용하지 않도록 설정할 수 있는 TPM 기능이 있습니다. 이 기능은 기본적으로 사용하지 않도록 설정됩니다. 이 기능을 사용하도록 설정하면 VM이 호스트 컴퓨터의 TPM에 액세스할 수 있습니다.

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

# <a name="physical-tpm"></a>[실제 TPM](#tab/physical-tpm)

디바이스를 프로비전하려면 TPM 칩에서 정보를 수집하고 서비스에서 연결을 시도할 때 디바이스를 인식할 수 있도록 디바이스 프로비저닝 서비스의 인스턴스에 제공해야 합니다.

먼저 각 TPM 칩에 고유하고 연결된 TPM 칩 제조업체에서 가져온 인증 키를 결정해야 합니다. 그런 다음 디바이스에 대한 등록 ID를 제공해야 합니다. 예를 들어 인증 키의 SHA-256 해시를 만들어 TPM 디바이스의 고유한 등록 ID를 파생할 수 있습니다.

Windows Linux용 IoT Edge TPM에서 이 정보를 검색하는 데 도움이 되는 PowerShell 스크립트를 제공합니다. 스크립트를 사용하려면 디바이스에서 다음 단계를 수행합니다.

1. 관리자 권한 세션에서 PowerShell을 엽니다.

1. 명령을 실행합니다.

   ```powershell
   Get-EflowVmTpmProvisioningInfo
   ```

# <a name="simulated-tpm"></a>[시뮬레이션된 TPM](#tab/simulated-tpm)

사용 가능한 실제 TPM이 없고 이 프로비저닝 메서드를 테스트하려는 경우에는 디바이스에서 TPM을 시뮬레이션하면 됩니다.

IoT Hub 디바이스 프로비저닝 서비스는 TPM을 시뮬레이션하고 인증 키 및 등록 ID를 반환하는 샘플을 제공합니다.

1. 원하는 언어에 따라 다음 목록에서 샘플 중 하나를 선택합니다.
1. 시뮬레이션된 TPM이 실행되고 **인증 키** 및 **등록 ID** 를 수집한 후 디바이스 프로비저닝 서비스 샘플 단계를 Stop following. 샘플 애플리케이션에서 등록을 실행하려면 **Enter** 키를 선택하지 마세요.
1. 이 시나리오 테스트를 완료할 때까지 시뮬레이션된 TPM을 호스트하는 창을 계속 실행합니다.
1. 이 문서로 돌아가서 디바이스 프로비저닝 서비스 등록을 만들고 디바이스를 구성합니다.

시뮬레이션된 TPM 샘플:

* [C](../iot-dps/quick-create-simulated-device-tpm.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm.md)
* [Node.JS](../iot-dps/quick-create-simulated-device-tpm.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm.md)

---

## <a name="create-a-device-provisioning-service-enrollment"></a>디바이스 프로비저닝 서비스 등록 만들기

TPM의 프로비전 정보를 사용하여 디바이스 프로비저닝 서비스에서 개별 등록을 만듭니다.

디바이스 프로비저닝 서비스에서 등록을 만들 때 초기 디바이스 **쌍 상태** 를 선언할 수 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

> [!TIP]
> 이 문서의 단계는 Azure Portal 위한 단계이지만 Azure CLI 사용하여 개별 등록을 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)를 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다.

1. [Azure Portal](https://portal.azure.com)IoT Hub 디바이스 프로비저닝 서비스의 인스턴스로 이동합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. **개별 등록 추가를** 선택한 다음, 다음 단계를 완료하여 등록을 구성합니다.

   1. **메커니즘** 의 경우 **TPM** 을 선택합니다.

   1. VM 또는 물리적 디바이스에서 복사한 **인증 키** 및 **등록 ID를** 제공합니다.

   1. 원하는 경우 디바이스에 대한 ID를 제공합니다. 디바이스 ID를 제공하지 않으면 등록 ID가 사용됩니다.

   1. **True를** 선택하여 VM 또는 물리적 디바이스가 IoT Edge 디바이스임을 선언합니다.

   1. 디바이스를 연결하려는 연결된 IoT Hub를 선택하거나 **새 IoT Hub 연결을** 선택합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 허브 중 하나에 디바이스가 할당됩니다.

   1. 원하는 경우 초기 **디바이스 쌍 상태에** 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 자세한 내용은 [대규모로 IoT Edge 모듈 배포](how-to-deploy-at-scale.md)를 참조하세요.

   1. **저장** 을 선택합니다.

이제 이 디바이스에 대한 등록이 존재하기 때문에 IoT Edge 런타임에서 프로비전할 수 있습니다.

## <a name="configure-the-device-with-provisioning-information"></a>프로비저닝 정보를 사용하여 디바이스 구성

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Windows 디바이스에서 관리자 권한 PowerShell 세션을 엽니다.

1. 디바이스 프로비전 서비스의 인스턴스에서 수집한 **범위 ID를** 사용하여 디바이스를 프로비전합니다.

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "<scope id>"
   ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **연결** 단계에서 디바이스를 프로비저닝합니다.

   1. **DpsTpm** 프로비저닝 메서드를 선택합니다.
   1. 디바이스 프로비저닝 서비스의 인스턴스에서 검색하는 **범위 ID를** 제공합니다.

      ![디바이스 프로비저닝 서비스 및 TPM 증명을 통해 디바이스를 프로비저닝하는 것을 보여 주는 스크린샷](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. **선택한 방법으로 프로비저닝** 을 선택합니다.

1. IoT Edge 성공적으로 설치되고 디바이스에 프로비전된 후 **마침을** 선택하여 배포 마법사를 종료합니다.

---

## <a name="verify-successful-configuration"></a>성공적인 구성 확인

IoT Edge for Linux on Windows가 IoT Edge 디바이스에 성공적으로 설치되고 구성되었는지 확인합니다.

런타임이 성공적으로 시작된 경우 IoT Hub로 이동하여 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다.

디바이스 프로비저닝 서비스에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure Portal 디바이스 프로비저닝 서비스 인스턴스로 이동합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

디바이스에서 다음 명령을 사용하여 IoT Edge가 성공적으로 설치되고 시작되는지 확인합니다.

1. PowerShell 세션에서 다음 명령을 사용하여 Windows VM에서 Linux용 IoT Edge 커넥트.

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >VM에 SSH할 수 있는 유일한 계정은 VM을 만든 사용자입니다.

1. 로그인한 후 다음 Linux 명령을 사용하여 실행 중인 IoT Edge 모듈 목록을 확인할 수 있습니다.

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

   * **IoT Edge 모듈 목록** 섹션에는 디바이스에서 실행되는 모듈이 표시됩니다. IoT Edge 서비스가 처음 시작되면 **edgeAgent** 모듈만 실행되는 것으로 표시됩니다. edgeAgent 모듈은 기본적으로 실행되며 디바이스에 배포하는 다른 모듈을 설치하고 시작하는 데 도움이 됩니다.
   * **IoT Edge 상태** 섹션에는 서비스 상태가 표시되며 **활성(실행 중)이어야** 합니다.

---

## <a name="next-steps"></a>다음 단계

디바이스 프로비전 서비스 등록 프로세스를 사용하면 새 디바이스를 프로비전하는 동시에 디바이스 ID 및 디바이스 쌍 태그를 설정할 수 있습니다. 이러한 값을 사용하여 자동 디바이스 관리를 통해 개별 디바이스 또는 디바이스 그룹을 대상으로 지정할 수 있습니다.

Azure Portal 또는 [Azure CLI 사용하여 대규모로 IoT Edge 모듈을 배포하고](how-to-deploy-at-scale.md) [모니터링하는](how-to-deploy-cli-at-scale.md)방법을 알아봅니다.