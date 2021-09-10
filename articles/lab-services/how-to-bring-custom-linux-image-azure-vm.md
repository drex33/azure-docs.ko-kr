---
title: Azure Lab Services-Azure 가상 머신에서 Linux 사용자 지정 이미지를 가져오는 방법.
description: Azure 가상 머신에서 Linux 사용자 지정 이미지를 가져오는 방법을 설명 합니다.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: 067ec63bd910b23db333f273fc9c01b429f2f15a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535601"
---
# <a name="bring-a-linux-custom-image-from-an-azure-virtual-machine"></a>Azure 가상 머신에서 Windows 사용자 지정 이미지 가져오기

이 문서의 단계에서는 [Azure VM(가상 머신)](https://azure.microsoft.com/services/virtual-machines/)에서 시작하는 사용자 지정 이미지를 가져오는 방법을 보여줍니다.  이 방법을 사용하면 Azure VM에서 이미지를 설정하고 이미지를 Shared Image Gallery로 가져와 Lab Services 내에서 사용할 수 있습니다.  사용자 지정 이미지를 만드는 데 이 방법을 사용하기 전에 [사용자 지정 이미지를 만들기 위한 권장 방법](approaches-for-custom-image-creation.md) 문서를 읽어 시나리오에 가장 적합한 방법을 결정해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

아래 단계를 완료하려면 학교의 Azure 구독에서 Azure VM을 만들 수 있는 권한이 있어야 합니다.

## <a name="prepare-a-custom-image-on-an-azure-vm"></a>Azure VM을 기반으로 사용자 지정 이미지 만들기

1. [Azure Portal](../virtual-machines/windows/quick-create-portal.md), [PowerShell](../virtual-machines/windows/quick-create-powershell.md), [Azure CLI](../virtual-machines/windows/quick-create-cli.md) 또는 [ARM 템플릿](../virtual-machines/windows/quick-create-template.md)을 사용하여 Azure VM을 만듭니다.
    
    - 디스크 설정을 지정하는 경우 디스크 크기는 128GB보다 크지 ‘않아야’ 합니다.
    
1. 소프트웨어를 설치하고 Azure VM의 이미지에 필요한 구성 변경을 수행합니다.

1. 필요에 따라 이미지를 일반화할 수 있습니다.  범용 이미지를 만들려면 [1 단계인 VM 프로비전해제](../virtual-machines/linux/capture-image.md#step-1-deprovision-the-vm)에 설명 된 단계를 수행합니다.  **-프로비전 해제 + 사용자** 명령을 사용 하면 이미지가 일반화됩니다.  이 과정이 이미지에서 민감한 정보가 모두 지워졌다거나 재배포에 적합하다는 것을 보장하지는 않습니다.

    그렇지 않고 특수 이미지를 만들려는 경우 다음 단계로 건너뛸 수 있습니다.

    머신별 정보 및 사용자 프로필을 유지 관리하려면 특수화 이미지를 만들어야 합니다.  일반화 이미지와 특수화 이미지 간 차이점에 관한 자세한 내용은 [일반화 및 특수화 이미지](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)를 참조하세요.

## <a name="import-the-custom-image-into-a-shared-image-gallery"></a>사용자 지정 이미지를 공유 이미지 갤러리로 가져옵니다.

1. Shared Image Gallery에서 [이미지 정의를 만들거나](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition) 기존 이미지 정의를 선택합니다.
     - **VM 세대** 에 대해 **1세대** 를 선택합니다.
     - **운영 체제 상태** 에 대해 **특수화** 또는 **일반화** 이미지를 만들지 여부를 선택합니다.

    이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](../virtual-machines/shared-image-galleries.md#image-definitions)를 참조하세요. 
    
    기존 이미지 정의를 사용하고 사용자 지정 이미지의 새 버전을 만들도록 선택할 수도 있습니다.
    
1. [이미지 버전을 만듭니다](../virtual-machines/windows/shared-images-portal.md#create-an-image-version).
    - **버전 번호** 속성은 *MajorVersion.MinorVersion.Patch* 형식을 사용합니다.   
    - **원본** 의 경우 드롭다운 목록에서 **디스크 및/또는 스냅샷** 을 선택합니다.
    - **OS 디스크** 속성의 경우 이전 단계에서 만든 Azure VM의 디스크를 선택합니다.

PowerShell을 사용하여 위의 단계를 자동화할 수도 있습니다.  자세한 내용은 다음 스크립트 및 함께 제공되는 추가 정보를 참조하세요.
- [Shared Image Gallery 스크립트로 이미지 가져오기](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/)

## <a name="create-a-lab"></a>랩 만들기

1. Lab Services에서 [랩을 만들고](tutorial-setup-classroom-lab.md) 공유 이미지 갤러리에서 사용자 지정 이미지를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [공유 이미지 갤러리 개요](../virtual-machines/shared-image-galleries.md)
* [공유 이미지 갤러리 연결 또는 분리](how-to-attach-detach-shared-image-gallery.md)
* [공유 이미지 갤러리를 사용하는 방법](how-to-use-shared-image-gallery.md)