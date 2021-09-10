---
title: Azure Lab Services - Azure 가상 머신에서 Windows 사용자 지정 이미지를 가져오는 방법
description: Azure 가상 머신에서 Windows 사용자 지정 이미지를 가져오는 방법을 설명합니다.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: ab79f4acb0b61a46e4a8f1cebe243e6df43702bf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528440"
---
# <a name="bring-a-windows-custom-image-from-an-azure-virtual-machine"></a>Azure 가상 머신에서 Windows 사용자 지정 이미지 가져오기

이 문서의 단계에서는 [Azure VM(가상 머신)](https://azure.microsoft.com/services/virtual-machines/)에서 시작하는 사용자 지정 이미지를 가져오는 방법을 보여줍니다.  이 방식을 사용하면 Azure VM에서 이미지를 설정하고 이미지를 공유 이미지 갤러리로 가져와 Lab Services 내에서 사용할 수 있습니다.  사용자 지정 이미지를 만드는 데 이 방식을 사용하기 전에 [사용자 지정 이미지를 만들기 위한 권장 방법](approaches-for-custom-image-creation.md) 문서를 읽어 시나리오에 가장 적합한 방법을 결정해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하려면 학교의 Azure 구독에서 Azure VM을 만들 수 있는 권한이 있어야 합니다.

## <a name="prepare-a-custom-image-on-an-azure-vm"></a>Azure VM에서 사용자 지정 이미지 준비

1. [Azure Portal](../virtual-machines/windows/quick-create-portal.md), [PowerShell](../virtual-machines/windows/quick-create-powershell.md), [Azure CLI](../virtual-machines/windows/quick-create-cli.md) 또는 [ARM 템플릿](../virtual-machines/windows/quick-create-template.md)을 사용하여 Azure VM을 만듭니다.
    
    - 디스크 설정을 지정하는 경우 디스크 크기는 128GB보다 크지 ‘않아야’ 합니다.
    
1. 소프트웨어를 설치하고 Azure VM의 이미지에 필요한 구성 변경을 수행합니다.

1. 필요에 따라 이미지를 일반화할 수 있습니다. 일반화 이미지를 만들어야 하는 경우 [SysPrep](../virtual-machines/generalize.md#windows)을 실행합니다.  그렇지 않고 특수 이미지를 만드는 경우 다음 단계로 건너뛸 수 있습니다.

    머신별 정보 및 사용자 프로필을 유지 관리하려면 특수화 이미지를 만들어야 합니다.  일반화 이미지와 특수화 이미지 간 차이점에 관한 자세한 내용은 [일반화 및 특수화 이미지](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)를 참조하세요.

## <a name="import-the-custom-image-into-a-shared-image-gallery"></a>사용자 지정 이미지를 공유 이미지 갤러리로 가져옵니다.

1. 공유 이미지 갤러리에서 [이미지 정의를 만들](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition)거나 기존 이미지 정의를 선택합니다.
     - **VM 세대** 에 대해 **1세대** 를 선택합니다.
     - **운영 체제 상태** 에 대해 **특수화** 또는 **일반화** 이미지를 만들지 여부를 선택합니다.

    이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](../virtual-machines/shared-image-galleries.md#image-definitions)를 참조하세요. 
    
    기존 이미지 정의를 사용하고 사용자 지정 이미지의 새 버전을 만들도록 선택할 수도 있습니다.
    
1. [이미지 버전을 만듭니다](../virtual-machines/windows/shared-images-portal.md#create-an-image-version).
    - **버전 번호** 속성은 *MajorVersion.MinorVersion.Patch* 형식을 사용합니다.   
    - **원본** 의 경우 드롭다운 목록에서 **디스크 및/또는 스냅샷** 을 선택합니다.
    - **OS 디스크** 속성의 경우 이전 단계에서 만든 Azure VM의 디스크를 선택합니다.

    PowerShell을 사용하여 Azure VM에서 공유 이미지 갤러리로 사용자 지정 이미지를 가져올 수도 있습니다.  자세한 내용은 다음 스크립트 및 함께 제공되는 추가 정보를 참조하세요.
    - [공유 이미지 갤러리로 이미지 가져오기 스크립트](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/)

## <a name="create-a-lab"></a>랩 만들기

1. Lab Services에서 [랩을 만들고](tutorial-setup-classroom-lab.md) 공유 이미지 갤러리에서 사용자 지정 이미지를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [공유 이미지 갤러리 개요](../virtual-machines/shared-image-galleries.md)
* [공유 이미지 갤러리 연결 또는 분리](how-to-attach-detach-shared-image-gallery.md)
* [공유 이미지 갤러리를 사용하는 방법](how-to-use-shared-image-gallery.md)