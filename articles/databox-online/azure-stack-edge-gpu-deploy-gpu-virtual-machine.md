---
title: Azure Stack Edge Pro GPU 디바이스에 GPU VM 배포
description: Azure Portal을 통해 또는 템플릿을 사용하여 Azure Stack Edge Pro GPU에 GPU VM(가상 머신)을 만들고 배포하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 958c87a78551555d2994c37e2b72c75cb989a834
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536364"
---
# <a name="deploy-gpu-vms-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에 GPU VM 배포

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

이 문서에서는 Azure Portal에서 또는 Azure Resource Manager 템플릿을 사용하여 GPU VM을 만드는 방법을 설명합니다.

Azure Portal을 사용하여 단일 GPU VM을 신속하게 배포합니다. VM을 만드는 동안 또는 이후에 GPU 확장을 설치할 수 있습니다. 또는 Azure Resource Manager 템플릿을 사용하여 여러 GPU VM을 효율적으로 배포하고 관리합니다.

## <a name="create-gpu-vms"></a>GPU VM 만들기

포털을 통해 또는 Azure Resource Manager 템플릿을 사용하여 GPU VM을 배포할 수 있습니다.

GPU VM에 지원되는 운영 체제, 드라이버 및 VM 크기의 목록은 [GPU 가상 머신이란?](azure-stack-edge-gpu-overview-gpu-virtual-machines.md)을 참조하세요. 배포 고려 사항은 [GPU VM 및 Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes)를 참조하세요.


> [!IMPORTANT]
> 디바이스에서 Kubernetes를 실행하는 경우 GPU VM을 배포하기 전에 Kubernetes를 구성하지 마세요. Kubernetes를 먼저 구성하면 사용 가능한 모든 GPU 리소스가 클레임되고 GPU VM을 만들지 못하게 됩니다. 1-GPU 및 2-GPU 디바이스에 대한 Kubernetes 배포 고려 사항은 [GPU VM 및 Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes)를 참조하세요.

### <a name="portal"></a>[포털](#tab/portal)

Azure Portal을 통해 GPU VM을 디바이스에 배포할 경우 다음 단계를 수행합니다.

1. GPU VM을 만들려면 다음 구성 요구 사항과 함께 [Azure Portal을 사용하여 Azure Stack Edge에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)의 모든 단계를 수행합니다.

    - **기본** 탭에서 [NCasT4-v3-series에서 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)를 선택합니다.

       ![Azure Stack Edge에서 "가상 머신 추가" 기본 탭의 스크린샷. GPU VM에 지원되는 VM 크기가 있는 크기 옵션이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/basics-vm-size-for-gpu.png)

    - 배포 중에 GPU 확장을 설치하려면 **고급** 탭에서 **설치할 확장 선택** 을 선택합니다. 그런 다음, 설치할 GPU 확장을 선택합니다. [NCasT4-v3-series에서 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)가 있는 가상 머신에서만 GPU 확장을 사용할 수 있습니다.
        
        > [!NOTE]
        > Red Hat 이미지를 사용하는 경우 VM을 배포한 후에 GPU 확장을 설치해야 합니다. [GPU 확장 설치](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)의 단계를 수행합니다.
    
       ![가상 머신 추가의 고급 탭에서 GPU 확장을 추가하는 단계를 보여주는 그림. 확장을 선택하고 추가하는 옵션이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-01.png)

       **고급** 탭에 선택한 확장이 표시됩니다.

       ![가상 머신 추가 고급 탭의 스크린샷. 설치된 GPU 확장이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-02.png)

1. GPU VM이 성공적으로 생성되면 Azure Portal의 Azure Stack Edge 리소스 가상 머신 목록에 이 VM이 표시됩니다.

    ![새로 만든 GPU VM이 강조 표시된 Azure Stack Edge의 "가상 머신" 보기 스크린샷.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machines-01.png)

    VM을 선택하고 세부 정보로 드릴다운합니다. GPU 확장이 **성공** 상태인지 확인합니다.

    ![Azure Stack Edge VM의 세부 정보 창 스크린샷. 설치된 확장(설치된 GPU 확장 포함)이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/vm-details-extension-installed.png)


### <a name="templates"></a>[템플릿](#tab/templates)

Azure Resource Manager 템플릿을 사용하여 디바이스에 GPU VM을 배포할 경우 다음 단계를 수행합니다.

1. 클라이언트 시스템에 [VM 템플릿 및 매개 변수 파일을 다운로드](https://aka.ms/ase-vm-templates)합니다. 작업 디렉터리로 사용할 디렉터리에 압축을 풉니다.

1. Azure Stack Edge 디바이스에서 VM을 배포하려면 먼저 Azure PowerShell에 대한 Azure Resource Manager를 통해 디바이스에 연결하도록 클라이언트를 구성해야 합니다. 자세한 지침은 [Azure Stack Edge 디바이스에서 Azure Resource Manager에 연결](azure-stack-edge-gpu-connect-resource-manager.md)을 참조하세요.

1. GPU VM을 만들려면 다음 구성 요구 사항과 함께 [템플릿을 사용하여 Azure Stack Edge에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)의 모든 단계를 수행합니다. 
            
    - GPU VM 크기를 지정할 경우 GPU VM에 지원되는 `CreateVM.parameters.json`에서 NCasT4-v3-series를 사용해야 합니다. 자세한 내용은 [GPU VM에 지원되는 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)를 참조하세요.

       ```json
           "vmSize": {
         "value": "Standard_NC4as_T4_v3"
       },
       ```

    GPU VM이 성공적으로 생성되면 Azure Portal의 Azure Stack Edge 리소스 가상 머신 목록에 이 VM이 표시됩니다.

    ![Azure Stack Edge의 가상 머신 보기 스크린샷. 새로 만든 GPU VM이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machines-01.png)

1. VM을 선택하고 세부 정보로 드릴다운합니다. VM에 할당된 IP 주소를 복사합니다.

    ![Azure Stack Edge 가상 머신의 세부 정보 창 스크린샷. 네트워킹 아래에 IP 주소가 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-of-virtual-machine.png)

VM을 만든 후 [확장 템플릿을 사용하여 GPU 확장을 배포](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)할 수 있습니다.

---

> [!NOTE]
> 디바이스 소프트웨어 버전을 2012 이후 버전으로 업데이트할 경우 수동으로 GPU VM을 중지해야 합니다.

## <a name="install-gpu-extension-after-deployment"></a>배포 후 GPU 확장 설치

Azure N 시리즈 VM의 GPU 기능을 최대한 활용하려면 Nvidia GPU 드라이버를 설치해야 합니다. Azure Portal에서 VM 배포 중에 또는 이후에 GPU 확장을 설치할 수 있습니다. 템플릿을 사용하는 경우 VM을 만든 후에 GPU 확장을 설치합니다.

---

### <a name="portal"></a>[포털](#tab/portal)

VM을 만들 때 GPU 확장을 설치하지 않았으면 다음 단계를 수행하여 배포된 VM에 설치합니다.

1. GPU 확장을 추가하려는 가상 머신으로 이동합니다.

    ![VM 목록에서 가상 머신이 강조 표시된 Azure Stack Edge 디바이스의 "가상 머신" 보기 스크린샷.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-after-deployment-01.png)
  
1. **세부 정보** 에서 **+확장 추가** 를 선택합니다. 그런 다음, 설치할 GPU 확장을 선택합니다.

    [NCasT4-v3-series에서 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)가 있는 가상 머신에서만 GPU 확장을 사용할 수 있습니다. 원하는 경우 [배포 후에 GPU 확장을 설치](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#install-gpu-extension-after-deployment)할 수 있습니다.

![가상 머신 "세부 정보" 창에서 "+ 확장 추가" 단추를 사용하여 Azure Stack Edge 디바이스의 VM에 GPU 확장을 추가하는 2단계를 보여주는 그림.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-after-deployment-02.png)

> [!Note]
> 포털을 통해 GPU 확장을 제거할 수 없습니다. 대신 Azure PowerShell에서 [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension?view=azurermps-6.13.0&preserve-view=true) cmdlet를 사용합니다. 자세한 내용은 [GPU 확장 제거](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md#remove-gpu-extension)를 참조하세요.

### <a name="templates"></a>[템플릿](#tab/templates)

템플릿을 사용하여 GPU VM을 만든 경우 배포한 후에 GPU 확장을 설치합니다. 템플릿을 사용하여 GPU 확장을 Windows 가상 머신이나 Linux 가상 머신에 배포하는 자세한 단계는 [GPU 확장 설치](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)를 참조하세요.

---

## <a name="next-steps"></a>다음 단계

- [VM 배포 문제 해결](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [GPU 확장 문제 해결](azure-stack-edge-gpu-troubleshoot-virtual-machine-gpu-extension-installation.md)
- [디바이스에서 VM 작업 모니터링](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [VM에서 CPU 및 메모리 모니터링](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)
