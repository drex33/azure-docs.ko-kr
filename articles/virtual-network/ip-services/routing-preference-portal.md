---
title: 공용 IP 주소에 대한 라우팅 기본 설정 구성 - Azure Portal
description: 인터넷 트래픽 라우팅 기본 설정을 사용하여 공용 IP를 만드는 방법 알아보기
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: 821a8e5f3211f010489717447b733c17ee61c249
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372832"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Azure Portal를 사용하여 공용 IP 주소에 대한 라우팅 기본 설정 구성

이 문서에서는 공용 IP 주소에 대해 ISP 네트워크(**인터넷** 옵션)를 통해 [라우팅 기본 설정](routing-preference-overview.md)을 구성하는 방법을 보여줍니다. 공용 IP 주소를 만든 후 인터넷에 대한 인바운드 및 아웃바운드 트래픽을 위해 다음 Azure 리소스와 연결할 수 있습니다.

* 가상 머신
* 가상 머신 크기 집합
* AKS(Azure Kubernetes Service)
* 인터넷 연결 부하 분산 장치
* Application Gateway
* Azure Firewall

기본적으로 공용 IP 주소에 대한 라우팅 기본 설정은 모든 Azure 서비스에 대해 Microsoft 글로벌 네트워크로 설정되며 모든 Azure 서비스에 연결할 수 있습니다.

Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 지금 만드세요.

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>라우팅 기본 설정을 사용하여 공용 IP 주소 만들기
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **리소스 만들기** 를 선택합니다.
3. 검색 상자에 *공용 IP 주소* 를 입력합니다.
3. 검색 결과에서 **공용 IP 주소** 를 선택합니다. 그런 다음, **공용 IP 주소** 페이지에서 **만들기** 를 선택합니다.
1. SKU의 경우 **표준** 을 선택합니다.
1. **라우팅 기본 설정** 에서 **인터넷** 을 선택합니다.

      ![공용 IP 주소 만들기](./media/routing-preference-portal/public-ip-new.png)
1. **IPv4 IP 주소 구성** 섹션에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | 구독을 선택합니다.|
    | Resource group | **새로 만들기** 를 선택하고 *RoutingPreferenceResourceGroup* 을 입력한 다음, **확인** 을 선택합니다. |
    | 위치 | **미국 동부** 를 선택합니다.|
    | 가용성 영역 | 기본값 - **영역 중복** 을 유지합니다. |
1. **만들기** 를 선택합니다.

    > [!NOTE]
    > 공용 IP 주소는 IPv4 또는 IPv6 주소를 사용하여 만들어집니다. 그러나 라우팅 기본 설정은 현재 IPV4만 지원합니다.

위에서 만든 공용 IP 주소를 [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신과 연결할 수 있습니다. 자습서 페이지의 CLI 섹션을 사용합니다. [공용 IP 주소를 가상 머신에 연결](../../virtual-network/associate-public-ip-address-vm.md#azure-cli)하여 공용 IP를 VM에 연결합니다. 위에서 만든 공용 IP 주소를 부하 분산 장치 **프런트 엔드** 구성에 할당하여 [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)와 연결할 수도 있습니다. 공용 IP 주소는 부하가 분산된 VIP(가상 IP 주소)로 사용됩니다.

## <a name="next-steps"></a>다음 단계
- [라우팅 기본 설정을 사용하는 공용 IP](routing-preference-overview.md)에 대해 자세히 알아봅니다.
- [VM에 대한 라우팅 기본 설정을 구성합니다](../../virtual-network/tutorial-routing-preference-virtual-machine-portal.md).
- [PowerShell을 사용하여 공용 IP 주소에 대한 라우팅 기본 설정을 구성합니다](routing-preference-powershell.md).
- Azure에서 [공용 IP 주소](public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.