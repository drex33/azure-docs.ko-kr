---
title: Azure Stack Edge Pro GPU에 VM을 배포하기 위해 ISO에서 범용 이미지를 준비합니다
description: ISO에서 시작하여 범용 Windows VM 이미지를 생성하는 방법을 설명합니다. 이 범용 이미지를 사용하여 Azure Stack Edge Pro GPU 장치에 가상 머신을 배포합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2021
ms.author: alkohli
ms.openlocfilehash: 22f9c86342428d3d011eb787745f74ac6ea026f8
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970416"
---
# <a name="prepare-generalized-image-from-iso-to-deploy-vms-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU에 VM을 배포하기 위해 ISO에서 범용 이미지를 준비합니다 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU 디바이스에 VM을 배포하려면, Azure에서 VM을 생성하는 데 사용할 수 있는 사용자 지정 가상 머신(VM) 이미지를 생성할 수 있어야 합니다. 이 문서에서는 ISO 설치 미디어를 사용하여 Windows VM 이미지를 준비한 다음 해당 이미지를 범용화하여 Azure Stack Edge Pro GPU 장치에 여러 개의 새 VM을 배포할 수 있도록 하는 방법을 설명합니다.

Windows VHD 또는 VHDX에서 생성한 범용 이미지를 준비하려면, [WINDOWS VHD에서 범용 이미지를 준비하여 Azure Stack Edge PRO GPU에 VM 배포](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)를 참조하세요.

## <a name="about-vm-images"></a>VM 이미지 정보

Windows VHD 또는 VHDX를 사용하여 *특수화된* 이미지 또는 *일반화된* 이미지를 만들 수 있습니다. 다음 표에는 *특수화된* 이미지와 *일반화된* 이미지의 주요 차이점이 요약되어 있습니다.

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>워크플로 

ISO를 사용하여 범용 Windows VHD를 생성하는 개략적인 워크플로는 다음과 같습니다.

1. ISO 이미지를 사용하여 원본 VM 준비:
   1. Hyper-v Manager에서 비어 있는 신규 고정 크기 VHD를 생성합니다.
   1. 해당 VHD를 사용하여 새 가상 머신을 생성합니다.
   1. 새 VM의 DVD 드라이브에 ISO 이미지를 탑재합니다.
1. VM을 시작하고 Windows 운영 체제를 설치합니다.
1. *sysprep* 유틸리티를 사용하여 VHD를 일반화합니다.
1. 범용 이미지를 Azure Blob Storage에 복사합니다.

## <a name="prerequisites"></a>필수 구성 요소

ISO 이미지를 사용하여 범용 Windows VHD를 생성하려면 먼저 다음을 확인해야 합니다.

- 범용 VHD로 전환하려는 지원되는 Windows 버전용 ISO 이미지를 가지고 있습니다. [Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/)에서 Windows ISO 이미지를 다운로드할 수 있습니다.

- Hyper-V 관리자가 설치된 Windows 클라이언트에 액세스할 수 있습니다.

- Azure Blob Storage 계정에 액세스하여 VHD를 준비한 후 저장할 수 있습니다.

## <a name="prepare-source-vm-using-an-iso"></a>ISO를 사용하여 원본 VM 준비

ISO 이미지를 사용하여 VM 이미지에 운영 체제를 설치하는 경우, Hyper-v Manager에서 비어 있는 고정 크기의 VHD를 생성하는 것부터 시작합니다. 해당 VHD를 사용하여 새 가상 머신을 생성합니다. 그런 다음 ISO 이미지를 VM에 연결합니다.

#### <a name="create-new-vhd-in-hyper-v-manager"></a>Hyper-v Manager에서 새 VHD 생성하기

첫 번째 단계는 Hyper-v Manager에서 새 가상 머신의 원본 VHD가 될 새 1세대 VHD를 생성하는 것입니다.

VHD를 생성하려면 다음 단계를 따릅니다.

1. 클라이언트 시스템에서 Hyper-V 관리자를 엽니다. **작업** 메뉴에서 **신규** 를 선택하고 **하드 디스크** 를 선택합니다.

   ![신규를 선택하고 하드 디스크를 선택합니다](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-01.png)

1. **디스크 형식 선택** 에서 **VHD** 를 선택합니다. 이후 **다음 >** 을 선택합니다. 

   ![디스크 형식으로 VHD를 선택합니다](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-02.png)

2. **디스크 유형 선택** 에서 **고정 크기** 를 선택합니다. 이후 **다음 >** 을 선택합니다.

   ![디스크 유형으로 고정 크기를 선택합니다](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-03.png)

3. **이름 및 위치 지정** 에서 새 VHD의 이름과 위치를 입력합니다. 이후 **다음 >** 을 선택합니다.

   ![VHD의 이름과 위치를 입력합니다](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-04.png)

4. **디스크 구성** 에서 **새 빈 가상 하드 디스크 생성하기** 를 선택하고 생성하려는 디스크의 크기(일반적으로 Windows Server의 경우 20GB 이상)를 입력합니다. 이후 **다음 >** 을 선택합니다.

   ![비어 있는 새 가상 하드 디스크를 생성 및 크기 지정 설정](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-05.png)

5. **요약** 에서 선택 항목을 검토하고 **마침** 을 선택하여 새 VHD를 생성합니다. 프로세스는 생성된 VHD의 크기에 따라 5분 이상 소요됩니다.

   ![VHD 설정 요약](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-06.png)

#### <a name="create-hyper-v-vm-from-vhd"></a>VHD에서 Hyper-V VM 생성하기

이제 방금 생성한 VHD를 사용하여 새 가상 머신을 생성합니다.

새 가상 머신을 생성하려면 다음 단계를 수행합니다.

1. Windows 클라이언트에서 Hyper-V Manager를 엽니다.

2. **작업** 창에서 **신규**, **가상 머신** 을 차례로 선택합니다.

   ![오른쪽의 메뉴에서 신규를 선택하고 가상 머신을 선택합니다.](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-07.png)

3. 새 가상 머신 마법사에서 VM의 이름과 위치를 지정합니다.

   ![새 가상 머신 마법사, 이름 및 위치 지정](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-08.png)

4. **세대 지정** 에서 **1세대** 를 선택합니다. 이후 **다음 >** 을 선택합니다.

   ![새 가상 머신 마법사, 만들 가상 머신의 세대 선택](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-09.png)

5. **메모리 할당** 에서 원하는 메모리를 가상 머신에 할당합니다. 이후 **다음 >** 을 선택합니다.

   ![새 가상 머신 마법사, 메모리 할당](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-10.png)

6. **네트워킹 구성** 에서 네트워크 구성을 입력합니다. 이후 **다음 >** 을 선택합니다.

   ![새 가상 머신 마법사, 네트워킹 구성](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-11.png)

7. **가상 하드 디스크 연결** 에서 **기존 가상 하드 디스크 사용** 을 선택하고 이전 절차에서 만든 고정 VHD를 찾습니다. 이후 **다음 >** 을 선택합니다. 

   ![새 가상 머신 마법사, 기존 가상 하드 디스크를 원본으로 사용 선택](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-12.png)

8. 요약을 검토하고 **마침** 을 선택하여 가상 머신을 생성합니다. 

#### <a name="mount-iso-image-on-dvd-drive-of-vm"></a>VM의 DVD 드라이브에 ISO 이미지 탑재

새 가상 머신을 생성한 후, 다음 단계에 따라 가상 머신의 DVD 드라이브에 ISO 이미지를 탑재합니다.

1. Hyper-V Manager에서 방금 생성한 VM을 선택하고 **설정** 을 선택합니다.
 
   ![Hyper-V Manager에서 가상 머신에 대한 설정을 엽니다](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-13.png)

2. **BIOS** 에서 **CD** 가 **시작 순서** 목록의 맨 위에 있는지 확인합니다.

   ![BIOS 설정에서 시작 순서 아래의 첫 번째 항목은 CD여야 합니다](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-14.png)


3. **DVD 드라이브** 에서 **이미지 파일** 을 선택하고 ISO 이미지를 찾습니다.  

   ![DVD 드라이브 설정에서 VHD의 이미지 파일을 선택합니다](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-15.png)

4. **확인** 을 선택하여 VM 설정을 저장합니다.

## <a name="start-vm-and-complete-os-installation"></a>VM 시작 및 OS 설치 완료

가상 머신 빌드를 완료하려면 가상 머신을 시작하고 운영 체제 설치를 진행해야 합니다.

[!INCLUDE [Connect to Hyper-V VM](../../includes/azure-stack-edge-connect-to-hyperv-vm.md)]

> [!NOTE]
> 가상 머신에 Windows Server 2019 Standard 운영 체제를 설치한 경우, [VHD 범용화](#generalize-the-vhd) 전에 **BIOS** 설정을 **IDE** 로 변경해야 합니다. 

## <a name="generalize-the-vhd"></a>VHD 일반화

*Sysprep* 유틸리티를 사용하여 VHD를 일반화합니다.

1. Windows Server 2019 Standard VM을 범용화하는 경우, VHD를 범용화하기 전에 가상 머신에 대한 첫 번째 **BIOS** 설정을 IDE로 설정합니다.

    1. Hyper-V Manager에서 VM을 선택하고 **설정** 을 선택합니다.
 
       ![Hyper-V Manager에서 선택한 VM에 대한 설정을 여는 방법을 보여주는 스크린샷](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-01.png)

     1. **BIOS** 에서 **IDE** 가 **시작 순서** 목록의 맨 위에 있는지 확인합니다. **확인** 을 선택하여 설정을 저장합니다.

        ![Hyper-V Manager에서 VM에 대한 BIOS 설정의 시작 순서 맨 위에 있는 IDE를 보여주는 스크린샷](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-02.png)

1. VM 내에서 명령 프롬프트를 엽니다.

1. 다음 명령을 실행하여 VHD를 일반화합니다. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    
    자세한 내용은 [Sysprep(시스템 준비) 개요](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)를 참조하세요.

1.  명령이 완료되면 VM이 종료됩니다. **VM을 다시 시작하지 마세요**.

<!--[!INCLUDE [Generalize the VHD](../../includes/azure-stack-edge-generalize-vhd.md)]-->

이제 VHD를 사용하여 Azure Stack Edge Pro GPU에서 사용하기 위해 범용 이미지를 생성할 수 있습니다.

## <a name="upload-generalized-vhd-to-azure-blob-storage"></a>Azure Blob Storage에 범용 VHD 업로드

[!INCLUDE [Upload VHD to Blob storage](../../includes/azure-stack-edge-upload-vhd-to-blob-storage.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Portal를 통해 일반화된 이미지에서 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Azure Stack Edge Pro GPU에 VM을 배포하기 위해 Windows VHD에서 일반화된 이미지 준비](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [특수 이미지 준비 및 이미지를 사용하여 VM 배포](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md) 
