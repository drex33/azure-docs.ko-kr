---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 890ec787bfc7a4316df9d27271460a90d5047175
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842703"
---
## <a name="install-iot-edge"></a>IoT Edge 설치

대상 디바이스에 Windows의 Linux용 Azure IoT Edge를 배포합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

대상 디바이스의 Windows Linux용 IoT Edge 설치합니다.

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

1. 대상 디바이스의 실행 정책이 아직 설정되지 않은 경우 `AllSigned`로 설정합니다. 현재 실행 정책을 확인하고 실행 정책을 로 설정하려면 명령에 대한 PowerShell 필수 구성요약을 `AllSigned` 참조하세요.

1. IoT Edge for Linux on Windows 배포를 만듭니다. 배포는 Linux 가상 머신을 만들고 IoT Edge 런타임을 설치합니다.

   ```powershell
   Deploy-Eflow
   ```

   >[!TIP]
   >기본적으로 `Deploy-Eflow` 명령은 RAM 1GB, vCPU 코어 1개, 디스크 공간 16GB를 사용하여 Linux 가상 머신을 만듭니다. 그러나 VM에 필요한 리소스는 배포하는 워크로드에 따라 달라집니다. 워크로드를 지원하기에 충분 한 메모리가 없는 VM은 시작되지 않습니다.
   >
   >`Deploy-Eflow` 명령의 선택적 매개 변수를 사용하여 가상 컴퓨터의 사용 가능한 리소스를 사용자 지정할 수 있습니다.
   >
   >예를 들어 아래 명령은 4개의 vCPU 코어, 4GB RAM(MB로 표시) 및 20GB의 디스크 공간이 있는 가상 머신을 만듭니다.
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >사용 가능한 모든 선택적 매개 변수에 대한 자세한 내용은 [Windows의 Linux용 IoT Edge용 PowerShell 함수](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)를 참조하세요.

   배포에 GPU를 할당하여 GPU 가속 Linux 모듈을 사용하도록 설정할 수 있습니다. 이러한 기능에 액세스하려면 [Windows의 Linux용 Azure IoT Edge용 GPU 가속](../articles/iot-edge/gpu-acceleration.md)에 설명된 필수 구성 요소를 설치해야 합니다.

   GPU 통과를 사용하려면 **gpuName,** **gpuPassthroughType** 및 **gpuCount** 매개 변수를 `Deploy-Eflow` 명령에 추가합니다. 사용 가능한 모든 선택적 매개 변수에 대한 자세한 내용은 [Windows의 Linux용 IoT Edge용 PowerShell 함수](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)를 참조하세요.

   >[!WARNING]
   >하드웨어 디바이스 통과를 사용하도록 설정하면 보안 위험이 증가할 수 있습니다. Microsoft는 해당되는 경우 GPU 공급업체의 디바이스 완화 드라이버를 권장합니다. 자세한 내용은 [개별 디바이스 할당을 사용하여 그래픽 디바이스 배포](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)를 참조하세요.

1. 'Y'를 입력하여 사용 조건에 동의합니다.

1. 기본 설정에 따라 'O' 또는 'R'을 입력하여 **선택적 진단 데이터** 를 설정하거나 해제합니다.

1. 배포가 완료되면 PowerShell 창에 **배포 성공** 이 보고됩니다.

   ![배포가 성공하면 메시지 끝에 '배포 성공'이 표시됩니다( PNG).](./media/iot-edge-install-linux-on-windows/successful-powershell-deployment.png)

   배포가 성공하면 디바이스를 프로비전할 준비가 된 것입니다.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Windows Admin Center용 Azure IoT Edge 확장은 현재 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)입니다. 설치 및 관리 프로세스는 일반적으로 사용할 수 있는 기능과 다를 수 있습니다.

디바이스의 Windows linux용 Azure IoT Edge를 설치합니다.

1. Windows [설치 관리자 에서 Linux용 Azure IoT Edge를 다운로드합니다.](https://aka.ms/AzEflowMSI)

1. Windows linux용 Azure IoT Edge를 설치했으면 Windows 관리 센터를 엽니다.

   Windows Admin Center 시작 페이지의 연결 목록 아래에는 Windows Admin Center를 실행하는 PC를 나타내는 로컬 호스트 연결이 표시됩니다. 관리하는 추가 서버, PC 또는 클러스터도 여기에 표시됩니다.

   Windows Admin Center를 사용하여 Azure IoT Edge for Linux on Windows를 로컬 디바이스 또는 원격 관리 디바이스에 설치하고 관리할 수 있습니다. 이 가이드에서 로컬 호스트 연결은 Azure IoT Edge for Linux on Windows를 배포하기 위한 대상 디바이스 역할을 수행합니다.

1. 로컬 디바이스가 아래와 같이 모든 연결 에 나열되어 있는지 **확인합니다.**

   ![대상 디바이스가 PNG로 나열된 초기 Windows 관리 센터 대시보드](./media/iot-edge-install-linux-on-windows/windows-admin-center-initial-dashboard.png)

   로컬 디바이스 대신 원격 대상 디바이스에 배포하려는데 목록에 원하는 대상 디바이스가 표시되지 않는 경우 [지침에 따라 디바이스를 추가합니다.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)

1. **+ 추가를** 선택하여 배포 만들기를 시작합니다. 배포는 Linux 가상 머신을 만들고 IoT Edge 런타임을 설치합니다.

1. **리소스 추가 또는 만들기** 창에서 **Azure IoT Edge** 타일을 찾습니다. **새로 만들기** 를 선택하여 새 Azure IoT Edge for Linux on Windows 인스턴스를 디바이스에 설치합니다.

   디바이스에서 실행되는 IoT Edge for Linux on Windows가 이미 있는 경우 **추가** 를 선택하여 기존 IoT Edge 디바이스에 연결하고 Windows Admin Center에서 해당 디바이스를 관리할 수 있습니다.

   ![Windows Admin Center, PNG에서 Azure IoT Edge 타일에서 새로 만들기를 선택합니다.](./media/iot-edge-install-linux-on-windows/resource-creation-tiles.png)

1. **Azure IoT Edge for Linux on Windows 배포 만들기** 창이 열립니다. **1. 시작하기** 탭에서 최소 요구 사항을 검토하고 **다음** 을 선택합니다.

1. 사용 조건을 검토하고, **동의함** 을 선택하고, **다음** 을 선택합니다.

1. 기본 설정에 따라 **선택적 진단 데이터** 를 설정하거나 해제할 수 있습니다.

1. **다음: 배포** 를 선택합니다.

   ![선택적 진단 데이터를 원하는 PNG로 전환한 후 다음: 배포 단추를 선택합니다.](./media/iot-edge-install-linux-on-windows/select-next-deploy.png)

1. **2. 배포** 탭의 **대상 디바이스 선택** 아래에서 나열된 디바이스를 클릭하여 최소 요구 사항을 충족하는지 확인합니다. 상태가 '지원됨'으로 확인되면 **다음** 을 선택합니다.

   ![디바이스를 선택하여 PNG가 지원되는지 확인합니다.](./media/iot-edge-install-linux-on-windows/evaluate-supported-device.png)

1. **2.2 설정** 탭에서 배포의 구성 설정을 검토합니다. 아래 표에서는 설정 및 해당 기본값을 보여 주세요.

   | 설정 | 기본값 |
   | ------- | ------------- |
   | 가상 디스크 크기(GB) | 16 |
   | 메모리(MB) | 1024 |
   | 코어 수 | 2 |
   | Azure IoT Edge 버전 | 1.1(LTS) |

   >[!NOTE]
   >Windows의 Linux용 IoT Edge는 Linux 가상 머신에 내부 IP 주소를 할당하는 기본 스위치를 사용합니다. 이 내부 IP 주소는 Windows 시스템 외부에서 연결할 수 없습니다. Windows 컴퓨터에 로그온한 상태에서 가상 컴퓨터에 로컬로 연결할 수 있습니다.
   >
   >Windows Server를 사용하는 경우 Windows Linux용 IoT Edge 배포하기 전에 [기본 스위치를 설정합니다.](../articles/iot-edge/how-to-create-virtual-switch.md)

   배포에 GPU를 할당하여 GPU 가속 Linux 모듈을 사용하도록 설정할 수 있습니다. 이러한 기능에 액세스하려면 [Windows의 Linux용 Azure IoT Edge용 GPU 가속](../articles/iot-edge/gpu-acceleration.md)에 설명된 필수 구성 요소를 설치해야 합니다. 배포 프로세스의 이 시점에서 이러한 필수 구성 요소만 설치하는 경우 처음부터 다시 시작해야 합니다.

   배포에 할당하는 GPU 어댑터에 따라 사용 가능한 GPU 통과에는 **DDA(직접 디바이스 할당)** 및 **GPU-PV(GPU 반가상화)** 의 두 가지 옵션이 있습니다.

   직접 디바이스 할당 방법의 경우 Linux 가상 머신에 할당할 GPU 프로세서 수를 선택합니다.

   반가상화 방법의 경우 추가 설정이 필요하지 않습니다.

   >[!WARNING]
   >하드웨어 디바이스 통과를 사용하도록 설정하면 보안 위험이 증가할 수 있습니다. Microsoft는 해당되는 경우 GPU 공급업체의 디바이스 완화 드라이버를 권장합니다. 자세한 내용은 [개별 디바이스 할당을 사용하여 그래픽 디바이스 배포](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)를 참조하세요.

   설정에 만족하면 **다음** 을 선택합니다.

1. **2.3 배포** 탭에서 배포 상황을 확인할 수 있습니다. 전체 프로세스에는 Azure IoT Edge for Linux on Windows 패키지 다운로드, 패키지 설치, 호스트 디바이스 구성 및 Linux 가상 머신 설정이 포함됩니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다. 성공적인 배포는 다음과 같습니다.

   ![배포가 성공하면 각 단계에 녹색 확인 표시와 '완료' 레이블 PNG가 표시됩니다.](./media/iot-edge-install-linux-on-windows/successful-deployment.png)

1. 배포가 완료되면 디바이스를 프로비전할 준비가 되었습니다. **다음: 연결** 을 선택하여 Azure IoT Edge 디바이스 프로비전을 처리하는 **3. 연결** 탭으로 이동합니다.

---
