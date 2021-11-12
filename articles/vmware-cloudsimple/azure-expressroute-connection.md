---
title: Azure VMware Solution by CloudSimple - ExpressRoute를 사용하여 프라이빗 클라우드를 Azure 네트워크에 연결
description: ExpressRoute를 사용하여 CloudSimple 프라이빗 클라우드 환경을 Azure 가상 네트워크에 연결하는 방법 설명
author: suzizuber
ms.author: v-szuber
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 76f1a861ff33f852a275e38c83e9320576551b31
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322686"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>ExpressRoute를 사용하여 CloudSimple 프라이빗 클라우드 환경을 Azure 가상 네트워크에 연결

Azure ExpressRoute를 사용하여 CloudSimple 프라이빗 클라우드를 Azure 가상 네트워크에 연결할 수 있습니다.  이 연결 방법은 대역폭이 높고 대기 시간이 짧기 때문에 프라이빗 클라우드 환경의 Azure 구독에서 실행되는 서비스에 액세스할 수 있습니다.

가상 네트워크 연결을 통해 다음을 수행할 수 있습니다.

* 프라이빗 클라우드의 가상 머신에 대한 백업 대상으로 Azure를 사용합니다.
* Azure 구독에 KMS 서버를 배포하여 프라이빗 클라우드 vSAN 데이터 저장소를 암호화합니다.
* 애플리케이션 및 데이터베이스 계층이 프라이빗 클라우드에서 실행되는 동안 애플리케이션의 웹 계층은 퍼블릭 클라우드에서 실행되는 하이브리드 애플리케이션을 사용합니다.

![가상 네트워크에 대한 Azure ExpressRoute 연결](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>가상 네트워크 연결 설정

프라이빗 클라우드에 대한 가상 네트워크 연결을 설정하려면 권한 부여 키, 피어 회로 URI, Azure 구독에 대한 액세스 권한이 필요합니다. 이 정보는 CloudSimple 포털의 가상 네트워크 연결 페이지에서 확인할 수 있습니다. 지침은 [Azure 가상 네트워크의 피어링 정보를 CloudSimple 연결로 가져오기](virtual-network-connection.md)를 참조하세요. 정보를 얻는 중에 문제가 있는 경우 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지원 요청</a>을 제출합니다.

> [!TIP]
> Azure 가상 네트워크, 게이트웨이 서브넷 및 가상 네트워크 게이트웨이가 이미 있는 경우 4단계부터 수행합니다.

1. Azure 구독에 가상 네트워크를 만든 후, 선택한 주소 공간이 프라이빗 클라우드의 주소 공간과 다른지 확인합니다.  Azure 가상 네트워크가 이미 있는 경우 기존 네트워크를 사용할 수 있습니다.  자세한 내용은 [Azure Portal을 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md)를 참조하세요.
2. Azure 가상 네트워크에 게이트웨이 서브넷을 만듭니다.  Azure 가상 네트워크에 게이트웨이 서브넷이 이미 있는 경우 기존 게이트웨이 서브넷을 사용할 수 있습니다. 자세한 내용은 [게이트웨이 서브넷 만들기](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)를 참조하세요.
3. 가상 네트워크에서 가상 네트워크 게이트웨이를 만듭니다.  기존 가상 네트워크 게이트웨이가 있는 경우 기존 가상 네트워크 게이트웨이를 사용할 수 있습니다. 자세한 내용은 [가상 네트워크 게이트웨이 만들기](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)를 참조하세요.
4. [가상 네트워크를 다른 구독의 회로에 연결](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)에 설명된 대로 권한 부여 키를 사용하여 가상 네트워크와 프라이빗 클라우드 간에 연결을 만듭니다.

> [!WARNING]
> 기존 가상 네트워크 게이트웨이를 사용 중이고 해당 게이트웨이가 CloudSimple ExpressRoute 회로와 동일한 위치에 대한 ExpressRoute 연결을 사용하는 경우 연결이 설정되지 않습니다.  새 가상 네트워크를 만들고 이전 단계를 따릅니다.

## <a name="test-the-virtual-network-connection"></a>가상 네트워크 연결 테스트

연결을 만든 후 **설정** 에서 **속성** 을 선택하여 연결 상태를 확인할 수 있습니다.  상태 및 프로비전 상태가 **성공** 으로 표시되어야 합니다.

![연결 상태](media/azure-expressroute-connection.png)

가상 네트워크 연결을 테스트하려면 다음을 수행합니다.

1. Azure 구독에서 가상 머신을 만듭니다.
2. 프라이빗 클라우드 vCenter의 IP 주소를 찾습니다(시작 이메일 참조).
3. Azure 가상 네트워크에서 만든 가상 머신의 클라우드 vCenter를 ping합니다.
4. 프라이빗 클라우드 vCenter에서 실행되는 가상 머신의 Azure 가상 머신을 ping합니다.

연결을 설정하는 중에 문제가 있는 경우 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지원 요청</a>을 제출합니다.
