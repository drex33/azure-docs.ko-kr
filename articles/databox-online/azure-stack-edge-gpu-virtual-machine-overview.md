---
title: Azure Stack Edge 디바이스의 VM 개요
description: Azure Stack Edge 디바이스의 가상 머신을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: alkohli
ms.openlocfilehash: 73cb44dd3564f56f8edd5304a344a50f1e4b5c9d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536356"
---
# <a name="virtual-machines-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스의 가상 머신

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서는 Azure Stack Edge 디바이스에서 실행되는 VM(가상 머신)과 지원되는 VM 크기에 대한 개요를 제공하고, VM 이미지를 생성, 배포, 관리하는 다양한 방법을 요약합니다. 

## <a name="about-vms"></a>VM 정보

Azure Stack Edge 솔루션은 에지 컴퓨팅 워크로드를 배포하고 데이터가 생성되는 에지에서 빠른 실행 가능한 인사이트를 얻는 데 사용될 수 있는, Microsoft의 목적 내장 하드웨어를 서비스 디바이스로 제공합니다. 

사용자 환경 및 실행 중인 애플리케이션 유형에 따라 이러한 디바이스에 다음 에지 컴퓨팅 워크로드 중 하나를 배포할 수 있습니다. 

- **컨테이너화** - IoT Edge 또는 Kubernetes를 사용하여 컨테이너화 애플리케이션을 실행합니다.
- **컨테이너화** -비 컨테이너화 애플리케이션을 실행하려면 디바이스에 Windows 및 Linux 가상 머신을 모두 배포합니다. 

컴퓨팅 환경을 보다 세부적으로 제어해야 하는 경우 디바이스에 VM을 배포합니다. 개발 및 테스트에서 에지로 애플리케이션 실행에 이르기까지 다양한 방법으로 디바이스에서 VM을 사용할 수 있습니다.

## <a name="before-you-create-a-vm"></a>VM 생성 전

시작하기 전에 VM에 대한 다음 고려 사항을 검토합니다.

- 사용할 VM 크기
- 디바이스에 만들 수 있는 VM의 최대 수
- VM의 운영 체제
- 시작 이후의 VM 구성


### <a name="vm-size"></a>VM 크기

VM을 배포하려는 경우 VM 크기를 알고 있어야 합니다. 디바이스에서 앱 및 워크로드를 실행하는 데 사용할 수 있는 여러 가지 크기의 VM이 있습니다. 그런 다음 선택하는 크기는 처리 성능, 메모리 및 스토리지 용량 등의 요소를 결정합니다. 자세한 내용은 [지원되는 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes)를 참조하세요.

디바이스에 배포할 수 있는 VM의 크기와 수를 파악하기 위해 디바이스에서 사용 가능한 컴퓨팅 및 실행 중인 다른 워크로드에 대한 요인을 파악합니다. Kubernetes를 실행하는 경우 Kubernetes 마스터 및 작업자 VM에 대한 컴퓨팅 요구 사항도 고려해야 합니다.

|Kubernetes VM 유형|CPU 및 메모리 요구 사항|
|---------|---------|
|마스터 VM|4 코어, 4-GB RAM|
|작업자 VM|12 코어, 32-GB RAM|

디바이스에서 사용할 수 있는 컴퓨팅 및 메모리는 해당 디바이스 모델의 [컴퓨팅 및 메모리 사양](azure-stack-edge-gpu-technical-specifications-compliance.md#compute-and-memory-specifications)을 참조하세요. 

GPU 가상 머신의 경우 [NCasT4-v3 시리즈의 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)를 사용해야 합니다.


### <a name="vm-limits"></a>VM 한도

디바이스에서 최대 24개의 VM을 실행할 수 있습니다. 이는 워크로드를 배포할 때 고려해야 할 또 다른 요소입니다.

### <a name="operating-system-disks-and-images"></a>운영 체제 디스크 및 이미지

디바이스에서 고정 VHD(가상 하드 디스크) 형식을 사용하는 1세대 VM만 사용할 수 있습니다. VHD는 OS(컴퓨터 운영 체제) 및 데이터를 저장하는 데 사용됩니다. VHD는 OS 설치에 쓰이는 이미지에도 사용됩니다. 

VM 이미지를 만드는 데 사용하는 이미지를 일반화하거나 특수화할 수 있습니다. VM에 대한 이미지를 만들 때 이미지를 준비해야 합니다. 디바이스에서 VM 이미지를 준비하고 사용하는 다음의 다양한 방법을 참조하세요.

- [VHD에서 Windows 범용 이미지 준비](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [ISO에서 범용 이미지 준비](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
- [Azure VM에서 시작하는 사용자 지정 VM 이미지 만들기](azure-stack-edge-gpu-create-virtual-machine-image.md)
- [특수화된 이미지 사용](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md)

### <a name="extensions"></a>확장

VM이 프로비저닝될 때 스크립트를 실행하여 워크로드를 구성하는 데 도움이 되는 사용자 지정 스크립트 확장을 디바이스의 VM에서 사용할 수 있습니다.

자세한 내용은 [디바이스에서 실행되는 VM에 사용자 지정 스크립트 확장 배포](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md)를 참조하세요.

GPU VM이 프로비저닝될 때 GPU 드라이버를 설치하려는 경우에도 VM에 대한 GPU 확장을 사용할 수 있습니다. 자세한 내용은 [GPU VM 만들기](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms) 및 [GPU 확장 설치](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)를 참조하세요.

## <a name="create-a-vm"></a>VM 만들기

VM을 배포하려면 먼저 VM을 만드는 데 필요한 모든 리소스를 만들어야 합니다. VM을 만드는 데 사용된 방법에 관계 없이 다음 단계를 수행합니다. 

1. 디바이스의 로컬 Azure Resource Manager에 연결합니다. 
1. 디바이스의 기본 제공 구독 쿼리를 파악합니다.
1. VM 이미지를 가져옵니다.
    1. 기본 제공 구독에 리소스 그룹을 만듭니다. 리소스 그룹에는 VM 및 관련된 모든 리소스가 포함됩니다.
    2. VM 이미지를 만드는 데 사용될 VHD를 저장할 로컬 스토리지 계정을 디바이스에 만듭니다.
    3. Windows/Linux 소스 이미지를 스토리지 계정에 업로드하여 관리 디스크를 만듭니다.
    4. 관리 디스크를 사용하여 VM 이미지를 만듭니다.
1. 디바이스 포트에서 컴퓨팅을 사용하도록 설정하여 가상 스위치를 만듭니다.
    1. 그러면 컴퓨팅을 사용하도록 설정한 포트에 연결된 가상 스위치를 사용하여 가상 네트워크를 만듭니다.  
1. 이전에 만든 VM 이미지, 가상 네트워크 및 가상 네트워크 인터페이스를 사용하여 VM을 만들어서, 가상 네트워크 내에서 통신하고 VM에 원격으로 액세스하는 공용 IP 주소를 할당합니다. 필요에 따라 VM에 더 많은 저장소를 제공하기 위해 데이터 디스크를 포함합니다.
 
다음 다이어그램에 배포 워크플로가 나와 있습니다.

![VM 배포 워크플로의 다이어그램.](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

디바이스에 VM을 배포하는 방법에는 여러 가지가 있습니다. 사용자의 선택은 환경에 따라 달라집니다. 다음 표에는 디바이스에서 VM을 배포하는 다양한 방법이 요약되어 있습니다.

|방법|아티클|
|---------|---------|
|Azure Portal|[Azure Portal을 통해 디바이스에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)합니다.|
|템플릿|[템플릿을 통해 디바이스에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)|
|PowerShell|[Azure PowerShell cmdlet을 통해 디바이스에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)<br>[Azure PowerShell 스크립트를 통해 디바이스에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-powershell-script.md)|
|CLI/Python|[Azure CLI/Python을 통해 디바이스에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-cli-python.md)|
|GPU|[GPU를 사용하여 디바이스에 VM 배포](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)|


## <a name="manage-your-vm"></a>VM 관리

디바이스의 Azure Portal이나 PowerShell 인터페이스, 또는 직접 API를 통해 디바이스의 VM을 관리할 수 있습니다. 몇 가지 일반적인 관리 작업은 다음과 같습니다.

- VM에 대한 정보를 가져옵니다.
- VM에 연결하고 VM을 시작, 중지, 삭제합니다.
- 디스크, VM 크기, 네트워크 인터페이스, 가상 스위치 관리
- VM 디스크 백업

### <a name="get-information-about-your-vm"></a>사용자 VM에 대한 정보 가져오기

Azure Portal을 통해 VM에 대한 자세한 정보를 보려면 다음 단계를 수행합니다.

1. 디바이스의 Azure Stack Edge 리소스로 이동한 다음, **가상 머신 > 개요** 로 이동합니다. 
1. **개요** 페이지에서 **가상 머신** 으로 이동하여 관심있는 가상 머신을 선택합니다. 그러면 VM 세부 정보를 볼 수 있습니다. 

### <a name="connect-to-your-vm"></a>VM에 연결

VM에서 실행하는 OS에 따라 다음과 같이 VM에 연결할 수 있습니다. 

- [디바이스에서 Windows VM에 연결합니다](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#connect-to-windows-vm).
- [디바이스에서 Linux VM에 연결합니다](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#connect-to-linux-vm).

### <a name="start-stop-delete-vms"></a>VM 시작, 중지 및 삭제

[VM을 활성화](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#turn-on-the-vm)하고, [VM을 일시 중단하거나 종료](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm)할 수 있습니다. 마지막으로, 사용 후 [VM을 삭제](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#delete-the-vm)할 수 있습니다.

### <a name="manage-network-interfaces-virtual-switches"></a>네트워크 인터페이스, 가상 스위치 관리

VM에 대한 [네트워크 인터페이스를 추가, 수정 및 분리](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md)할 수 있습니다. 디바이스에 [새 가상 스위치를 만들어서](azure-stack-edge-gpu-create-virtual-switch-powershell.md) VM을 배포할 수도 있습니다. 

### <a name="manage-data-disks-vm-size"></a>데이터 디스크, VM 크기 관리

[기존 VM에 데이터 디스크](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#add-a-data-disk)를 추가하고, [기존 디스크를 연결](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#add-a-data-disk)하고, [데이터 디스크를 분리](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#detach-a-data-disk)하고, 마지막으로 Azure Portal을 통해 [VM 자체 크기를 조정](azure-stack-edge-gpu-manage-virtual-machine-resize-portal.md#resize-a-vm)할 수 있습니다.

### <a name="back-up-vms"></a>VM 백업

VM 디스크를 백업하고 디바이스 오류가 발생할 경우 백업에서 데이터를 복원할 수 있습니다. 자세한 내용은 [VM 디스크 백업](azure-stack-edge-gpu-back-up-virtual-machine-disks.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro GPU용 VM 크기 및 유형](azure-stack-edge-gpu-virtual-machine-sizes.md)에 대해 알아봅니다.


