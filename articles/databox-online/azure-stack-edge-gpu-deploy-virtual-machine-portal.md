---
title: Azure Portal을 통해 Azure Stack Edge Pro GPU에 VM 배포
description: Azure Portal을 통해 Azure Stack Edge Pro GPU에서 VM을 만들고 관리하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 8089666388134e8443c515d86e91ae3ba5c32b58
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536907"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Azure Portal을 통해 Azure Stack Edge Pro GPU 디바이스에 VM 배포

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Portal, 템플릿, Azure PowerShell cmdlet을 사용하고 Azure CLI 또는 Python 스크립트를 통해 Azure Stack Edge Pro GPU 디바이스에서 VM(가상 머신)을 만들고 관리할 수 있습니다. 이 문서에서는 Azure Portal을 사용하여 Azure Stack Edge Pro GPU 디바이스에서 VM을 만들고 관리하는 방법을 설명합니다.

> [!IMPORTANT] 
> 클라우드에서 디바이스에 배포된 VM을 관리하는 사용자를 위해 다단계 인증을 사용하도록 설정하는 것이 좋습니다.
        
## <a name="vm-deployment-workflow"></a>VM 배포 워크플로

배포 워크플로에 대한 개략적인 요약은 다음과 같습니다.

1. Azure Stack Edge 디바이스에서 컴퓨팅용 네트워크 인터페이스를 사용하도록 설정합니다. 이 단계에서는 지정한 네트워크 인터페이스에서 가상 스위치를 만듭니다.
1. Azure Portal에서 VM의 클라우드 관리를 사용하도록 설정합니다.
1. Azure Storage Explorer를 사용하여 Azure Storage 계정에 VHD를 업로드합니다. 
1. 업로드된 VHD를 사용하여 디바이스에 VHD를 다운로드하고 VHD에서 VM 이미지를 만듭니다. 
1. 이전 단계에서 만든 리소스를 사용합니다.
    1. 만든 VM 이미지입니다.
    1. 컴퓨팅을 사용하도록 설정한 네트워크 인터페이스와 연결된 가상 스위치
    1. 가상 스위치와 연결된 서브넷

    그리고 다음 리소스 인라인을 생성하거나 지정합니다.
    1. VM 이름, VM에 지원되는 VM 크기, 로그인 자격 증명을 선택합니다. 
    1. 새 데이터 디스크를 만들거나 기존 데이터 디스크를 연결합니다.
    1. VM의 고정 IP 또는 동적 IP를 구성합니다. 고정 IP를 제공하는 경우 컴퓨팅을 사용할 수 있도록 설정된 네트워크 인터페이스의 서브넷 범위에서 사용 가능한 IP를 선택합니다.

    위의 리소스를 사용하여 VM을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Portal를 통해 디바이스에서 VM 만들기 및 관리를 시작하기 전에 다음을 확인합니다.

1. [1단계: Azure Stack Edge Pro GPU 디바이스 구성](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-device)의 설명대로 Azure Stack Edge Pro GPU 디바이스에서 네트워크 설정을 완료했습니다.

    1. 네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정했습니다. 이 네트워크 인터페이스 IP는 VM 배포를 위한 가상 스위치를 만드는 데 사용됩니다. 디바이스의 로컬 UI에서 **컴퓨팅** 페이지로 이동합니다. 가상 스위치를 만드는 데 사용할 네트워크 인터페이스를 선택합니다.

        > [!IMPORTANT] 
        > 컴퓨팅용 포트는 하나만 구성할 수 있습니다.

    1. 네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정합니다. 이 네트워크 인터페이스에 해당하는 가상 스위치를 Azure Stack Edge Pro GPU가 만들고 관리합니다.

1. 만들려는 VM의 VM 이미지를 만드는 데 사용할 Windows 또는 Linux VHD에 액세스할 수 있습니다.

## <a name="deploy-a-vm"></a>VM 배포

Azure Stack Edge Pro GPU 디바이스에서 VM을 만들려면 다음 단계를 수행합니다.

### <a name="add-a-vm-image"></a>VM 이미지 추가

1. Azure Storage 계정에 VHD 업로드. [업로드에 Storage Explorer 사용](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)의 단계를 수행합니다.

   VHD를 준비하는 방법에 대한 자세한 내용은 [Windows VHD에서 일반화된 이미지 준비](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)를 참조하세요.

   [VM 이미지 업로드 문제를 해결합니다](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md).

1. Azure Portal에서 디바이스의 Azure Stack Edge 리소스로 이동합니다. 그런 다음, **Edge 서비스** > **가상 머신** 으로 이동합니다.

    ![Edge 서비스 아래에서 가상 머신 옵션이 강조 표시된 Azure Stack Edge 디바이스의 개요 창을 보여주는 스크린샷입니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. **개요** 페이지에서 다음을 수행합니다. 가상 머신 클라우드 관리를 사용하도록 설정하려면 **사용** 을 선택합니다. 

    ![Azure Stack Edge 디바이스에 대한 가상 머신 보기의 개요 창 스크린샷입니다. VM 클라우드 관리를 사용하도록 설정하는 사용 단추가 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. 첫 번째 단계는 VM 이미지를 추가하는 것입니다. 이전 단계에서 이미 스토리지 계정에 VHD를 업로드했습니다. 이 VHD를 사용하여 VM 이미지를 만듭니다.

    스토리지 계정에서 VHD를 다운로드하고 이를 디바이스에 추가하려면 **+ 이미지 추가** 를 선택합니다. 다운로드 프로세스는 VHD 크기와 다운로드에 사용할 수 있는 인터넷 대역폭에 따라 몇 분이 걸립니다. 

    ![\+ 이미지 추가 단추가 강조 표시된 Virtual Machines의 개요 창 스크린샷입니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. **이미지 추가** 창에서 다음 필드 항목을 만듭니다. 그런 다음, **추가** 를 선택합니다.

    |필드  |설명  |
    |---------|---------|
    |스토리지 BLOB에서 다운로드    |VHD를 업로드한 스토리지 계정에서 스토리지 BLOB의 위치로 이동합니다.         |
    |다운로드 위치:    | VM을 배포하는 현재 디바이스로 자동으로 설정됩니다.        |
    |Edge 리소스 그룹  |이미지를 추가할 리소스 그룹을 선택합니다. |
    |다른 이름으로 이미지 저장      | 스토리지 계정에 업로드한 VHD에서 만들 VM 이미지의 이름입니다.        |
    |OS 유형     |VM 이미지를 만드는 데 사용할 VHD의 운영 체제로 Windows 또는 Linux를 선택합니다.         |
   

    ![추가 단추가 강조 표시된 가상 머신의 이미지 추가 페이지를 보여주는 스크린샷입니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. VHD가 다운로드되고 VM 이미지가 생성됩니다. 이미지를 만드는 데 몇 분 정도 걸립니다. VM 이미지가 완료되었다는 알림이 표시됩니다.<!--There's a fleeting notification that image creation is in progress, but I didn't see any notification that image creation completed successfully.-->

    ![Azure Stack Edge 디바이스에서 가상 머신 개요의 스크린샷입니다. 이미지 창에서 새로 만든 VM이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. VM 이미지가 생성되면 **이미지** 창의 이미지 목록에 추가됩니다.

    ![Azure Stack Edge 디바이스에서 가상 머신 보기의 이미지 창을 보여주는 스크린샷입니다. VM 이미지 항목이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    **배포** 창이 업데이트되어 배포 상태를 표시합니다.

    ![Azure Stack Edge 디바이스에서 가상 머신 보기의 배포 창을 보여주는 스크린샷입니다. 배포된 VM의 항목이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    새로 추가한 이미지는 **개요** 페이지에도 표시됩니다.

    ![Azure Stack Edge 디바이스에서 Virtual Machines의 개요 페이지를 보여주는 스크린샷입니다. 새로 추가된 VM 이미지가 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>VM 추가

VM 이미지를 만든 후에 VM을 만들려면 다음 단계를 수행합니다.

1. **가상 머신** 의 **개요** 페이지에서 **+ 가상 머신 추가** 를 선택합니다.

    ![Azure Stack Edge 디바이스에서 가상 머신의 개요 페이지를 보여주는 스크린샷입니다. \+ 가상 머신 추가 단추가 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. **기본 사항** 탭에서 다음 매개 변수를 입력합니다.

    |매개 변수 |설명  |
    |---------|---------|
    |가상 머신 이름     | 새 가상 머신의 이름을 입력합니다.        |
    |Edge 리소스 그룹     | VM과 연결된 모든 리소스를 위한 새 리소스 그룹을 만듭니다.        |
    |이미지     | 디바이스에서 사용할 수 있는 VM 이미지를 선택합니다.        |
    |크기     | [지원되는 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md)에서 선택합니다.<br>GPU VM에 대해 [NCasT4-v3-series의 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)를 선택합니다. |
    |사용자 이름     | 관리자의 기본 사용자 이름인 **azureuser** 를 사용하여 VM에 로그인합니다.        |
    |인증 유형    | SSH 퍼블릭 키 또는 사용자 정의 암호를 선택합니다.       |
    |SSH 퍼블릭 키 | **SSH 공개 키** 인증 유형을 선택할 때 표시됩니다. SSH 공개 키에 붙여 넣습니다. |
    |암호     | **암호** 인증 유형을 선택할 때 표시됩니다. 암호를 입력하여 VM에 로그인합니다. 암호는 12자 이상이어야 하며 정의된 [복잡성 요구 사항](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-)을 충족해야 합니다. |
    |암호 확인    | 암호를 다시 입력합니다.        |

    ![Azure Stack Edge에서 가상 머신 추가 마법사의 기본 탭을 보여주는 스크린샷입니다. 기본 탭과 다음: 디스크 단추가 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    완료되면 **다음: 디스크** 를 선택합니다.

1. **디스크** 탭에서 디스크를 VM에 연결합니다. 
    
    1. **새 디스크 만들기 및 연결** 또는 **기존 디스크 연결** 을 선택할 수 있습니다.

        ![Azure Stack Edge에서 가상 머신 추가 마법사의 디스크 탭을 보여주는 스크린샷입니다. 새 디스크 만들기 및 연결 옵션이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. **새 디스크 만들기 및 연결** 을 선택합니다. **새 디스크 만들기** 창에서 디스크 이름과 크기(GiB)를 입력합니다.

        ![Azure Stack Edge에서 “가상 머신 추가”의 “새 디스크 만들기” 화면을 보여주는 스크린샷입니다. 확인 단추가 강조 표시됩니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1. 이전 프로세스를 반복하여 디스크를 더 추가합니다. 디스크를 만들면 **디스크** 탭에 표시됩니다. **다음: 네트워킹** 을 선택합니다.

1. **네트워킹** 탭에서 VM의 네트워크 연결을 구성합니다.

    |매개 변수  |설명 |
    |---------|---------|
    |가상 네트워크    | 네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정한 경우 드롭다운 목록에서 Azure Stack Edge 디바이스에 만들어진 가상 스위치를 선택합니다.    |
    |서브넷     | 이 필드는 컴퓨팅을 사용하도록 설정한 네트워크 인터페이스와 연결된 서브넷으로 자동으로 채워집니다.         |
    |IP 주소     | VM의 고정 IP 또는 동적 IP를 제공합니다. 고정 IP는 지정된 서브넷 범위에서 사용 가능한 여유 IP여야 합니다.        |

    ![Azure Stack Edge에서 “가상 머신 추가”의 네트워킹 탭을 보여주는 스크린샷입니다. 네트워킹 탭과 다음: 고급 단추가 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    **다음: 고급** 을 선택합니다. **고급** 탭에서 확장을 선택하여 VM 배포 중 설치하고 `cloud-init` 스크립트를 지정하여 VM을 사용자 지정할 수 있습니다.

1. VM을 만들 때 VM에 확장을 설치하려면 **설치할 확장 선택** 을 선택합니다. 그런 다음, **확장 추가** 화면에서 확장을 선택합니다.

    VM 배포 중 GPU 확장을 설치하기 위한 세부 단계는 [GPU VM 배포](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)를 참조하세요.

    ![“가상 머신 추가”의 고급 탭에 GPU 확장을 추가하기 위한 2단계 설명입니다. 확장을 선택하고 추가하는 옵션이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-extension-01.png)

1. **고급** 탭에서 처음 부팅할 때 `cloud-init` 유틸리티를 사용하여 새 VM을 사용자 지정하려면 `cloud-init` 스크립트를 **사용자 지정 데이터 및 cloud init** 아래의 **사용자 지정 데이터** 상자에 붙여넣습니다. 

    `cloud-init` 사용에 대한 자세한 내용은 [Cloud-init 개요](../virtual-machines/linux/tutorial-automate-vm-deployment.md#cloud-init-overview)를 참조하세요.

    ![새 가상 머신 만들기 마법사의 고급 탭을 보여주는 스크린샷입니다. 사용자 지정 데이터 상자의 cloud init 스크립트가 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-advanced-tab-with-cloud-init-script.png)

    완료되면 **다음: 검토 + 만들기** 를 선택합니다.

1. **검토 + 만들기** 탭에서 VM 사양을 검토합니다. 그런 다음 **생성** 를 선택합니다.

    ![Azure Stack Edge에서 “가상 머신 추가” 마법사의 검토 및 만들기 탭을 보여주는 스크린샷입니다. 검토 및 만들기 탭이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. VM 생성이 시작되고 최대 20분이 걸릴 수 있습니다. **배포** 로 이동하여 VM 생성을 모니터링할 수 있습니다.

    ![Azure Stack Edge 디바이스에서 가상 머신 보기의 배포 창을 보여주는 스크린샷입니다. 배포된 VM의 항목이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

1. VM이 성공적으로 생성된 후 **개요** 창에 새 VM이 표시됩니다.

    ![Azure Stack Edge 디바이스에서 Virtual Machines의 개요 페이지를 보여주는 스크린샷입니다. 새 가상 머신이 목록에 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. 새로 만든 VM을 선택하여 **가상 머신** 으로 이동합니다.

    ![Azure Stack Edge 디바이스에서 Virtual Machines 창을 보여주는 스크린샷입니다. Virtual Machines 레이블과 가상 머신 항목이 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    VM을 선택하여 세부 정보를 확인합니다.

    ![Azure Stack Edge에서 가상 머신의 개요 창에 있는 세부 정보 탭을 보여주는 스크린샷입니다. 네트워킹의 개요 레이블 및 IP 주소가 강조 표시되어 있습니다.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

    네트워크 인터페이스의 IP 주소를 사용하여 VM에 연결합니다.

## <a name="connect-to-a-vm"></a>VM에 연결

Linux 또는 Windows VM을 만들었는지 여부에 따라 연결 단계는 다를 수 있습니다. Azure Portal를 통해 디바이스에 배포된 VM에 연결할 수 없습니다. Linux 또는 Windows VM에 연결하는 단계를 수행합니다.

### <a name="connect-to-a-linux-vm"></a>Linux VM에 연결

Linux VM에 연결하려면 다음 단계를 수행합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-a-windows-vm"></a>Windows VM에 연결

Windows VM에 연결하려면 다음 단계를 수행합니다.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>다음 단계

- [GPU VM 배포](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)
- [VM 배포 문제 해결](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [디바이스에서 VM 작업 모니터링](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [VM에서 CPU 및 메모리 모니터링](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)

