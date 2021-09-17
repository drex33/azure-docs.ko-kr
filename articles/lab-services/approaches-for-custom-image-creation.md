---
title: Azure Lab Services - 랩에 대한 사용자 지정 이미지를 만드는 권장 방법
description: 랩에 대한 사용자 지정 이미지를 만드는 방법에 대해 설명합니다.
ms.date: 07/27/2021
ms.topic: article
ms.openlocfilehash: 621456c910b5137ed14a77d8d2cfeb9664910fa9
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450649"
---
# <a name="recommended-approaches-for-creating-custom-images"></a>사용자 지정 이미지를 만들기 위한 권장 방법
이 문서에서는 사용자 지정 이미지를 만들기 위한 다음과 같은 권장 방법을 설명합니다.

-   [랩의 템플릿 VM(가상 머신)](how-to-create-manage-template.md)에서 사용자 지정 이미지를 만들고 저장합니다.
-   다음을 사용하여 랩의 컨텍스트 외부에서 사용자 지정 이미지를 가져옵니다.
    - [Azure VM](https://azure.microsoft.com/services/virtual-machines/).
    - 물리적 랩 환경의 VHD.

## <a name="save-a-custom-image-from-a-labs-template-vm"></a>랩의 템플릿 VM에서 사용자 지정 이미지 저장

랩의 템플릿 VM을 사용하여 사용자 지정 이미지를 만들고 저장하는 기능은 Azure Lab Services 포털을 통해 지원되므로 이미지를 만드는 가장 간단한 방법입니다. 따라서 IT 부서와 교육자 모두 랩의 템플릿 VM을 사용하여 사용자 지정 이미지를 만들 수 있습니다.

예를 들어, Azure Marketplace 이미지 중 하나로 시작한 다음, 클래스에 필요한 소프트웨어 애플리케이션과 도구를 설치할 수 있습니다. 이미지 설정을 완료한 후에는 [연결된 공유 이미지 갤러리](how-to-attach-detach-shared-image-gallery.md)에 저장하여 다른 교육자와 함께 이미지를 사용하여 새 랩을 만들 수 있습니다.

이 접근 방식에서 주의해야 할 몇 가지 주요 사항이 있습니다.

- Lab Services는 템플릿 VM에서 이미지를 내보낼 때 ‘특수화’ 이미지를 자동으로 저장합니다. 대부분의 경우 이미지에 머신별 정보와 사용자 프로필이 유지되므로 특수화 이미지는 새 랩을 만드는 데 적합합니다. 특수 이미지를 사용하면, 이미지를 사용하여 새 랩을 생성할 때 설치된 소프트웨어가 동일하게 실행되도록 할 수 있습니다. *범용* 이미지를 만들어야 하는 경우, 이 문서의 다른 권장 방법 중 하나를 사용하여 사용자 지정 이미지를 만들어야 합니다.

    Azure Lab Services에서 범용 이미지와 특수 이미지를 기반으로 하는 랩을 만들 수 있습니다. 차이점에 관한 자세한 내용은 [일반화 및 특수화 이미지](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)를 참조하세요.

- 이미지 설정과 관련된 고급 시나리오의 경우 대신 물리적 랩 환경에서 Azure VM 또는 VHD를 사용하여 랩 외부에서 이미지를 만드는 것이 유용할 수 있습니다. 자세한 내용은 다음 섹션을 참조하세요.

### <a name="use-a-labs-template-vm-to-save-a-custom-image"></a>랩의 템플릿 VM을 사용하여 사용자 지정 이미지 저장 

랩의 템플릿 VM을 사용하여 Windows 또는 Linux 사용자 지정 이미지를 만들 수 있습니다. 자세한 내용은 [공유 이미지 갤러리에 이미지 저장](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery)을 참조하세요.

## <a name="bring-a-custom-image-from-an-azure-vm"></a>Azure VM에서 사용자 지정 이미지 가져오기

또 다른 방법은 Azure VM을 사용하여 사용자 지정 이미지를 설정하는 것입니다. 이미지 설정을 완료한 후 사용자와 동료가 이미지를 사용하여 새 랩을 만들 수 있도록 공유 이미지 갤러리에 저장할 수 있습니다.

Azure VM을 사용하면 더 많은 유연성을 제공합니다.
- [범용 또는 특수](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) 이미지를 모두 생성할 수 있습니다. 생성하지 않고 [이미지를 내보내는](how-to-use-shared-image-gallery.md) 데 랩의 템플릿 VM을 사용하는 경우 이미지는 항상 특수화됩니다.
- 이미지 설정에 도움이 될 수 있는 Azure VM의 고급 기능에 액세스할 수 있습니다. 예를 들어, [확장](../virtual-machines/extensions/overview.md)을 사용하여 배포 후 구성 및 자동화를 수행할 수 있습니다. 또한 VM의 [부팅 진단](../virtual-machines/boot-diagnostics.md) 및 [직렬 콘솔](/troubleshoot/azure/virtual-machines/serial-console-overview)에 액세스할 수 있습니다.

Azure VM을 사용하여 이미지를 설정하는 것은 더 복잡합니다. 따라서 IT 부서가 일반적으로 Azure VM에서 사용자 지정 이미지를 만들어야 합니다.

### <a name="use-an-azure-vm-to-set-up-a-custom-image"></a>Azure VM을 사용하여 사용자 지정 이미지 설정

Azure VM에서 사용자 지정 이미지를 가져오는 고급 단계는 다음과 같습니다.

1. Windows 또는 Linux Marketplace 이미지를 사용하여 [Azure VM](https://azure.microsoft.com/services/virtual-machines/)을 만듭니다.
1. Azure VM에 연결하고 추가 소프트웨어를 설치합니다. 랩에 필요한 다른 사용자 지정을 만들 수도 있습니다.
1. 이미지 설정을 마쳤으면 [VM의 이미지를 공유 이미지 갤러리에 저장](../virtual-machines/image-version.md)합니다. 이 단계의 일부로 이미지 정의와 버전도 만들어야 합니다.
1. 사용자 지정 이미지가 갤러리에 저장된 후 이미지를 사용하여 새 랩을 만들 수 있습니다. 


단계는 사용자 지정 Windows 또는 Linux 이미지를 만드는지 여부에 따라 달라집니다. 자세한 단계는 다음 문서를 참조하세요.

-   [Azure VM에서 사용자 지정 Windows 이미지 가져오기](how-to-bring-custom-windows-image-azure-vm.md)
-   [Azure VM에서 사용자 지정 Linux 이미지 가져오기](how-to-bring-custom-linux-image-azure-vm.md)

## <a name="bring-a-custom-image-from-a-vhd-in-your-physical-lab-environment"></a>물리적 랩 환경의 VHD에서 사용자 지정 이미지 가져오기

고려해야 할 세 번째 방법은 물리적 랩 환경의 VHD에서 공유 이미지 갤러리로 사용자 지정 이미지를 가져오는 것입니다. 이미지가 공유 이미지 갤러리에 포함된 후 사용자와 다른 교육자는 이미지를 사용하여 새 랩을 만들 수 있습니다.

이 방법을 사용하게 되는 몇 가지 이유는 다음과 같습니다.

- 랩에서 사용할 [범용 또는 특수](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) 이미지를 만들 수 있는 유연성이 있습니다. 생성하지 않고 이미지를 내보내는 데 [랩의 템플릿 VM](how-to-use-shared-image-gallery.md)을 사용하는 경우 이미지는 항상 특수화됩니다.
- 온-프레미스 환경에 있는 리소스에 액세스할 수 있습니다. 예를 들어, 온-프레미스 환경에 랩의 템플릿 VM에 복사하는 데 시간이 너무 오래 걸리는 대용량 설치 파일이 있을 수 있습니다.
- [Microsoft Endpoint Configuration Manager](/mem/configmgr/core/understand/introduction)와 같은 다른 도구를 사용하여 만든 이미지를 업로드할 수 있으므로 랩의 템플릿 VM을 사용하여 이미지를 수동으로 설정할 필요가 없습니다.

Azure 내에서 작동하도록 이미지가 제대로 설정되었는지 확인해야 하므로 VHD에서 사용자 지정 이미지를 가져오는 것이 가장 고급 방법입니다. 따라서 IT 부서가 일반적으로 VHD에서 사용자 지정 이미지를 만드는 역할을 담당합니다.

### <a name="bring-a-custom-image-from-a-vhd"></a>VHD에서 사용자 지정 이미지 가져오기

VHD에서 사용자 지정 이미지를 가져오는 고급 단계는 다음과 같습니다.

1. 온-프레미스 컴퓨터에서 [Windows Hyper-V](/virtualization/hyper-v-on-windows/about/)를 사용하여 Windows 또는 Linux VHD를 생성합니다.
1. Hyper-V VM에 연결하고 추가 소프트웨어를 설치합니다. 랩에 필요한 다른 사용자 지정을 만들 수도 있습니다.
1. 이미지 설정을 마쳤으면 VHD를 업로드하여 Azure에서 [관리 디스크](../virtual-machines/managed-disks-overview.md)를 만듭니다.
1. 관리 디스크에서 공유 이미지 갤러리에 [이미지 정의](../virtual-machines/shared-image-galleries.md#image-definitions) 및 버전을 만듭니다.
1. 사용자 지정 이미지가 갤러리에 저장된 후 이미지를 사용하여 새 랩을 만들 수 있습니다. 

단계는 사용자 지정 Windows 또는 Linux 이미지를 만드는지 여부에 따라 달라집니다. 자세한 단계는 다음 문서를 참조하세요.

-   [VHD에서 사용자 지정 Windows 이미지 가져오기](upload-custom-image-shared-image-gallery.md)
-   [VHD에서 사용자 지정 Linux 이미지 가져오기](how-to-bring-custom-linux-image-vhd.md)

## <a name="next-steps"></a>다음 단계

* [공유 이미지 갤러리 개요](../virtual-machines/shared-image-galleries.md)
* [공유 이미지 갤러리 연결 또는 분리](how-to-attach-detach-shared-image-gallery.md)
* [공유 이미지 갤러리 사용](how-to-use-shared-image-gallery.md)
