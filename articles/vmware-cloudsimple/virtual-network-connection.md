---
title: ExpressRoute를 사용하여 CloudSimple에 Azure 가상 네트워크 연결 - Azure VMware Solution by CloudSimple
description: Azure 가상 네트워크와 CloudSimple 환경 간의 연결을 위한 피어링 정보를 얻는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 98f38a7222b569dc4c1a8a9ecef0fd6bd6818ede
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339032"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>ExpressRoute를 사용하여 CloudSimple에 Azure 가상 네트워크 연결

프라이빗 클라우드 네트워크를 Azure 가상 네트워크와 Azure 리소스로 확장할 수 있습니다. ExpressRoute 연결을 통해 프라이빗 클라우드에서 Azure 구독에서 실행 중인 리소스에 액세스할 수 있습니다.

## <a name="request-authorization-key"></a>권한 부여 키 요청

프라이빗 클라우드와 Azure 가상 네트워크 간의 ExpressRoute 연결에는 권한 부여 키가 필요합니다. 키를 얻으려면 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지원</a>에 티켓을 제출합니다.  요청에 다음 정보를 사용합니다.

* 문제 유형: **기술**
* 구독: **CloudSimple 서비스가 배포된 구독 선택**
* 서비스: **VMware Solution by CloudSimple**
* 문제 유형: **서비스 요청**
* 문제 하위 유형: **Azure VNET 연결을 위한 권한 부여 키**
* 제목: **Azure VNET 연결을 위한 권한 부여 키 요청**

## <a name="get-peering-information-from-cloudsimple-portal"></a>CloudSimple 포털에서 피어링 정보 가져오기

연결을 설정하려면 Azure 가상 네트워크와 CloudSimple 환경 간에 연결을 설정해야 합니다.  절차의 일부로 피어 회로 URI와 권한 부여 키를 제공해야 합니다. [CloudSimple 포털](access-cloudsimple-portal.md)에서 URI와 권한 부여 키를 가져옵니다.  측면 메뉴에서 **네트워크** 를 선택하고 **Azure 네트워크 연결** 을 선택합니다. 또는 측면 메뉴에서 **계정** 을 선택하고 **Azure 네트워크 연결** 을 선택합니다.

*복사* 아이콘을 사용하여 각 지역에 대한 권한 부여 키와 피어 회로 URI를 복사합니다. 연결하려는 각 CloudSimple 지역에 대해 다음을 수행합니다.

1. **복사** 를 클릭하여 URI를 복사합니다. Azure Portal에 추가할 수 있는 파일에 붙여넣습니다.  
2. **복사** 를 클릭하여 권한 부여 키를 복사하고 파일에도 붙여넣습니다.

**사용 가능** 상태의 권한 부여 키와 피어 회로 URI를 복사합니다.다.  **사용됨** 상태는 키가 가상 네트워크 연결을 만드는 데 이미 사용되었음을 나타냅니다.

![가상 네트워크 연결 페이지](media/virtual-network-connection.png)

CloudSimple 링크에 Azure 가상 네트워크를 설정하는 방법에 대한 자세한 내용은 [ExpressRoute를 사용하여 Azure 가상 네트워크에 CloudSimple 프라이빗 클라우드 환경 연결](azure-expressroute-connection.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드에 대한 Azure 가상 네트워크 연결](azure-expressroute-connection.md)
* [Azure ExpressRoute를 사용하여 온-프레미스 네트워크에 연결](on-premises-connection.md)
