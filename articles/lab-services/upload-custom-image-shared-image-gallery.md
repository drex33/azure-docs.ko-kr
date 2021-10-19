---
title: 실제 랩 환경에서 Windows 사용자 지정 이미지를 가져오는 방법
description: 실제 랩 환경에서 Windows 사용자 지정 이미지를 가져오는 방법을 설명합니다.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: 36a6c8f920ebada3d5cd4110e48fe8544e4c7ae3
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179016"
---
# <a name="bring-a-windows-custom-image-from-a-physical-lab-environment"></a>물리적 랩 환경에서 Windows 사용자 지정 이미지 가져오기

이 문서의 단계에서는 물리적 랩 환경에서 시작하는 사용자 지정 이미지를 가져오는 방법을 보여 줍니다.  이 접근 방식을 통해 Lab Services 내에서 VHD를 사용할 수 있도록 물리적 환경에서 VHD를 만들어 공유 이미지 갤러리로 가져옵니다.  사용자 지정 이미지를 만드는 데 이 방법을 사용하기 전에 [사용자 지정 이미지를 만들기 위한 권장 방법](approaches-for-custom-image-creation.md) 문서를 읽어 시나리오에 가장 적합한 방법을 결정합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하려면 학교의 Azure 구독에서 [Azure 관리 디스크](../virtual-machines/managed-disks-overview.md)를 만들 수 있는 권한이 있어야 합니다.

물리적 랩 환경에서 Lab Services로 이미지를 이동할 때 랩의 클래스에 필요한 소프트웨어만 포함하도록 각 이미지를 다시 구성해야 합니다.  자세한 내용은 [실제 랩에서 Azure Lab Services로 이동](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) 블로그 게시물을 참조하세요.

## <a name="prepare-a-custom-image-using-hyper-v-manager"></a>Hyper-V 관리자를 사용하여 사용자 지정 이미지 준비

다음 단계에서는 Hyper-V 관리자를 사용하여 Windows Hyper-V VM(가상 머신)에서 Windows 이미지를 만드는 방법을 보여 줍니다.

1. 이미지에서 만든 물리적 랩 환경에서 Hyper-V VM으로 시작합니다.  자세한 내용은 [Hyper-V에서 가상 머신을 만드는 방법](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)에 관한 문서를 참조하세요.
    - VM은 **1세대** VM으로 만들어야 합니다.
    - **기본 스위치** 네트워크 구성 옵션을 사용하여 VM이 인터넷에 연결되도록 허용합니다.
    - VM의 가상 디스크는 고정 크기 VHD여야 합니다.  디스크 크기는 128GB보다 크지 ‘않아야’ 합니다. VM을 만들 때 아래 이미지에 표시된 것처럼 디스크 크기를 입력합니다.

        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="가상 하드 디스크 연결":::

    디스크 크기가 128GB보다 큰 이미지는 Lab Services에서 지원되지 ‘않습니다’.

1. Hyper-V VM에 연결하고 다음 단계를 수행하여 [Azure를 위해 준비](../virtual-machines/windows/prepare-for-upload-vhd-image.md)합니다.
    1. [Azure에 대한 Windows 구성을 설정](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)합니다.
    1. [VM 연결을 보장하는 데 필요한 Windows 서비스를 확인](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)합니다.
    1. [원격 데스크톱 레지스트리 설정을 업데이트](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)합니다.
    1. [Windows 방화벽 규칙을 구성](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)합니다.
    1. [Windows 업데이트를 설치](../virtual-machines/windows/prepare-for-upload-vhd-image.md)합니다.
    1. [여기에 표시된 대로 Azure VM 에이전트 및 추가 구성을 설치](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations)합니다.

    특수화 또는 일반화 이미지를 공유 이미지 갤러리에 업로드하고 이를 사용하여 랩을 만들 수 있습니다.  위 단계에서는 특수화 이미지를 만듭니다. 일반화 이미지를 만들어야 하는 경우 [SysPrep을 실행](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep)해야 합니다.  

    머신별 정보 및 사용자 프로필을 유지 관리하려면 특수화 이미지를 만들어야 합니다.  일반화 이미지와 특수화 이미지 간 차이점에 관한 자세한 내용은 [일반화 및 특수화 이미지](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)를 참조하세요.

1. **Hyper-V** 는 기본적으로 **VHDX** 파일을 만들기 때문에 이 파일을 VHD 파일로 변환해야 합니다.
    1. **Hyper-V 관리자** -> **작업** -> **디스크 편집** 으로 이동합니다.
    1. 그런 다음, 디스크를 VHDX에서 VHD로 **변환** 합니다.  
     - 디스크 크기를 확장하는 경우 128GB를 초과하지 ‘않아야’ 합니다.
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="작업 선택":::

    자세한 내용은 [가상 디스크를 고정 크기 VHD로 변환](../virtual-machines/windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd)하는 방법을 보여 주는 문서를 참조하세요.

VHD 크기를 조정하고 VHDX로 변환하는 데 도움이 되도록 다음 PowerShell cmdlet을 사용할 수도 있습니다.

- [Resize-VHD](/powershell/module/hyper-v/resize-vhd)
- [Convert-VHD](/powershell/module/hyper-v/convert-vhd)

## <a name="upload-the-custom-image-to-a-shared-image-gallery"></a>공유 이미지 갤러리에 사용자 지정 이미지 업로드

1. VHD를 Azure에 업로드하여 관리 디스크를 만듭니다.
    1. [Azure에 VHD 업로드 또는 다른 지역으로 관리 디스크 복사](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)에 표시된 대로 Storage Explorer 또는 명령줄의 AzCopy를 사용할 수 있습니다.

    1. VHD를 업로드한 후 Azure Portal에서 볼 수 있는 관리 디스크가 생성됩니다.
    머신이 절전 모드 또는 잠금으로 전환되면 업로드 프로세스가 중단되고 실패할 수 있습니다.  또한 AzCopy가 완료된 후 디스크에 대한 SAS 액세스를 취소해야 합니다.  그렇지 않으면 디스크에서 이미지를 만들려고 하면 **'이미지 만들기' 작업이 '활성 업로드' 상태의 '디스크 이름' 디스크에서 지원되지 않습니다. 오류가 표시됩니다. 오류 코드: OperationNotAllowed**

    Azure Portal의 관리 디스크에 대한 **크기+성능** 탭에서는 디스크 크기를 변경할 수 있습니다. 앞에서 설명한 대로 크기는 128GB보다 크지 ‘않아야’ 합니다.

1. 공유 이미지 갤러리에서 이미지 정의 및 버전을 만듭니다.
    1. [이미지 정의를 만듭니다](../virtual-machines/image-version.md).  
     - **VM 세대** 에 대해 **1세대** 를 선택합니다.
     - **운영 체제 상태** 에 대해 **특수화** 또는 **일반화** 이미지를 만들지 여부를 선택합니다.

    이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](../virtual-machines/shared-image-galleries.md#image-definitions)를 참조하세요.

    기존 이미지 정의를 사용하고 사용자 지정 이미지의 새 버전을 만들도록 선택할 수도 있습니다.

1. [이미지 버전을 만듭니다](../virtual-machines/image-version.md).
    - **버전 번호** 속성은 *MajorVersion.MinorVersion.Patch* 형식을 사용합니다.   Lab Services를 사용하여 랩을 만들고 사용자 지정 이미지를 선택하면 최신 버전의 이미지가 자동으로 사용됩니다.  최신 버전은 MajorVersion, MinorVersion, Patch의 순서로 가장 큰 값을 기준으로 선택됩니다.
    - **원본** 의 경우 드롭다운 목록에서 **디스크 및/또는 스냅샷** 을 선택합니다.
    - **OS 디스크** 속성의 경우 이전 단계에서 만든 디스크를 선택합니다.

    이미지 정의에 대해 지정할 수 있는 값에 관한 자세한 내용은 [이미지 버전](../virtual-machines/shared-image-galleries.md#image-versions)을 참조하세요.

## <a name="create-a-lab"></a>랩 만들기

1. Lab Services에서 [랩을 만들고](tutorial-setup-classroom-lab.md) 공유 이미지 갤러리에서 사용자 지정 이미지를 선택합니다.

    OS가 원래 Hyper-V VM에 설치된 *후* 디스크를 확장한 경우 할당되지 않은 디스크 공간을 사용하려면 Windows에서 C 드라이브를 확장해야 할 수도 있습니다.
    - 랩의 템플릿 VM에 로그인하고 [기본 볼륨 확장](/windows-server/storage/disk-management/extend-a-basic-volume)에 표시된 것과 비슷한 단계를 수행합니다.

## <a name="next-steps"></a>다음 단계

- [공유 이미지 갤러리 개요](../virtual-machines/shared-image-galleries.md)
- [공유 이미지 갤러리 연결 또는 분리](how-to-attach-detach-shared-image-gallery.md)
- [공유 이미지 갤러리를 사용하는 방법](how-to-use-shared-image-gallery.md)
