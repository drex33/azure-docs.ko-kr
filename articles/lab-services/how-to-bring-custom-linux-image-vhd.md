---
title: Azure Lab Services - 물리적 랩 환경에서 Linux 사용자 지정 이미지를 가져오는 방법
description: 물리적 랩 환경에서 Linux 사용자 지정 이미지를 가져오는 방법을 설명합니다.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: 9a8591d383ac5230085bc83d1d791e9de830a99e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771368"
---
# <a name="bring-a-linux-custom-image-from-your-physical-lab-environment"></a>물리적 랩 환경에서 Linux 사용자 지정 이미지 가져오기

이 단계에서는 물리적 랩 환경에서 시작하는 사용자 지정 이미지를 가져오는 방법을 보여줍니다. 이 방법을 통해 Azure Lab Services 내에서 VHD를 사용할 수 있도록 물리적 환경에서 VHD를 만들어 공유 이미지 갤러리로 가져옵니다. 사용자 지정 이미지를 만드는 데 이 방법을 사용하기 전에 [사용자 지정 이미지를 만들기 위한 권장 방법](approaches-for-custom-image-creation.md)을 참조하여 시나리오에 가장 적합한 방법을 결정합니다.

Azure는 다양한 [배포판 및 버전](../virtual-machines/linux/endorsed-distros.md#supported-distributions-and-versions)을 지원합니다. VHD에서 사용자 지정 Linux 이미지를 가져오는 단계는 배포판 마다 다릅니다. 모든 배포판은 Azure에서 실행되도록 설정해야 하는 고유한 필수 구성 요소를 포함하기 때문에 서로 다릅니다.

이 문서에서는 VHD에서 사용자 지정 Ubuntu 16.04\18.04\20.04 이미지를 가져오는 단계를 보여 줍니다. VHD를 사용하여 다른 배포판용 사용자 지정 이미지를 만드는 방법에 관한 내용은 [Linux 배포판의 일반 단계](../virtual-machines/linux/create-upload-generic.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 학교의 Azure 구독에서 [Azure 관리 디스크](../virtual-machines/managed-disks-overview.md)를 만들 수 있는 권한이 있어야 합니다.

물리적 랩 환경에서 Lab Services로 이미지를 이동할 때 랩의 클래스에 필요한 소프트웨어만 포함하도록 각 이미지를 다시 구성합니다. 자세한 내용은 [실제 랩에서 Azure Lab Services로 이동](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) 블로그 게시물을 참조하세요.

## <a name="prepare-a-custom-image-by-using-hyper-v-manager"></a>Hyper-V 관리자를 사용하여 사용자 지정 이미지 준비

다음 단계에서는 Windows Hyper-V 관리자를 사용하여 Hyper-V VM(가상 머신)에서 Ubuntu 16.04\18.04\20.04 이미지를 만드는 방법을 보여 줍니다.

1. Hyper-V VM에서 사용자 지정 이미지를 설정하는 데 사용할 Windows 호스트 머신에 공식 [Linux Ubuntu Server](https://ubuntu.com/server/docs) 이미지를 다운로드합니다.

   [GNOME](https://www.gnome.org/) GUI 데스크톱이 설치되지 *않은* Ubuntu 이미지를 사용하는 것이 좋습니다. GNOME은 현재 Lab Services에서 이미지가 제대로 작동하는 데 필요한 Azure Linux 에이전트와 충돌합니다. 예를 들어, Ubuntu Server 이미지를 사용하고 XFCE 또는 MATE 같은 다른 GUI 데스크톱을 설치합니다.

   또한 Ubuntu는 [다운로드를 위해 사전 빌드된 Azure VHD](https://cloud-images.ubuntu.com/)를 게시합니다. 이러한 VHD는 Linux 호스트 머신과 하이퍼바이저(예: KVM)에서 사용자 지정 이미지를 만들어야 합니다. 해당 VHD를 사용하려면 먼저 Windows에서 사용할 수 없는 qemu 등의 Linux 도구를 사용해야 수행할 수 있는 기본 사용자 암호를 설정해야 합니다. 따라서 Windows Hyper-V를 사용하여 사용자 지정 이미지를 만들면 해당 VHD에 연결하여 이미지를 사용자 지정할 수 없습니다. 사전 빌드된 Azure VHD에 대한 자세한 내용은 [Ubuntu 설명서](https://help.ubuntu.com/community/UEC/Images?_ga=2.114783623.1858181609.1624392241-1226151842.1623682781#QEMU_invocation)를 참조하세요.

1. 이미지에서 만든 물리적 랩 환경에서 Hyper-V VM으로 시작합니다. 자세한 내용은 [Hyper-V에서 가상 머신을 만드는 방법](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) 문서를 참조하세요. 다음과 같이 설정을 지정합니다.
    - VM은 **1세대** VM으로 만들어야 합니다.
    - **기본 스위치** 네트워크 구성 옵션을 사용하여 VM이 인터넷에 연결되도록 허용합니다.
    - **가상 하드 디스크 연결** 설정에서 디스크의 **크기** 는 다음 이미지와 같이 128GB보다 크지 ‘않아야’ 합니다.

        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="가상 하드 디스크 연결 화면을 보여 주는 스크린샷":::

    - **설치 옵션** 설정에서, 이전에 Ubuntu에서 다운로드한 **.iso** 파일을 선택합니다.

    디스크 크기가 128GB보다 큰 이미지는 Lab Services에서 지원되지 ‘않습니다’.

1. Hyper-V VM에 연결하고 [Ubuntu VHD를 만들고 업로드하는 수동 단계](../virtual-machines/linux/create-upload-ubuntu.md#manual-steps)의 단계에 따라 Azure에서 사용할 준비를 합니다.

    Azure 용 Linux 이미지를 준비하는 단계는 배포판에 따라 다릅니다. 각 배포에 대한 자세한 내용과 특정 단계는 [배포판 및 버전](../virtual-machines/linux/endorsed-distros.md#supported-distributions-and-versions)을 참조하세요.

    이전 단계를 수행하는 경우 다음과 같은 몇 가지 사항이 중요합니다.
    - 해당 단계에서는 **deprovision+user** 명령을 실행할 때 [일반화](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) 이미지를 만듭니다. 그러나 이미지에서 중요한 정보가 모두 지워졌다거나 재배포에 적합하다는 것이 보장되지는 않습니다.
    - 마지막 단계는 **VHDX** 파일을 **VHD** 파일로 변환하는 것입니다. **Hyper-V 관리자** 를 사용하여 작업을 수행하는 방법을 보여 주는 단계는 다음과 같습니다.

        1. **Hyper-V 관리자** > **작업** > **디스크 편집** 으로 이동합니다.
        1. 그런 다음, 디스크를 VHDX에서 VHD로 **변환** 합니다.
        1. **디스크 유형** 에서, **고정 크기** 를 선택합니다.
            - 디스크 크기를 확장하는 경우, 128GB를 초과하지 *않아야* 합니다.
            :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="작업 선택 화면을 보여 주는 스크린샷":::

다음 PowerShell cmdlet을 사용하면 VHD 크기를 조정하고 VHDX로 변환하는 데 도움이 됩니다.

- [Resize-VHD](/powershell/module/hyper-v/resize-vhd)
- [Convert-VHD](/powershell/module/hyper-v/convert-vhd)

## <a name="upload-the-custom-image-to-a-shared-image-gallery"></a>공유 이미지 갤러리에 사용자 지정 이미지 업로드

1. VHD를 Azure에 업로드하여 관리 디스크를 만듭니다.
    1. [Azure에 VHD 업로드 또는 다른 지역으로 관리 디스크 복사](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)에 표시된 대로 Storage Explorer 또는 명령줄의 AzCopy를 사용할 수 있습니다.

    1. VHD를 업로드한 후 Azure Portal에서 볼 수 있는 관리 디스크가 생성됩니다.

    머신이 절전 모드로 전환되거나 잠기면 업로드 프로세스가 중단되고 실패할 수 있습니다. AzCopy가 완료되면, 디스크에 대한 SAS 액세스를 취소해야 합니다. 취소하지 않으면 디스크에서 이미지를 만들려고 할 때 “‘이미지 만들기’ 작업이 ‘활성 업로드’ 상태의 ‘디스크 이름’ 디스크에서 지원되지 않습니다. 오류 코드: OperationNotAllowed*.” 오류가 표시됩니다.

    관리 디스크에 대해 Azure Portal의 **크기+성능** 탭을 사용하여 디스크 크기를 변경합니다. 앞에서 설명한 대로 크기는 128GB보다 크지 ‘않아야’ 합니다.

1. 공유 이미지 갤러리에서 이미지 정의 및 버전을 생성합니다.
    1. [이미지 정의를 만듭니다](../virtual-machines/image-version.md).
        - **VM 세대** 에 대해 **1세대** 를 선택합니다.
        - **운영 체제** 로 **Linux** 를 선택합니다.
        - **운영 체제 상태** 로 **범용** 을 선택합니다.

    이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](../virtual-machines/shared-image-galleries.md#image-definitions)를 참조하세요.

    기존 이미지 정의를 사용하고 사용자 지정 이미지의 새 버전을 만들도록 선택할 수도 있습니다.

1. [이미지 버전을 만듭니다](../virtual-machines/image-version.md).
    - **버전 번호** 속성은 *MajorVersion.MinorVersion.Patch* 형식을 사용합니다. Lab Services를 사용하여 랩을 만들고 사용자 지정 이미지를 선택하면 최신 버전의 이미지가 자동으로 사용됩니다. 최신 버전은 MajorVersion, MinorVersion, Patch의 순서로 가장 큰 값을 기준으로 선택됩니다.
    - **원본** 의 경우 드롭다운 목록에서 **디스크 및/또는 스냅샷** 을 선택합니다.
    - **OS 디스크** 속성으로 이전 단계에서 생성한 디스크를 선택합니다.

    이미지 정의에 대해 지정할 수 있는 값에 관한 자세한 내용은 [이미지 버전](../virtual-machines/shared-image-galleries.md#image-versions)을 참조하세요.

## <a name="create-a-lab"></a>랩 만들기

Lab Services에서 [랩을 만들고](tutorial-setup-classroom-lab.md) 공유 이미지 갤러리에서 사용자 지정 이미지를 선택합니다.

OS가 원래 Hyper-V VM에 설치된 *후* 디스크를 확장한 경우 할당되지 않은 디스크 공간을 사용하도록 Linux의 파일 시스템에 있는 파티션을 확장해야 할 수도 있습니다.  랩의 템플릿 VM에 로그인하고 [디스크 파티션 및 파일 시스템 확장](../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem)에 설명된 것과 비슷한 단계를 수행합니다.

OS 디스크는 일반적으로 **/dev/sad2** 파티션에 있습니다. OS 디스크 파티션의 현재 크기를 보려면 **df -h** 명령을 사용합니다.

## <a name="next-steps"></a>다음 단계

- [공유 이미지 갤러리 개요](../virtual-machines/shared-image-galleries.md)
- [공유 이미지 갤러리 연결 또는 분리](how-to-attach-detach-shared-image-gallery.md)
- [공유 이미지 갤러리 사용](how-to-use-shared-image-gallery.md)