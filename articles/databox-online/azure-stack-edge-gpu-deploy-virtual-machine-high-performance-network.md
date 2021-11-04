---
title: Azure Stack Edge Pro GPU에 고성능 네트워크 vm 배포
description: Azure Stack Edge Pro GPU에 고성능 네트워크 vm을 배포 하는 방법에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/29/2021
ms.author: alkohli
ms.openlocfilehash: 4b3f2a2b232bec60edbce204008e245bcabee09c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103208"
---
# <a name="deploy-high-performance-network-vms-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 장치에 고성능 네트워크 vm 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Portal, 템플릿, Azure PowerShell cmdlet을 사용하고 Azure CLI 또는 Python 스크립트를 통해 Azure Stack Edge Pro GPU 디바이스에서 VM(가상 머신)을 만들고 관리할 수 있습니다. 이 문서에서는 Azure Stack Edge Pro GPU 장치에서 hpn (고성능 네트워크) VM을 만들고 관리 하는 방법을 설명 합니다. 

## <a name="about-hpn-vms"></a>HPN Vm 정보

NUMA (non-uniform memory access) 아키텍처는 프로세서 속도를 높이는 데 사용 됩니다. NUMA 시스템에서 Cpu는 노드 라는 작은 시스템으로 정렬 됩니다. 각 노드에는 고유한 프로세서 및 메모리가 있습니다. 일반적으로 프로세서는 가까이 있는 메모리를 할당 하므로 액세스 속도가 더 빨라집니다. 자세한 내용은 [NUMA 지원](/windows/win32/procthread/numa-support)을 참조 하세요.  

Azure Stack Edge 장치에서 논리적 프로세서는 NUMA 노드에 배포 되 고 고속 네트워크 인터페이스는 이러한 노드에 연결할 수 있습니다. HPN VM에는 논리 프로세서의 전용 집합이 있습니다. 이러한 프로세서는 먼저 고속 네트워크 인터페이스가 연결 된 NUMA 노드에서 선택 된 다음 다른 노드에서 선택 됩니다. HPN VM은 해당 프로세서에 할당 된 NUMA 노드의 메모리를 사용할 수 있습니다.  

장치에 배포 된 HPN Vm에서 짧은 대기 시간 및 높은 처리량의 네트워크 응용 프로그램을 실행 하려면 NUMA 노드 0에 상주 하는 vCPUs를 예약 해야 합니다. 이 노드에는 포트 5와 포트 6의 Mellanox 고속 네트워크 인터페이스가 연결 되어 있습니다.   

        
## <a name="hpn-vm-deployment-workflow"></a>HPN VM 배포 워크플로

HPN 배포 워크플로의 개략적인 요약은 다음과 같습니다.

1. Azure Stack Edge 디바이스에서 컴퓨팅용 네트워크 인터페이스를 사용하도록 설정합니다. 이 단계에서는 지정한 네트워크 인터페이스에서 가상 스위치를 만듭니다.
1. Azure Portal에서 VM의 클라우드 관리를 사용하도록 설정합니다.
1. Azure Storage Explorer를 사용하여 Azure Storage 계정에 VHD를 업로드합니다. 
1. 업로드된 VHD를 사용하여 디바이스에 VHD를 다운로드하고 VHD에서 VM 이미지를 만듭니다. 
1. HPN Vm에 대 한 장치에서 vCPUs를 예약 합니다.
1. 이전 단계에서 만든 리소스를 사용합니다.
    1. 만든 VM 이미지입니다.
    1. 컴퓨팅을 사용하도록 설정한 네트워크 인터페이스와 연결된 가상 스위치
    1. 가상 스위치와 연결된 서브넷

    그리고 다음 리소스 인라인을 생성하거나 지정합니다.
    1. Vm 이름, 지원 되는 HPN VM 크기, VM에 대 한 로그인 자격 증명을 선택 합니다. 
    1. 새 데이터 디스크를 만들거나 기존 데이터 디스크를 연결합니다.
    1. VM의 고정 IP 또는 동적 IP를 구성합니다. 고정 IP를 제공하는 경우 컴퓨팅을 사용할 수 있도록 설정된 네트워크 인터페이스의 서브넷 범위에서 사용 가능한 IP를 선택합니다.

    이전 리소스를 사용 하 여 HPN VM을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Portal를 통해 디바이스에서 VM 만들기 및 관리를 시작하기 전에 다음을 확인합니다.

- [1단계: Azure Stack Edge Pro GPU 디바이스 구성](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-device)의 설명대로 Azure Stack Edge Pro GPU 디바이스에서 네트워크 설정을 완료했습니다.

    1. 네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정했습니다. 이 네트워크 인터페이스 IP는 VM 배포를 위한 가상 스위치를 만드는 데 사용됩니다. 디바이스의 로컬 UI에서 **컴퓨팅** 페이지로 이동합니다. 가상 스위치를 만드는 데 사용할 네트워크 인터페이스를 선택합니다.

        > [!IMPORTANT] 
        > 컴퓨팅용 포트는 하나만 구성할 수 있습니다.

    1. 네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정합니다. 이 네트워크 인터페이스에 해당하는 가상 스위치를 Azure Stack Edge Pro GPU가 만들고 관리합니다.

-  만들려는 VM의 VM 이미지를 만드는 데 사용할 Windows 또는 Linux VHD에 액세스할 수 있습니다.

VM을 만드는 데 사용 되는 위의 필수 구성 요소 외에도 다음과 같은 필수 구성 요소를 HPN Vm에 맞게 구성 해야 합니다.

- Mellanox 인터페이스에서 HPN Vm에 대 한 vCPUs를 예약 합니다. 다음 단계를 수행합니다.

    1. [디바이스의 PowerShell 인터페이스에 연결합니다](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

    1. 장치에서 실행 되는 모든 Vm을 식별 합니다. 여기에는 Kubernetes Vm 또는 배포한 VM 워크 로드가 포함 됩니다.

        ```powershell
        get-vm -force
        ```
    1. 실행 중인 모든 Vm을 중지 합니다.
    
        ```powershell
        stop-vm -force
        ```
    1. `hostname`장치에 대 한를 가져옵니다. 장치 호스트 이름에 해당 하는 문자열을 반환 해야 합니다.

        ```powershell
        hostname
        ```
    1. HPN Vm에 대해 예약할 논리적 프로세서 인덱스를 가져옵니다.
    
        ```powershell
        Get-HcsNumaLpMapping -MapType HighPerformanceCapable -NodeName <Output of hostname command>
        ```
        출력의 예제는 다음과 같습니다.
    
        ```powershell    
        [dbe-1csphq2.microsoftdatabox.com]: PS>hostname
        1CSPHQ2
        [dbe-1csphq2.microsoftdatabox.com]: P> Get-HcsNumaLpMapping -MapType HighPerformanceCapable -NodeName 1CSPHQ2
         { Numa Node #0 : CPUs [4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19] }
         { Numa Node #1 : CPUs [24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39] }
        
        [dbe-1csphq2.microsoftdatabox.com]: PS>
        ```
    
    1. HPN Vm에 대 한 vCPUs를 예약 합니다. 여기에 예약 된 vCPUs 수는 HPN Vm에 할당 될 수 있는 사용 가능한 vCPUs를 결정 합니다. 각 HPN VM 크기에서 사용 하는 코어 수는 [지원 되는 HPN vm 크기](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes)를 참조 하세요. 장치에서 Mellanox 포트 5와 6은 NUMA 노드 0에 있습니다.
    
        ```powershell
        Set-HcsNumaLpMapping -CpusForHighPerfVmsCommaSeperated <Logical indexes from the Get-HcsNumaLpMapping cmdlet> -AssignAllCpusToRoot $false
        ```
    
        이 명령을 실행 하면 장치가 자동으로 다시 시작 됩니다.

        출력의 예제는 다음과 같습니다. 
    
        ```powershell
        [dbe-1csphq2.microsoftdatabox.com]: PS>Set-HcsNumaLpMapping -CpusForHighPerfVmsCommaSeperated "4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39" -AssignAllCpusToRoot $false
        Requested Configuration requires a reboot...
        Machine will reboot in some time. Please be patient.
        [dbe-1csphq2.microsoftdatabox.com]: PS>    
        ```
    
        > [!NOTE]
        > - 예제에 표시 된 NUMA 노드나 인덱스의 하위 집합 모두에서 모든 논리적 인덱스를 예약 하도록 선택할 수 있습니다. 인덱스의 하위 집합을 예약 하도록 선택 하는 경우 최상의 성능을 위해 Mellanox 네트워크 인터페이스가 연결 된 장치 노드에서 인덱스를 선택 합니다. Azure Stack Edge Pro GPU의 경우 Mellanox 네트워크 인터페이스가 포함 된 NUMA 노드가 #0 됩니다.  
        > - 논리적 인덱스 목록은 홀수와 짝수의 쌍을 이루는 시퀀스를 포함 해야 합니다. 예: ((4, 5) (6, 7) (10, 11)). 와 같은 또는 쌍과 같은 숫자 목록을 설정 하려고 `5,6,7` `4,6` 하면 작동 하지 않습니다.
        > - `Set-HcsNuma`VCPUs를 할당 하기 위해 연속적으로 두 명령을 사용 하면 구성이 다시 설정 됩니다. 또한 HPN VM을 배포한 경우 Set-HcsNuma cmdlet을 사용 하 여 Cpu를 해제 하지 마십시오.
  
    1. 장치가 다시 부팅을 완료할 때까지 기다립니다. 장치가 실행 되 면 새 PowerShell 세션을 엽니다. [디바이스의 PowerShell 인터페이스에 연결합니다](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
    
    1. VCPU 예약의 유효성을 검사 합니다.
    
        ```powershell
        Get-HcsNumaLpMapping -MapType MinRootAware -NodeName <Output of hostname command> 
        ```
        출력은 사용자가 설정한 인덱스를 표시 하면 안 됩니다. 출력에 설정 된 인덱스가 표시 되 면 `Set` 명령이 성공적으로 완료 되지 않은 것입니다. 명령을 다시 시도 하 고 문제가 지속 되 면 Microsoft 지원에 문의 하세요. 
    
        출력의 예제는 다음과 같습니다.
    
        ```powershell
        [dbe-1csphq2.microsoftdatabox.com]: PS> Get-HcsNumaLpMapping -MapType MinRootAware -NodeName 1CSPHQ2
        { Numa Node #0 : CPUs [0, 1, 2, 3] }
        { Numa Node #1 : CPUs [20, 21, 22, 23] }
        [dbe-1csphq2.microsoftdatabox.com]: PS>
        ```
    
    1. 이전 단계에서 중지 한 Vm을 다시 시작 합니다.
    
        ```powershell
        start-vm 
        ```
    <!-- Start-vm doesn't seem to work alone. Get-vm alone doesn't seem to return my running VM"VmId"--> 



## <a name="deploy-a-vm"></a>VM 배포

장치에서 HPN VM을 만들려면 다음 단계를 수행 합니다.

1. Azure Stack에 지 리소스의 Azure Portal에서 [VM 이미지를 추가](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#add-a-vm-image)합니다. 이 VM 이미지를 사용 하 여 다음 단계에서 VM을 만듭니다.

1. 이 구성 요구 사항을 사용 하 여 [VM 추가](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#add-a-vm) 의 모든 단계를 수행 합니다. 

    기본 사항 탭에서 [DSv2 또는 HPN에 대해 지원 되는 F 시리즈](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes)에서 VM 크기를 선택 합니다.

    ![Azure Stack Edge에서 가상 머신 추가 마법사의 기본 탭을 보여주는 스크린샷입니다. 기본 탭과 다음: 디스크 단추가 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-1.png)

1. VM 만들기의 나머지 단계를 완료 합니다. VM을 생성 하는 데 약 30 분이 걸립니다. 

    ![Azure Stack Edge에 대 한 가상 컴퓨터 추가 마법사의 검토 + 만들기 탭을 보여 주는 스크린샷 만들기 단추가 강조 표시됩니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-2.png)

1. VM이 성공적으로 생성된 후 **개요** 창에 새 VM이 표시됩니다. 새로 만든 VM을 선택하여 **가상 머신** 으로 이동합니다.

    ![Azure Stack Edge 디바이스에서 Virtual Machines 창을 보여주는 스크린샷입니다. Virtual Machines 레이블과 가상 머신 항목이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-3.png)

    VM을 선택하여 세부 정보를 확인합니다.

    ![Azure Stack Edge에서 가상 머신의 개요 창에 있는 세부 정보 탭을 보여주는 스크린샷입니다. 네트워킹의 VM 크기와 IP 주소가 강조 표시 됩니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-4.png)

    네트워크 인터페이스의 IP 주소를 사용하여 VM에 연결합니다.

    > [!NOTE]
    > 배포 전에 vCPUs가 HPN Vm에 예약 되어 있지 않으면 배포에 실패 하 고 `FabricVmPlacementErrorInsufficientNumaNodeCapacity` 오류가 발생 합니다.
                                                                                                                                                                         
## <a name="next-steps"></a>다음 단계

- [VM 배포 문제 해결](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [디바이스에서 VM 작업 모니터링](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [VM에서 CPU 및 메모리 모니터링](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)

