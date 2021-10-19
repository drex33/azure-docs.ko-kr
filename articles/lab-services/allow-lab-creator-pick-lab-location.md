---
title: 랩 작성자가 Azure Lab Services에서 위치를 선택하도록 허용
description: 이 문서에서는 랩 작성자가 랩 위치를 선택할 수 있도록 랩 계정 관리자가 허용하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: f9dbafe6b297d7b94b1d1ec5eb419345cb140457
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177254"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>랩 작성자가 Azure Lab Services에서 랩 위치를 선택하도록 허용
Azure Lab Services에서 랩 계정 소유자는 랩 작성자(교육자)가 만든 랩의 위치를 선택할 수 있습니다. 이 위치는 랩 계정의 위치와 다를 수 있습니다. 위치는 Azure 지역 그룹입니다. 예를 들어 미국 위치는 미국 동부, 미국 서부 등의 지역 그룹입니다. 

랩 계정 소유자는 랩 계정을 만들 때 및 랩 계정을 만든 후(또는 기존 랩 계정) **랩 작성자가 랩 위치를 선택하도록 허용** 옵션을 선택할 수 있습니다. 

## <a name="at-the-time-of-lab-account-creation"></a>랩 계정 생성 시
랩 계정을 만들 때 첫 번째 화면(**기본** 탭)에 이 옵션이 표시됩니다. 

![랩 생성 시 옵션을 사용하도록 설정](./media/allow-lab-creator-pick-lab-location/create-lab-account.png)

**고급** 탭에서 랩 계정에 대한 피어 가상 네트워크를 선택하는 경우에는 이 옵션을 사용할 수 없습니다.  

![피어 가상 네트워크를 사용하는 경우](./media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>랩 계정을 만든 후
랩 계정을 만든 후 다음 단계를 수행하여 이 옵션을 사용하거나 사용하지 않도록 설정할 수 있습니다. 

1. **랩 계정** 페이지의 왼쪽 메뉴에서 **랩 설정** 을 선택합니다.
2. 랩 작성자가 랩 위치를 선택할 수 있도록 허용하려면 **랩 작성자가 랩 위치를 선택하도록 허용**  옵션을 선택합니다. 이 옵션이 사용하지 않도록 설정된 경우 랩 계정이 있는 동일한 위치에 랩이 자동으로 생성됩니다. 
    
    **피어 가상 네트워크** 필드의 가상 네트워크를 선택하면 이 필드를 사용할 수 없습니다. 랩 계정의 랩은 피어 가상 네트워크의 리소스에 액세스하기 위해 랩 계정과 동일한 지역에 있어야 하기 때문입니다. 
1. 도구 모음에서 **저장** 을 선택합니다. 

    ![랩 설정](./media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>가상 네트워크 및 위치 선택은 허용되지 않습니다.
이 시나리오에서는 **랩 작성자가 랩 위치를 선택하도록 허용** 옵션을 사용하도록 설정하지 않았습니다. 

![랩 위치 없음](./media/allow-lab-creator-pick-lab-location/lab-no-location.png)

그러면 랩 작성자(교육자)에게 랩 위치를 선택하는 옵션이 표시되지 않습니다. 사용할 수 있는 모든 크기 옵션에 대한 시간당 가격이 표시됩니다. 랩을 만드는 경우 랩은 랩 계정이 있는 Azure 지역과 동일한 위치에 있는 Azure 지역에 생성됩니다. 예를 들어 랩 계정이 **미국 서부** 에 있는 경우 랩이 **미국 중남부** 에 생성될 수는 있지만 **캐나다 동부** 에는 생성되지 않습니다. 해당 위치에 있는 항목 외에는 지역에 대한 어떤 항목도 보장되지 않습니다. 현재 크기가 제한되어 있는 경우 일반적으로 랩 작성자에게는 크기를 볼 수 있는 확인란이 표시됩니다. 일반적으로 확인란이 지원되지만 지금은 제공되지 않습니다. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>가상 네트워크에서 및 위치 선택이 허용되지 않음
이 시나리오에서는 랩 계정에 대한 피어 가상 네트워크를 선택했으므로 **랩 작성자가 랩 위치를 선택하도록 허용** 옵션을 사용할 수 없습니다. 따라서 랩 작성자에게 이전 옵션과 동일한 화면이 표시됩니다. 모든 VM은 가상 네트워크와 동일한 Azure 지역에 있어야 하기 때문에 가상 네트워크가 있는 동일한 Azure 지역에 랩이 생성됩니다. 특정 지역이 크기가 제한되는 경우 크기는 사용할 수 없음으로 표시됩니다. 

## <a name="location-selection-is-enabled"></a>위치 선택이 사용하도록 설정됨
**랩 작성자가 랩 위치를 선택하도록 허용** 을 선택하면 랩을 만들 때 랩 작성자(교육자)에게 위치를 선택하는 옵션이 표시됩니다. 

![랩 위치 선택](./media/allow-lab-creator-pick-lab-location/location-selection.png)

랩 작성자는 해당 크기의 모든 위치에 대한 가격 범위를 확인하고 위치를 선택할 수 있습니다. 랩은 해당 위치에 매핑되는 모든 Azure 지역에서 생성됩니다.

위치가 제한되는 경우에는 기본적으로 목록에 표시되지 않습니다. 드롭다운 목록을 확장하고 **이 크기에 사용할 수 없는 위치 표시** 를 선택합니다. 

![사용할 수 없는 위치 표시](./media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Cost
이전에는 랩에 대해 선택한 VM 크기를 기준으로 가격이 책정되었습니다. 이제 가격은 OS(운영 체제), 크기 및 위치의 조합을 기반으로 합니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩의 네트워크를 피어 가상 네트워크에 연결](how-to-connect-peer-virtual-network.md)
- [공유 이미지 갤러리를 랩에 연결](how-to-attach-detach-shared-image-gallery.md)
- [사용자를 랩 소유자로 추가](how-to-add-user-lab-owner.md)
- [랩에 대한 방화벽 설정 보기](how-to-configure-firewall-settings.md)
- [랩에 대한 기타 설정 구성](how-to-configure-lab-accounts.md)