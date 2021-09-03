---
title: Azure Lab Services에서 공유 이미지 갤러리 사용 | Microsoft Docs
description: 사용자가 다른 사용자와 이미지를 공유하고 다른 사용자가 해당 이미지를 사용하여 랩에서 템플릿 VM을 만들 수 있도록 공유 이미지 갤러리를 사용하도록 랩 계정을 구성하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: caba089d5f3f0f9ec50f575a06ce9fad1bf8ef8f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529027"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services에서 공유 이미지 갤러리 사용

이미지에는 VM에 설치된 운영 체제, 소프트웨어 애플리케이션, 파일, 설정이 포함됩니다.  새 랩을 설정할 때 사용할 수 있는 두 가지 유형의 이미지가 있습니다.
-   Azure 내에서 사용하도록 Microsoft에서 미리 빌드한 마켓플레이스 이미지.  이러한 이미지에는 Windows 또는 Linux가 설치되어 있으며 소프트웨어 애플리케이션도 포함될 수 있습니다.  예를 들어 [Data Science Virtual Machine 이미지](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm)에는 설치된 딥 러닝 프레임워크와 도구가 포함되어 있습니다.
-   교육 기관의 IT 부서 및/또는 기타 교육자가 만든 사용자 지정 이미지.  Windows 및 Linux 사용자 지정 이미지를 모두 만들 수 있으며 고유한 요구 사항에 따라 Microsoft와 타사 애플리케이션을 유연하게 설치할 수 있습니다.  또한 파일을 추가하고 애플리케이션 설정을 변경할 수 있습니다.

이 문서에서는 다른 사용자가 랩을 만드는 데 사용할 수 있도록 교육자/랩 관리자가 템플릿 가상 머신 이미지를 [공유 이미지 갤러리](../virtual-machines/shared-image-galleries.md)에 저장하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 공유 이미지 갤러리를 사용하는 동안 Azure Lab Services는 128GB 미만의 OS 디스크 공간이 있는 이미지만 지원합니다. 128GB 이상의 디스크 공간이 있거나 여러 디스크가 있는 이미지는 랩 생성 동안 가상 머신 이미지 목록에 표시되지 않습니다.

## <a name="scenarios"></a>시나리오
이 기능이 지원하는 몇 가지 시나리오는 다음과 같습니다. 

- 랩 계정 관리자는 공유 이미지 갤러리를 랩 계정에 연결하고, 랩 컨텍스트 외부의 공유 이미지 갤러리에 이미지를 업로드합니다. 그런 다음, 랩 작성자는 공유 이미지 갤러리에서 해당 이미지를 사용하여 랩을 만들 수 있습니다. 
- 랩 계정 관리자는 공유 이미지 갤러리를 랩 계정에 연결합니다. 랩 작성자(교육자)는 자체 랩의 사용자 지정된 이미지를 공유 이미지 갤러리에 저장합니다. 그런 다음, 다른 랩 작성자가 공유 이미지 갤러리에서 이 이미지를 선택하여 자신의 랩에 대한 템플릿을 만들 수 있습니다. 

    이미지가 공유 이미지 갤러리에 저장되는 경우 Azure Lab Services는 저장된 이미지를 동일한 [지리](https://azure.microsoft.com/global-infrastructure/geographies/)에서 사용할 수 있는 다른 지역에 복제합니다. 그러면 해당 이미지를 동일한 지리의 다른 지역에서 만든 랩에서 사용할 수 있습니다. 공유 이미지 갤러리에 이미지를 저장하면 복제된 모든 이미지에 대한 비용을 포함하는 추가 비용이 발생합니다. 이 비용은 Azure Lab Services 사용 비용과는 별개입니다. Shared Image Gallery 가격 책정에 대한 자세한 내용은 [Shared Image Gallery – 청구](../virtual-machines/shared-image-galleries.md#billing)를 참조하세요.
    
## <a name="prerequisites"></a>사전 요구 사항
- [Azure PowerShell](../virtual-machines/shared-images-powershell.md) 또는 [Azure CLI](../virtual-machines/shared-images-cli.md)를 사용하여 공유 이미지 갤러리를 만듭니다.
- 공유 이미지 갤러리를 랩 계정에 연결했습니다. 단계별 지침은 [공유 이미지 갤러리를 연결하거나 분리하는 방법](how-to-attach-detach-shared-image-gallery.md)을 참조하세요.

## <a name="save-an-image-to-the-shared-image-gallery"></a>공유 이미지 갤러리에 이미지 저장
공유 이미지 갤러리가 연결된 후에는 랩 계정 관리자 또는 교육자는 다른 교육자가 다시 사용할 수 있도록 공유 이미지 갤러리에 이미지를 저장할 수 있습니다. 

1. 랩에 대한 **템플릿** 페이지의 도구 모음에서 **공유 이미지 갤러리로 내보내기** 를 선택합니다.

    ![이미지 저장 단추](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. **공유 이미지 갤러리로 내보내기** 대화 상자에서 **이미지에 대한 이름** 을 입력한 다음, **내보내기** 를 선택합니다. 

    ![공유 이미지 갤러리로 내보내기 대화 상자](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)

3. **템플릿** 페이지에서 이 작업의 진행률을 볼 수 있습니다. 이 작업에는 시간이 오래 걸릴 수 있습니다. 

    ![내보내기 진행 중](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. 내보내기 작업에 성공하면 다음 메시지가 표시됩니다.

    ![내보내기 완료](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    공유 이미지 갤러리에 이미지를 저장하면 다른 랩을 만들 때 갤러리에서 해당 이미지를 사용할 수 있습니다. 랩 컨텍스트 외부의 공유 이미지 갤러리에 이미지를 업로드할 수도 있습니다. 자세한 내용은 다음을 참조하세요.

    - [Shared Image Gallery 개요](../virtual-machines/shared-images-powershell.md)
    - [사용자 지정 이미지를 만들기 위한 권장 방법](approaches-for-custom-image-creation.md)

    > [!IMPORTANT]
    > Azure Lab Services에서 [랩의 템플릿 이미지](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery)를 공유 이미지 갤러리에 저장하면 이미지가 갤러리에 **특수 이미지** 로 업로드됩니다. [특수 이미지](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)는 머신 관련 정보와 사용자 프로필을 유지합니다. Azure Lab Services 외부의 갤러리에 일반 이미지를 직접 업로드할 수 있습니다.    

## <a name="use-a-custom-image-from-the-shared-image-gallery"></a>Shared Image Gallery의 사용자 지정 이미지 사용
교육자는 새 랩을 설정할 때 만들어지는 템플릿 VM에 대한 공유 이미지 갤러리에서 사용할 수 있는 사용자 지정 이미지를 선택할 수 있습니다.

![갤러리에서 가상 머신 이미지 사용](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> Azure Lab Services에서 **일반화** 이미지와 **특수** 이미지를 기준으로 하는 템플릿 VM을 만들 수 있습니다.

### <a name="resave-a-custom-image-to-shared-image-gallery"></a>Shared Image Gallery에 사용자 지정 이미지 예약

공유 이미지 갤러리의 사용자 지정 이미지에서 랩을 만든 후에는 템플릿 VM을 사용하여 이미지를 변경하고 이미지를 공유 이미지 갤러리로 다시 내보낼 수 있습니다.  다시 내보낼 경우 새 이미지를 만들거나 원래 이미지를 업데이트하는 옵션이 있습니다. 

 ![공유 이미지 갤러리로 다시 내보내기 대화 상자](./media/how-to-use-shared-image-gallery/reexport-to-shared-image-gallery-dialog.png) 

**새 이미지 만들기** 를 선택하면 새 [이미지 정의](../virtual-machines/shared-image-galleries.md#image-definitions)가 만들어집니다.  이렇게 하면 공유 이미지 갤러리에 이미 있는 원래 사용자 지정 이미지를 변경하지 않고 완전히 새로운 사용자 지정 이미지를 저장할 수 있습니다.

대신 **기존 이미지 업데이트** 를 선택하면 원본 사용자 지정 이미지의 정의가 새 [버전](../virtual-machines/shared-image-galleries.md#image-versions)으로 업데이트됩니다.  랩 서비스는 다음에 사용자 지정 이미지를 사용하여 랩을 만들 때 최신 버전을 자동으로 사용합니다.

## <a name="next-steps"></a>다음 단계
랩 계정에 연결 및 분리하여 공유 이미지 갤러리를 설정하는 방법에 대한 자세한 내용은 [공유 이미지 갤러리를 연결 및 분리하는 방법](how-to-attach-detach-shared-image-gallery.md)을 참조하세요.

랩 컨텍스트 외부의 공유 이미지 갤러리에 사용자 지정 이미지를 가져오기 위한 다른 옵션을 살펴보려면 [사용자 지정 이미지를 만들기 위한 권장 방법](approaches-for-custom-image-creation.md)을 참조하세요.

일반적인 공유 이미지 갤러리에 대한 자세한 내용은 [공유 이미지 갤러리](../virtual-machines/shared-image-galleries.md)를 참조하세요.