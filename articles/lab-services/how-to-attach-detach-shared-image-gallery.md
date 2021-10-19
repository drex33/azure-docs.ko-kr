---
title: Azure Lab Services에서 공유 이미지 갤러리 연결 또는 분리 | Microsoft Docs
description: 이 문서에서는 Azure Lab Services의 클래스룸 랩에 공유 이미지 갤러리를 연결하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 09/11/2020
ms.openlocfilehash: 496b88617fd6ae28a57b19e930002120e3352a4f
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180992"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services에서 공유 이미지 갤러리 연결 또는 분리
이 문서에서는 공유 이미지 갤러리를 랩 계정에 연결하거나 분리하는 방법을 보여줍니다. 

> [!NOTE]
> Azure Lab Services에서 [랩의 템플릿 이미지](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery)를 공유 이미지 갤러리에 저장하면 이미지가 갤러리에 특수 이미지로 업로드됩니다. [특수 이미지](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)는 머신 관련 정보와 사용자 프로필을 유지합니다. Azure Lab Services 외부의 갤러리에 일반 이미지를 직접 업로드할 수 있습니다. 
>
> 랩 작성자는 Azure Lab Services에서 일반화된 이미지와 특수 이미지를 기반으로 템플릿 VM을 만들 수 있습니다. 

## <a name="scenarios"></a>시나리오
이 기능이 지원하는 몇 가지 시나리오는 다음과 같습니다. 

- 랩 계정 관리자는 공유 이미지 갤러리를 랩 계정에 연결하고, 랩 컨텍스트 외부의 공유 이미지 갤러리에 이미지를 업로드합니다. 그런 다음, 랩 작성자는 공유 이미지 갤러리에서 해당 이미지를 사용하여 랩을 만들 수 있습니다. 
- 랩 계정 관리자는 공유 이미지 갤러리를 랩 계정에 연결합니다. 랩 작성자(교육자)는 자체 랩의 사용자 지정된 이미지를 공유 이미지 갤러리에 저장합니다. 그런 다음, 다른 랩 작성자가 공유 이미지 갤러리에서 이 이미지를 선택하여 자신의 랩에 대한 템플릿을 만들 수 있습니다. 

    이미지가 공유 이미지 갤러리에 저장되는 경우 Azure Lab Services는 저장된 이미지를 동일한 [지리](https://azure.microsoft.com/global-infrastructure/geographies/)에서 사용할 수 있는 다른 지역에 복제합니다. 그러면 해당 이미지를 동일한 지리의 다른 지역에서 만든 랩에서 사용할 수 있습니다. 공유 이미지 갤러리에 이미지를 저장하면 복제된 모든 이미지에 대한 비용을 포함하는 추가 비용이 발생합니다. 이 비용은 Azure Lab Services 사용 비용과는 별개입니다. Shared Image Gallery 가격 책정에 대한 자세한 내용은 [Shared Image Gallery – 청구](../virtual-machines/shared-image-galleries.md#billing)를 참조하세요.

> [!IMPORTANT]
> 공유 이미지 갤러리를 사용하는 동안 Azure Lab Services는 128GB 미만의 OS 디스크 공간이 있는 이미지만 지원합니다. 128GB 이상의 디스크 공간이 있거나 여러 디스크가 있는 이미지는 랩 생성 동안 가상 머신 이미지 목록에 표시되지 않습니다.

## <a name="configure-at-the-time-of-lab-account-creation"></a>랩 계정 생성 시 구성
랩 계정을 만들 때 공유 이미지 갤러리를 랩 계정에 연결할 수 있습니다. 기존 공유 이미지 갤러리를 드롭다운 목록에서 선택하거나 공유 이미지 갤러리를 새로 만들 수 있습니다. 공유 이미지 갤러리를 만들어서 랩 계정에 연결하려면 **새로 만들기** 를 선택하고 갤러리 이름을 입력한 다음, **확인** 을 입력합니다. 

![랩 계정을 만들 때 공유 이미지 갤러리 구성](./media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>랩 계정을 만든 후 구성
랩 계정을 만든 후에는 다음 작업을 수행할 수 있습니다.

- 공유 이미지 갤러리 생성 및 연결
- 공유 이미지 갤러리를 랩 계정에 연결
- 공유 이미지 갤러리를 랩 계정에서 분리

## <a name="create-and-attach-a-shared-image-gallery"></a>공유 이미지 갤러리 생성 및 연결
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스** 를 선택합니다. **DEVOPS** 섹션에서 **랩 서비스** 를 선택합니다. **랩 서비스** 옆에 있는 별표(`*`)를 선택하면 왼쪽 메뉴의 **즐겨찾기** 섹션에 추가됩니다. 다음부터는 **즐겨찾기** 아래에서 **랩 서비스** 를 선택합니다.

    ![모든 서비스 -> 랩 서비스](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 랩 계정을 선택하여 **랩 계정** 페이지를 표시합니다. 
4. 왼쪽 메뉴에서 **공유 이미지 갤러리** 를 선택하고 도구 모음에서 **+ 만들기** 를 선택합니다.  

    ![Shared Image Gallery 만들기 단추](./media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. **Shared Image Gallery 만들기** 창에서 갤러리의 **이름** 을 입력하고 **확인** 을 입력합니다. 

    ![Shared Image Gallery 만들기 창](./media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services는 공유 이미지 갤러리를 만들어서 랩 계정에 연결합니다. 이 랩 계정에서 생성된 모든 랩은 연결된 공유 이미지 갤러리에 액세스할 수 있습니다. 

    ![연결된 이미지 갤러리](./media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    아래쪽 창에 공유 이미지 갤러리의 이미지가 보입니다. 새 갤러리에는 이미지가 없습니다. 이미지를 갤러리에 업로드하면 이 페이지에 표시됩니다.     

    연결된 공유 이미지 갤러리의 모든 이미지는 기본적으로 사용하도록 설정됩니다. 목록에서 이미지를 선택하고 **선택한 이미지 사용** 또는 **선택한 이미지 사용 안 함** 단추를 사용하여 선택한 이미지를 사용하거나 사용하지 않도록 설정할 수 있습니다.

## <a name="attach-an-existing-shared-image-gallery"></a>기존 공유 이미지 갤러리 연결
다음 절차는 기존 공유 이미지 갤러리를 랩 계정에 연결하는 방법을 보여줍니다. 

1. **랩 계정** 페이지의 왼쪽 메뉴에서 **공유 이미지 갤러리** 를 선택하고 도구 모음에서 **연결** 을 선택합니다. 

    ![공유 이미지 갤러리 - 추가 단추](./media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. **기존 Shared Image Gallery 연결** 페이지에서 공유 이미지 갤러리를 선택하고 **확인** 을 선택합니다.

    ![기존 갤러리 선택](./media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 다음과 같은 화면이 표시됩니다. 

    ![목록의 내 갤러리](./media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    이 예제는 공유 이미지 갤러리에 아직 이미지가 없습니다.

    Azure Lab Services ID는 랩에 연결된 공유 이미지 갤러리에 기여자로 추가됩니다. 이렇게 하면 교육자/IT 관리자가 가상 머신 이미지를 공유 이미지 갤러리에 저장할 수 있습니다. 이 랩 계정에서 생성된 모든 랩은 연결된 공유 이미지 갤러리에 액세스할 수 있습니다. 

    연결된 공유 이미지 갤러리의 모든 이미지는 기본적으로 사용하도록 설정됩니다. 목록에서 이미지를 선택하고 **선택한 이미지 사용** 또는 **선택한 이미지 사용 안 함** 단추를 사용하여 선택한 이미지를 사용하거나 사용하지 않도록 설정할 수 있습니다. 

## <a name="detach-a-shared-image-gallery"></a>공유 이미지 갤러리 분리
랩에는 공유 이미지 갤러리를 하나만 연결할 수 있습니다. 다른 공유 이미지 갤러리를 연결하려면 새 갤러리를 연결하기 전에 현재 갤러리를 분리합니다. 공유 이미지 갤러리를 랩에서 분리하려면 도구 모음에서 **분리** 를 선택하고 분리 작업을 확인합니다. 

![공유 이미지 갤러리를 랩 계정에서 분리](./media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>다음 단계
랩 이미지를 공유 이미지 갤러리에 저장하거나 공유 이미지 갤러리의 이미지를 사용하여 VM을 만드는 방법을 알아보려면, [공유 이미지 갤러리를 사용하는 방법](how-to-use-shared-image-gallery.md)을 참조하세요.

랩 컨텍스트 외부의 공유 이미지 갤러리에 사용자 지정 이미지를 가져오기 위한 다른 옵션을 살펴보려면 [사용자 지정 이미지를 만들기 위한 권장 방법](approaches-for-custom-image-creation.md)을 참조하세요.

일반적인 공유 이미지 갤러리에 대한 자세한 내용은 [공유 이미지 갤러리](../virtual-machines/shared-image-galleries.md)를 참조하세요.