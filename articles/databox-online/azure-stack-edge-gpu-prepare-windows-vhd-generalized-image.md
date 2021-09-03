---
title: Azure Stack Edge Pro GPU에 VM을 배포하기 위해 Windows VHD에서 범용 이미지 준비
description: Windows VHD 또는 VHDX에서 시작하는 범용 VM 이미지를 만드는 방법을 설명합니다. 이 범용 VM 이미지를 사용하여 Azure Stack Edge Pro GPU 디바이스에 가상 머신을 배포합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/18/2021
ms.author: alkohli
ms.openlocfilehash: e60f7e7195ef81b3bf17a85c5662824df945ef91
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968462"
---
# <a name="prepare-generalized-image-from-windows-vhd-to-deploy-vms-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU에 VM을 배포하기 위해 Windows VHD에서 범용 이미지 준비

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU 디바이스에 VM을 배포하려면 VM을 만드는 데 사용할 수 있는 사용자 지정 VM 이미지를 만들 수 있어야 합니다. 이 문서에서는 Windows Stack Edge Pro GPU 디바이스에 가상 머신을 배포하는 데 사용할 수 있는 Windows VHD 또는 VHDX에서 범용 이미지를 준비하는 방법을 설명합니다.

ISO를 사용하여 범용 VM 이미지를 준비하려면 [Azure Stack Edge Pro GPU에 VM을 배포하기 위해 ISO에서 범용 이미지 준비](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)를 참조하세요.

## <a name="about-vm-images"></a>VM 이미지 정보

Windows VHD 또는 VHDX를 사용하여 *특수화된* 이미지 또는 *일반화된* 이미지를 만들 수 있습니다. 다음 표에는 *특수화된* 이미지와 *일반화된* 이미지의 주요 차이점이 요약되어 있습니다.

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>워크플로

기존 가상 머신의 VHD 또는 VHDX에서 시작하여 범용 이미지로 사용할 Windows VHD를 준비하는 상위 수준 워크플로에는 다음 단계가 있습니다.

1. Windows VHD에서 원본 VM을 준비합니다.
   1. 원본 VHD 또는 VHDX를 고정 크기의 VHD로 변환합니다.
   1. 해당 VHD를 사용하여 새 가상 머신을 만듭니다.<!--Can this procedure be generalized and moved to an include file?-->
1. VM을 시작하고 Windows 운영 체제를 설치합니다.
1. *sysprep* 유틸리티를 사용하여 VHD를 일반화합니다.
1. 일반화된 이미지를 Blob Storage에 복사합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Stack Edge Pro GPU 디바이스에서 범용 이미지로 사용할 Windows VHD를 준비하기 전에 다음을 확인합니다.

- 지원되는 버전의 Windows를 포함하는 VHD 또는 VHDX가 있습니다. 
- Hyper-V 관리자가 설치된 Windows 클라이언트에 액세스할 수 있습니다. 
- Azure Blob Storage 계정에 액세스하여 VHD를 준비한 후 저장할 수 있습니다.

## <a name="prepare-source-vm-from-windows-vhd"></a>Windows VHD에서 원본 VM 준비

VM 원본이 Windows VHD 또는 VHDX인 경우 먼저 Windows VHD를 고정 크기 VHD로 변환해야 합니다. 고정 크기 VHD를 사용하여 새 가상 머신을 만듭니다.

> [!IMPORTANT]
> 이 절차에서는 원본 VHD가 사용자 지정 구성 및 설정으로 구성된 경우를 다루지 않습니다. 예를 들어 사용자 지정 방화벽 규칙 또는 프록시 설정이 포함된 VHD를 일반화하려면 추가 작업이 필요할 수 있습니다. 이러한 추가 작업에 대한 자세한 내용은 [Azure에 업로드할 WINDOWS VHD 준비-Azure Virtual Machines](../virtual-machines/windows/prepare-for-upload-vhd-image.md)를 참조하세요.

#### <a name="convert-source-vhd-to-a-fixed-size-vhd"></a>원본 VHD를 고정 크기 VHD로 변환

디바이스의 경우 VM 이미지를 만들기 위한 고정 크기의 VHD가 필요합니다. 원본 Windows VHD 또는 VHDX를 고정 VHD로 변환해야 합니다. 

다음 단계를 수행합니다.

1. 클라이언트 시스템에서 Hyper-V 관리자를 엽니다. **디스크 편집** 으로 이동.

    ![Hyper-V 관리자를 엽니다](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. **시작하기 전에** 페이지에서 **다음>** 을 선택합니다.

1. **가상 하드 디스크 찾기** 페이지에서 변환할 원본 Windows VHD 또는 VHDX의 위치로 이동합니다. **다음>** 을 선택합니다.

    ![가상 하드 디스크 찾기 페이지](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. **작업 선택** 페이지에서 **변환** 을 선택하고 **다음>** 을 선택합니다.

    ![작업 페이지 선택](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. **디스크 형식 선택** 페이지에서 **VHD** 형식을 선택하고 **다음>** 을 선택합니다.

   ![디스크 형식 선택 페이지](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)

1. **디스크 유형 선택** 페이지에서 **고정 크기** 를 선택하고 **다음>** 를 선택합니다.

   ![디스크 유형 선택 페이지](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)

1. **디스크 구성** 페이지에서 위치로 이동하고 고정 크기의 VHD 디스크 이름을 지정합니다. **다음>** 을 선택합니다.

   ![디스크 구성 페이지](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)

1. 요약을 검토하고 **마침** 을 선택합니다. VHD 또는 VHDX 변환에는 몇 분이 걸립니다. 변환 시간은 원본 디스크의 크기에 따라 달라집니다.

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
이 문서의 모든 후속 단계에 이 고정 크기 VHD를 사용합니다.

#### <a name="create-hyper-v-vm-from-the-fixed-size-vhd"></a>고정 크기 VHD에서 Hyper-V VM 만들기

1. 범위 창에서 **Hyper-V 관리자** 의 시스템 노드를 마우스 오른쪽 단추로 클릭하여 상황에 맞는 메뉴를 연 다음 **신규** > **가상 머신** 을 선택합니다.

    ![범위 창에서 새 가상 머신 선택](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. 새 가상 머신 마법사의 **시작하기 전에** 페이지에서 **다음** 을 선택합니다.

1. **이름 및 위치 지정** 페이지에서 가상 머신의 **이름** 및 **위치** 를 입력합니다. **다음** 을 선택합니다.

    ![VM의 이름 및 위치 지정](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. **세대 지정** 페이지에서 .vhd 디바이스 이미지 유형으로 **세대 1** 를 선택하고 **다음** 을 선택합니다.    

    ![세대 지정](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. 원하는 메모리와 네트워킹 구성을 할당합니다.

1. **가상 하드 디스크 연결** 페이지에서 **기존 가상 하드 디스크 사용** 을 선택하고 가상 이전에 만든 Windows 고정 VHD의 위치를 지정한 후 **다음** 을 선택합니다.

    ![가상 하드 디스크 연결 페이지](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. **요약** 을 검토하고 **마침** 을 선택하여 가상 머신을 만듭니다.

가상 머신을 만드는 데 몇 분 정도 걸립니다.

VM은 클라이언트 시스템의 가상 머신 목록에 표시됩니다.

## <a name="start-vm-and-install-operating-system"></a>VM 시작 및 운영 체제 설치

가상 컴퓨터 빌드를 완료하려면 가상 컴퓨터를 시작하고 운영 체제 설치를 진행해야 합니다.

[!INCLUDE [Connect to Hyper-V VM](../../includes/azure-stack-edge-connect-to-hyperv-vm.md)]

VM에 연결한 후 컴퓨터 설치 마법사를 완료하고 VM에 로그인합니다.<!--It's not clear what they are doing here. Where does the Machine setup wizard come in?-->

## <a name="generalize-the-vhd"></a>VHD 일반화

*Sysprep* 유틸리티를 사용하여 VHD를 일반화합니다. 

[!INCLUDE [Generalize the VHD](../../includes/azure-stack-edge-generalize-vhd.md)]

이제 VHD를 사용하여 Azure Stack Edge Pro GPU에서 사용하기 위해 범용 이미지를 만들 수 있습니다.

## <a name="upload-generalized-vhd-to-azure-blob-storage"></a>Azure Blob Storage에 일반화된 VHD 업로드

[!INCLUDE [Upload VHD to Blob storage](../../includes/azure-stack-edge-upload-vhd-to-blob-storage.md)]

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->

## <a name="next-steps"></a>다음 단계

배포의 특성에 따라 다음 절차 중 하나를 선택할 수 있습니다.

- [Azure Portal를 통해 일반화된 이미지에서 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Azure Stack Edge Pro GPU에 VM을 배포하기 위해 ISO에서 범용 이미지 준비](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
- [특수 이미지 준비 및 이미지를 사용하여 VM 배포](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md) 
