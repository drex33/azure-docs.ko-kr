---
title: Azure Lab Services에 대한 가속 랩 계정 설정 가이드
description: 이 가이드를 통해 관리자는 학교 내에서 사용할 랩 계정을 신속하게 설정할 수 있습니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 8b233464da7e6a5c5529e0057240d839282faf46
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177349"
---
# <a name="lab-account-setup-guide"></a>랩 계정 설정 가이드
관리자는 Azure Lab Services 환경을 설정하기 전에 먼저 Azure 구독 내에서 *랩 계정* 을 만들어야 합니다. 랩 계정은 하나 이상의 랩에 대한 컨테이너이며 몇 분 정도면 설정할 수 있습니다.

이 가이드에는 다음 섹션이 포함되어 있습니다.
-  사전 요구 사항
-  랩 계정 설정 계획
-  랩 계정 설정

## <a name="prerequisites"></a>사전 요구 사항
다음 섹션에서는 랩 계정을 설정하기 전에 수행해야 하는 작업을 간략하게 설명합니다.


### <a name="access-your-azure-subscription"></a>Azure 구독에 액세스
랩 계정을 만들려면 학교용으로 이미 설정된 Azure 구독에 대한 액세스 권한이 필요합니다. 학교에 하나 이상의 구독이 있을 수 있습니다. 구독을 사용하여 랩 계정을 비롯한 모든 Azure 리소스 및 서비스의 청구 및 보안을 관리합니다.  Azure 구독은 일반적으로 IT 부서에서 관리합니다.  자세한 내용은 [Azure Lab Services - 관리자 가이드](./administrator-guide.md#subscription)의 "구독" 섹션을 참조하세요.

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>필요한 VM 수 및 VM 크기 예측
학교 랩에 필요한 [VM(가상 머신) 수와 VM 크기](./administrator-guide.md#vm-sizing)를 파악하는 것이 중요합니다. 

랩 및 이미지를 구조화하는 방법에 대한 지침을 보려면 블로그 게시물 [물리적 랩에서 Azure Lab Services로 이동(Moving from a physical lab to Azure Lab Services)](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)을 참조하세요.

랩을 구성하는 방법에 대한 추가 지침은 [Azure Lab Services - 관리자 가이드](./administrator-guide.md#lab)의 "랩" 섹션을 참조하세요.

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>구독 VM 제한 및 지역 VM 용량 이해
랩의 VM 수와 VM 크기를 계산한 후에는 다음을 수행해야 합니다.

- Azure 구독의 용량 한도가 랩에서 사용하려는 VM 수 및 VM 크기를 허용하는지 확인합니다.
- 사용 가능한 VM 용량이 충분한 지역 내에서 랩 계정을 만듭니다.

자세한 내용은 [VM 구독 제한 및 지역 용량](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553)을 참조하세요.

### <a name="decide-how-many-lab-accounts-to-create"></a>만들 랩 계정 수 결정

신속하게 시작하려면 자체 리소스 그룹 내에서 단일 랩 계정을 만듭니다.  나중에 필요에 따라 추가 랩 계정 및 리소스 그룹을 만들 수 있습니다. 예를 들어, 결과적으로는 비용을 명확하게 구분하는 방법으로 학과당 하나의 랩 계정 및 리소스 그룹을 유지할 수 있습니다. 

랩 계정, 리소스 그룹 및 비용 구분에 대한 자세한 내용은 다음을 참조하세요.
- [Azure Lab Services - 관리자 가이드](./administrator-guide.md#resource-group)의 "리소스 그룹" 섹션
- [Azure Lab Services - 관리자 가이드](./administrator-guide.md#lab-account)의 "랩 계정" 섹션 
- [Azure Lab Services에 대한 비용 관리](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>랩 계정 설정 계획

랩 계정 설정을 계획하려면 다음 질문을 고려합니다.

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>랩 계정의 소유자 및 기여자는 누구인가요?

학교 IT 관리자는 일반적으로 랩 계정에 대한 소유자 및 기여자 역할을 담당합니다. 이러한 역할은 랩 계정의 모든 랩에 적용되는 정책을 관리하는 일을 담당합니다. 랩 계정을 만드는 사람은 자동으로 소유자가 됩니다. 구독과 연결된 Azure AD(Azure Active Directory) 테넌트에서 소유자 및 기여자를 추가할 수 있습니다. 

랩 계정 소유자 및 기여자 역할에 대한 자세한 내용은 [Azure Lab Services - 관리자 가이드](./administrator-guide.md#manage-identity)의 "ID 관리" 섹션을 참조하세요.

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

랩 사용자는 Azure Lab Services에서 Azure AD 테넌트를 통해 액세스할 수 있는 VM의 단일 목록만 볼 수 있습니다.

### <a name="who-will-be-allowed-to-create-labs"></a>랩은 누가 만들 수 있나요?

IT 팀 또는 교직원이 랩을 만들도록 선택할 수 있습니다. 랩을 만들려면 랩 계정 내에서 이러한 사용자를 랩 작성자 역할에 할당합니다. 일반적으로 학교 구독과 연결된 Azure AD 테넌트에서 이 역할을 할당합니다. 랩을 만드는 사람은 자동으로 랩 소유자로 할당됩니다.  

랩 작성자 역할에 대한 자세한 내용은 [Azure Lab Services - 관리자 가이드](./administrator-guide.md#manage-identity)의 "ID 관리" 섹션을 참조하세요.

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>랩은 누가 소유하고 관리할 수 있나요?

또한 랩을 만들 수 있는 기능을 제공하지 *않고도* IT 및 교직원이 랩을 소유 및 관리하도록 선택할 수도 있습니다.  이 경우 구독의 Azure AD 테넌트에 있는 사용자에게는 기존 랩에 대한 소유자 또는 기여자가 할당됩니다.  

랩 소유자 및 기여자 역할에 대한 자세한 내용은 [Azure Lab Services - 관리자 가이드](./administrator-guide.md#manage-identity)의 "ID 관리" 섹션을 참조하세요.

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>이미지를 저장하고 랩에서 공유하려고 하나요?

공유 이미지 갤러리는 이미지를 저장하고 공유하는 데 사용할 수 있는 서비스입니다. 동일한 이미지를 사용해야 하는 수업의 경우 랩 작성자는 이미지를 만든 다음, 공유 이미지 갤러리로 내보낼 수 있습니다.  공유 이미지 갤러리로 이미지를 내보낸 후에는 해당 이미지를 사용하여 새 랩을 만들 수 있습니다.

물리적 환경에서 이미지를 만든 다음, 공유 이미지 갤러리로 가져올 수 있습니다. 자세한 내용은 블로그 게시물 [공유 이미지 갤러리에 사용자 지정 이미지 가져오기(Import a custom image to a shared image gallery)](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)를 참조하세요.

공유 이미지 갤러리 서비스를 사용하기로 결정한 경우 공유 이미지 갤러리를 만들거나 랩 계정에 연결해야 합니다. 언제든지 공유 이미지 갤러리를 랩 계정에 연결할 수 있으므로 이 결정을 나중에 해도 됩니다.  

자세한 내용은 다음을 참조하세요.
- [Azure Lab Services - 관리자 가이드](./administrator-guide.md#shared-image-gallery)의 "공유 이미지 갤러리" 섹션
- [Azure Lab Services - 관리자 가이드](./administrator-guide.md#pricing)의 "가격 책정" 섹션

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>랩에서는 Azure Marketplace의 어떤 이미지를 사용하나요?

Azure Marketplace는 랩 작성자가 랩을 만들 때 사용할 수 있도록 수백 개의 이미지를 제공합니다. 일부 이미지에는 랩에 이미 필요한 모든 항목이 포함되어 있을 수 있습니다. 다른 경우에는 이미지를 시작 지점으로 사용하고, 랩 작성자가 추가 애플리케이션 또는 도구를 설치하여 이미지를 사용자 지정할 수 있습니다.

필요한 이미지를 잘 모를 경우 나중에 다시 돌아와 사용하도록 설정할 수 있습니다. 사용할 수 있는 이미지를 확인하는 가장 좋은 방법은 먼저 랩 계정을 만드는 것입니다. 그러면 사용 가능한 이미지와 해당 내용 목록을 검토할 수 있는 액세스 권한이 제공됩니다.  

자세한 내용은 [랩 작성자가 사용할 수 있는 Azure Marketplace 이미지 지정](./specify-marketplace-images.md)을 참조하세요.
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>랩 VM에서 다른 Azure 또는 온-프레미스 리소스에 액세스해야 하나요?

랩 계정을 설정하는 경우 가상 네트워크에 랩 계정을 피어링할 수도 있습니다.  가상 네트워크와 랩 계정은 동일한 지역에 있어야 합니다.  가상 네트워크에 피어링해야 하는지 여부를 결정하려면 다음 시나리오를 고려하세요.

- **라이선스 서버에 대한 액세스 권한**
  
   Azure Marketplace 이미지를 사용하는 경우 운영 체제 라이선스의 비용은 랩 서비스 가격에 포함됩니다. 그러나 운영 체제 자체에 대한 라이선스를 제공할 필요는 없습니다. 설치된 추가 소프트웨어 및 애플리케이션의 경우 적절한 라이선스를 제공해야 합니다.  라이선스 서버에 액세스하려면
   - 온-프레미스 라이선스 서버에 연결하도록 선택할 수 있습니다.  온-프레미스 라이선스 서버에 연결하려면 추가 설정이 필요합니다.
   - 더 빠르게 설정할 수 있는 또 다른 옵션은 Azure VM에서 호스트하는 라이선스 서버를 만드는 것입니다.  Azure VM은 랩 계정에 피어링하는 가상 네트워크 내에 있습니다.

- **파일 공유 또는 데이터베이스 등의 다른 온-프레미스 리소스에 대한 액세스**

   일반적으로 사이트 간 가상 네트워크 게이트웨이를 사용하여 온-프레미스 리소스에 대한 액세스를 제공하는 가상 네트워크를 만듭니다. 이러한 유형의 환경을 설정하는 데는 시간이 추가로 소요됩니다.

- **가상 네트워크 외부에 있는 다른 Azure 리소스에 대한 액세스**

   가상 네트워크 내에서 보호되지 *않는* Azure 리소스에 액세스해야 하는 경우 피어링을 수행하지 않고도 퍼블릭 인터넷을 통해 액세스할 수 있습니다.

   가상 네트워크에 대한 자세한 내용은 다음을 참조하세요.
   - [Azure Lab Services의 아키텍처 기본 사항](./classroom-labs-fundamentals.md#virtual-network)의 "가상 네트워크" 섹션
   - [Azure Lab Services에서 랩의 네트워크를 피어 가상 네트워크에 연결](./how-to-connect-peer-virtual-network.md)
   - [Azure Lab Services에서 공유 리소스를 사용하여 랩 만들기](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>랩 계정 설정

계획이 끝났으면 랩 계정을 설정할 준비가 되었습니다. [Teams에서 Azure Lab Services](./lab-services-within-teams-overview.md)를 설정하는 것과 동일한 단계를 적용할 수 있습니다.

1. **랩 계정을 만듭니다**. 지침은 [랩 계정 만들기](./tutorial-setup-lab-account.md#create-a-lab-account)를 참조하세요.
   
    명명 규칙에 대한 내용은 [Azure Lab Services - 관리자 가이드](./administrator-guide.md#naming)의 "이름 지정" 섹션을 참조하세요.

1. **랩 작성자 역할에 사용자를 추가합니다**. 지침은 [랩 작성자 역할에 사용자 추가](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)를 참조하세요.

1. **피어 가상 네트워크에 연결합니다**. 지침은 [랩 네트워크를 피어 가상 네트워크에 연결](./how-to-connect-peer-virtual-network.md)을 참조하세요.

   [랩 VM 주소 범위를 구성](./how-to-configure-lab-accounts.md)하기 위한 지침을 참조해야 할 수도 있습니다.

1. **이미지를 사용하도록 설정하고 검토합니다**. 지침은 [랩 작성자가 사용할 수 있는 Azure Marketplace 이미지 지정](./specify-marketplace-images.md)을 참조하세요.

   각 Azure Marketplace 이미지의 내용을 검토하려면 이미지 이름을 선택합니다. 예를 들어, 다음 스크린샷은 Ubuntu Data Science VM 이미지에 대한 세부 정보를 보여 줍니다.

   ![Azure Marketplace에서 검토할 수 있는 이미지 목록의 스크린샷](./media/setup-guide/review-marketplace-images.png)

   공유 이미지 갤러리가 랩 계정에 연결되어 있으며 랩 작성자가 사용자 지정 이미지를 공유할 수 있도록 설정하려면 다음 스크린샷에 표시된 것과 비슷한 단계를 완료합니다.

   ![공유 이미지 갤러리에서 사용하도록 설정된 사용자 지정 이미지 목록의 스크린샷](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>다음 단계

랩 설정 및 관리에 대한 자세한 내용은 다음을 참조하세요.

- [랩 계정 관리](how-to-manage-lab-accounts.md)  
- [랩 설정 가이드](setup-guide.md)
