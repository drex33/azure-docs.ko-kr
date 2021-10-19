---
title: Azure Lab Services에서 랩에 마켓플레이스 이미지 지정
description: 이 문서에서는 랩 작성자가 Azure Lab Services의 랩 계정에서 랩을 만드는 데 사용할 수 있는 마켓플레이스 이미지를 지정하는 방법을 알려줍니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: ccd304d1a12d38ae18891f9400beaf8c9d90b889
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177653"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>랩 작성자에 사용할 수 있는 Marketplace 이미지 지정
랩 계정 소유자는 랩 작성자가 랩 계정에서 랩을 만들 때 사용할 수 있는 Marketplace 이미지를 지정할 수 있습니다. 

## <a name="select-images-available-for-labs"></a>랩에 사용할 수 있는 이미지 선택
왼쪽 메뉴에서 **Marketplace 이미지** 를 선택합니다. 기본적으로 이미지의 전체 목록이 표시됩니다(사용 및 사용 안 함으로 설정된 이미지 모두 포함). 맨 위의 드롭다운 목록에서 **Enabled only**(사용으로 설정된 이미지만)/**Disabled only**(사용 안 함으로 설정된 이미지만) 옵션을 선택하여 사용/사용 안 함으로 설정된 이미지만 표시하도록 목록을 필터링할 수 있습니다. 
    
![Marketplace 이미지 페이지](./media/tutorial-setup-lab-account/marketplace-images-page.png)

다음 조건을 만족하는 Marketplace 이미지만 목록에 표시됩니다.
    
- 단일 VM을 만듭니다.
- Azure Resource Manager를 사용하여 VM을 프로비전합니다.
- 추가 라이선스 플랜을 구입하지 않아도 됩니다.

## <a name="disable-images-for-a-lab"></a>랩에 이미지 사용 안 함 
랩에 단일 이미지를 사용하지 않으려면 마지막 열에서 **... (말줄임표)** 를 선택한 다음 **이미지 사용 안 함** 을 선택합니다. 

![하나의 이미지를 사용하지 않도록 설정](./media/tutorial-setup-lab-account/disable-one-image.png) 

또는 이미지 이름 앞에 있는 확인란을 선택하고 도구 모음에서 **선택한 이미지 사용 안 함** 을 선택합니다. 

동시에 여러 이미지를 사용하지 않으려면 이미지 이름 앞의 확인란을 선택하고 도구 모음에서 **선택한 이미지 사용 안 함** 을 선택합니다. 

![여러 이미지를 사용하지 않도록 설정](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>랩에 이미지 사용
비활성화된 이미지를 사용하려면 마지막 열에서 **... (말줄임표)** 를 선택한 다음 **이미지 사용** 을 선택합니다. 또는 이미지 이름 앞에 있는 확인란을 선택하고 도구 모음에서 **선택한 이미지 사용** 을 선택합니다. 

동시에 여러 이미지를 사용하지 않으려면 이미지 이름 앞에 있는 확인란을 선택하고 도구 모음에서 **선택한 이미지 사용** 을 선택합니다. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>랩 생성 시 이미지 사용
랩을 만들 때 더 많은 이미지를 사용하도록 설정할 수 있습니다. 

1. [Azure Lab Services 웹 사이트](https://labs.azure.com) 에 **랩 계정 소유자** 자격 증명을 사용하여 로그인합니다
2. 기본 가상 머신 이미지 또는 아래쪽 화살표를 선택합니다. 
3. **더 많은 이미지 옵션 사용** 을 선택합니다. 

    ![더 많은 이미지 옵션 사용](./media/specify-marketplace-images/enable-more-images-menu.png)
4. 이전 섹션의 지침에 따라 선택한 이미지를 사용하도록 설정합니다. 
5. 이전 단계에서 선택한 이미지를 보려면 **새 랩** 창을 닫은 후 다시 열어야 할 수도 있습니다. 



## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 랩에 액세스](how-to-use-classroom-lab.md)
