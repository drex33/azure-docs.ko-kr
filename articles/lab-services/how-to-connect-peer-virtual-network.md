---
title: Azure Lab Services에서 피어 네트워크에 연결 | Microsoft Docs
description: 랩 네트워크를 다른 네트워크와 피어로 연결하는 방법에 대해 알아봅니다. 예를 들어 Azure에서 랩의 가상 네트워크와 온-프레미스 조직/대학 네트워크를 연결합니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: dd549061c5b40447afa11b82dfcfdb07d6b635ab
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180707"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Azure Lab Services에서 랩의 네트워크를 피어 가상 네트워크에 연결

이 문서에서는 랩 네트워크를 다른 네트워크와 피어링하는 방법에 대한 정보를 제공합니다.

## <a name="overview"></a>개요

가상 네트워크 피어링을 사용하면 Azure 가상 네트워크를 원활하게 연결할 수 있습니다. 가상 네트워크가 피어링되면 연결성을 위해 하나로 표시됩니다. 트래픽이 개인 IP 주소를 통해 동일한 가상 네트워크에 있는 가상 머신 간에 라우팅되는 것과 마찬가지로 피어링된 가상 네트워크에 있는 가상 머신 간의 트래픽은 Microsoft 백본 인프라를 통해 라우팅됩니다. 자세한 내용은 [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)을 참조하세요.

다음을 포함하여 몇 가지 시나리오에서 랩의 네트워크를 피어 가상 네트워크와 연결해야 할 수 있습니다.

- 랩의 가상 머신에는 라이선스를 획득하기 위해 온-프레미스 라이선스 서버에 연결하는 소프트웨어가 있습니다.
- 랩의 가상 머신은 대학 네트워크 공유의 데이터 세트(또는 다른 파일)에 대한 액세스 권한이 있어야 합니다.

특정 온-프레미스 네트워크는 [ExpressRoute](../expressroute/expressroute-introduction.md) 또는 [Virtual Network Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 통해 Azure Virtual Network에 연결됩니다. 이러한 서비스는 Azure Lab Services 외부에서 설정해야 합니다. ExpressRoute를 사용하여 온-프레미스 네트워크를 Azure에 연결하는 방법에 대한 자세한 정보는 [ExpressRoute 개요](../expressroute/expressroute-introduction.md)를 참조하세요. Virtual Network Gateway를 사용하는 온-프레미스 연결의 경우 게이트웨이, 지정된 가상 네트워크 및 랩 계정이 모두 동일한 지역에 있어야 합니다.

> [!NOTE]
> 랩 계정과 피어링할 Azure Virtual Network를 만들 때는 가상 네트워크의 지역이 랩을 만들 때 영향을 주는 방식을 이해하는 것이 중요합니다.  자세한 내용은 [지역\위치](./administrator-guide.md#regionslocations)에서 관리자 가이드의 섹션을 참조하세요.

> [!NOTE]
> 학교에서 [CIPA(Children's Internet Protection Act)](https://www.fcc.gov/consumers/guides/childrens-internet-protection-act) 준수와 같은 콘텐츠 필터링을 수행해야 하는 경우 타사 소프트웨어를 사용해야 합니다.  자세한 내용은 [Lab Services를 통해 콘텐츠 필터링](./administrator-guide.md#content-filtering)에 대한 지침을 참조하세요.

## <a name="configure-at-the-time-of-lab-account-creation"></a>랩 계정 생성 시 구성

새 [랩 계정을 생성](tutorial-setup-lab-account.md)하는 동안 **고급** 탭의 **피어 가상 네트워크** 드롭다운 목록에 표시되는 기존 가상 네트워크를 선택할 수 있습니다.  이 목록에는 랩 계정과 동일한 지역에 있는 가상 네트워크만 표시됩니다. 선택한 가상 네트워크는 랩 계정에서 만든 랩에 연결(피어링)됩니다.  이 변경 작업을 수행한 후 생성된 랩의 모든 가상 머신은 피어링된 가상 네트워크의 리소스에 액세스할 수 있게 됩니다.

![피어링할 VNet 선택](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>주소 범위

랩의 가상 머신에 대한 **주소 범위** 를 제공하는 옵션도 있습니다.  **주소 범위** 속성은 랩에서 **피어 가상 네트워크** 를 사용하도록 설정한 경우에만 적용됩니다. 주소 범위가 제공되면 랩 계정에 속하는 랩의 모든 가상 머신이 해당 주소 범위에서 생성됩니다. 주소 범위는 CIDR 표기법(예: 10.20.0.0/20)에 따라야 하고 기존 주소 범위와 겹치지 않아야 합니다.  주소 범위를 제공할 때는 생성되는 *랩* 수를 고려하여 이를 수용하는 주소 범위를 제공하는 것이 중요합니다. Lab Services는 랩당 최대 512대의 가상 머신을 가정합니다.  예를 들어 '/23'인 IP 범위는 랩을 하나만 만들 수 있습니다.  '/21'인 범위에서는 네 개의 랩을 만들 수 있습니다.

**주소 범위** 를 지정하지 않으면 Lab Services에서 사용자의 가상 네트워크와 피어링할 가상 네트워크를 만들 때 Azure에서 지정한 기본 주소 범위를 사용합니다.  범위는 대개 10.x.0.0/16과 같습니다.  이로 인해 IP 범위가 중복될 수 있으므로 랩 설정에서 주소 범위를 지정하거나 피어링되는 사용자 가상 네트워크의 주소 범위를 확인해야 합니다.

> [!NOTE]
> 랩 계정이 가상 네트워크에 피어링되지만 IP 주소 범위가 너무 좁으면 랩 생성이 실패할 수 있습니다. 랩 계정에 너무 많은 랩이 있는 경우(각 랩에서 512개 주소를 사용하는 경우) 주소 범위에서 공간이 부족할 수 있습니다. 
> 
> 랩 생성이 실패하는 경우 랩 계정 소유자/관리자에게 연락하여 주소 범위를 증가시키도록 요청합니다. 관리자는 [랩 계정의 VM에 대해 주소 범위 지정](#specify-an-address-range-for-vms-in-the-lab-account) 섹션에 설명된 단계를 사용하여 주소 범위를 늘릴 수 있습니다. 

## <a name="configure-after-the-lab-account-is-created"></a>랩 계정 생성 후 구성

랩 계정 생성 시 피어 네트워크를 설정하지 않은 경우 **랩 계정** 페이지의 **랩 구성** 탭에서 동일한 속성을 사용하도록 설정할 수 있습니다. 이 설정에 대한 변경 내용은 변경 후에 생성된 랩에만 적용됩니다. 이미지에서 볼 수 있듯이 랩 계정에서 랩에 대한 **피어 가상 네트워크** 를 사용하거나 사용하지 않도록 설정할 수 있습니다.

![랩을 만든 후 VNet 피어링 사용 또는 사용 안 함](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

**피어 가상 네트워크** 필드에 대해 가상 네트워크를 선택하면 **랩 작성자가 랩 위치를 선택하도록 허용** 옵션이 사용하지 않도록 설정됩니다. 랩 계정의 랩은 피어 가상 네트워크의 리소스와 연결하기 위해 랩 계정과 동일한 지역에 있어야 하기 때문입니다.

> [!IMPORTANT]
> 피어링된 가상 네트워크 설정은 변경 후에 생성된 랩에만 적용되며 기존 랩에는 적용되지 않습니다.


## <a name="specify-an-address-range-for-vms-in-the-lab-account"></a>랩 계정의 VM에 대해 주소 범위 지정
다음 절차에는 랩의 VM에 대한 주소 범위를 지정하는 단계가 나와 있습니다. 이전에 지정한 범위를 업데이트하는 경우 수정된 주소 범위는 변경 후에 생성된 VM에만 적용됩니다. 

주소 범위를 지정할 때 유의해야 하는 몇 가지 제한 사항이 있습니다. 

- 접두사는 23보다 작거나 같아야 합니다. 
- 가상 네트워크가 랩 계정에 피어링되는 경우 제공된 주소 범위는 피어링된 가상 네트워크의 주소 범위와 겹칠 수 없습니다.

1. **랩 계정** 페이지의 왼쪽 메뉴에서 **랩 설정** 을 선택합니다.
2. **주소 범위** 필드에 랩에서 생성할 VM의 주소 범위를 지정합니다. 주소 범위는 CIDR(Classless Inter-Domain Routing) 표기법을 따라야 합니다(예: 10.20.0.0/23). 랩의 가상 머신은 이 주소 범위에서 만들어집니다.
3. 도구 모음에서 **저장** 을 선택합니다. 

    ![주소 범위 구성](./media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요.

- [랩 작성자가 랩 위치를 선택하도록 허용](allow-lab-creator-pick-lab-location.md)
- [공유 이미지 갤러리를 랩에 연결](how-to-attach-detach-shared-image-gallery.md)
- [사용자를 랩 소유자로 추가](how-to-add-user-lab-owner.md)
- [랩에 대한 방화벽 설정 보기](how-to-configure-firewall-settings.md)
- [랩에 대한 기타 설정 구성](how-to-configure-lab-accounts.md)