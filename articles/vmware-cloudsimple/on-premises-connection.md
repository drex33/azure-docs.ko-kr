---
title: Azure VMware Solution by CloudSimple - ExpressRoute를 사용한 온-프레미스 연결
description: CloudSimple 지역 네트워크에서 ExpressRoute를 사용하여 온-프레미스 연결을 요청하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0c80e9e96fae2247d7e49f58bbad3918a4912d32
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305620"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>ExpressRoute를 사용하여 온-프레미스에서 CloudSimple로 연결

외부 위치(예: 온-프레미스)에서 Azure로의 Azure ExpressRoute 연결이 이미 있는 경우 이를 CloudSimple 환경에 연결할 수 있습니다. 두 ExpressRoute 회로를 서로 연결할 수 있는 Azure 기능을 통해 이를 수행할 수 있습니다. 이 방법은 두 환경 간에 대기 시간이 짧은 고대역폭의 안전한 프라이빗 연결을 설정합니다.

[![온-프레미스 ExpressRoute 연결 - Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>시작하기 전에

온-프레미스에서 Global Reach 연결을 설정하려면 **/29** 네트워크 주소 블록이 필요합니다.  /29 주소 공간은 ExpressRoute 회로 간의 전송 네트워크에 사용됩니다.  전송 네트워크는 Azure 가상 네트워크, 온-프레미스 네트워크 또는 CloudSimple 프라이빗 클라우드 네트워크와 겹치지 않아야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 회로와 CloudSimple 프라이빗 클라우드 네트워크 간에 연결을 설정하려면 Azure ExpressRoute 회로가 필요합니다.
* 사용자에게 ExpressRoute 회로에서 권한 부여 키를 만들 수 있는 권한이 있어야 합니다.

## <a name="scenarios"></a>시나리오

온-프레미스 네트워크를 프라이빗 클라우드 네트워크에 연결하면 다음과 같은 시나리오를 포함한 다양한 방법으로 프라이빗 클라우드를 사용할 수 있습니다.

* 사이트 간 VPN 연결을 만들지 않고 프라이빗 클라우드 네트워크에 액세스합니다.
* 프라이빗 클라우드에서 ID 원본으로 온-프레미스 Active Directory를 사용합니다.
* 온-프레미스에서 실행되는 가상 머신을 프라이빗 클라우드로 마이그레이션합니다.
* 재해 복구 솔루션의 일부로 프라이빗 클라우드를 사용합니다.
* 프라이빗 클라우드 워크로드 VM에서 온-프레미스 리소스를 사용합니다.

## <a name="connecting-expressroute-circuits"></a>ExpressRoute 회로 연결

ExpressRoute 연결을 설정하려면 ExpressRoute 회로에서 권한 부여를 만들고 CloudSimple에 권한 부여 정보를 제공해야 합니다.


### <a name="create-expressroute-authorization"></a>ExpressRoute 권한 부여 사용

1. Azure Portal에 로그인합니다.

2. 위쪽 검색 창에서 **ExpressRoute 회로** 를 검색하고 **서비스** 아래에서 **ExpressRoute 회로** 를 클릭합니다.
    [![ExpressRoute 회로](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. CloudSimple 네트워크에 연결하려는 ExpressRoute 회로를 선택합니다.

4. ExpressRoute 페이지에서 **권한 부여** 를 클릭하고 권한 부여 이름을 입력한 다음, **저장** 을 클릭합니다.
    [![ExpressRoute 회로 권한 부여](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. 복사 아이콘을 클릭하여 리소스 ID와 권한 부여 키를 복사합니다. ID와 키를 텍스트 파일에 붙여 넣습니다.
    [![ExpressRoute 회로 권한 부여 복사](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **리소스 ID** 는 UI에서 복사해야 하며 지원에 제공할 때 ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` 형식이어야 합니다.

6. 연결을 만들려면 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지원</a>팀에 티켓을 제출합니다.
    * 문제 유형: **기술**
    * 구독: **CloudSimple 서비스가 배포된 구독**
    * 서비스: **VMware Solution by CloudSimple**
    * 문제 유형: **서비스 요청**
    * 문제 하위 유형: **온-프레미스에 대한 ExpressRoute 연결 만들기**
    * 세부 정보 창에서 복사하여 저장한 리소스 ID와 권한 부여 키를 제공합니다.
    * 전송 네트워크에 대한 /29 네트워크 주소 공간을 제공합니다.
    * ExpressRoute를 통해 기본 경로를 보내고 있습니까?
    * 프라이빗 클라우드 트래픽이 ExpressRoute를 통해 보낸 기본 경로를 사용해야 합니까?

    > [!IMPORTANT]
    > 기본 경로를 보내면 온-프레미스 인터넷 연결을 사용하여 프라이빗 클라우드에서 모든 인터넷 트래픽을 보낼 수 있습니다.  프라이빗 클라우드에 구성된 기본 경로를 사용하지 않고 온-프레미스 연결 기본 경로를 사용하려면 지원 티켓에 세부 정보를 제공합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 네트워크 연결에 대해 자세히 알아봅니다.](cloudsimple-azure-network-connection.md)  
