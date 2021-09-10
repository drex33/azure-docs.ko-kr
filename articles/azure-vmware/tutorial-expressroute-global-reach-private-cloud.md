---
title: 온-프레미스 환경을 Azure VMware Solution에 피어링
description: Azure VMware Solution의 프라이빗 클라우드에 대한 ExpressRoute Global Reach 피어링을 만드는 방법을 알아봅니다.
ms.topic: tutorial
ms.custom: contperf-fy21q4, contperf-fy22q1
ms.date: 07/28/2021
ms.openlocfilehash: b930aab15ef9af8e43919af1671f44c20e6b2075
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730263"
---
# <a name="peer-on-premises-environments-to-azure-vmware-solution"></a>온-프레미스 환경을 Azure VMware Solution에 피어링

Azure VMware Solution 프라이빗 클라우드를 배포한 후 온-프레미스 환경에 연결합니다. ExpressRoute Global Reach는 온-프레미스 환경을 Azure VMware Solution 프라이빗 클라우드에 연결합니다. ExpressRoute Global Reach 연결은 프라이빗 클라우드 ExpressRoute 회로와 온-프레미스 환경에 대한 기존 ExpressRoute 연결 간에 설정됩니다. 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="ExpressRoute Global Reach 온-프레미스 네트워크 연결을 보여주는 다이어그램." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

>[!NOTE]
>VPN을 통해 연결할 수도 있지만 이 빠른 시작 가이드에서는 설명하지 않습니다.

이 문서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 온-프레미스 ExpressRoute 회로에서 ExpressRoute 권한 키를 만듭니다.
> * 온-프레미스 ExpressRoute 회로와 프라이빗 클라우드 피어링
> * 온-프레미스 네트워크 연결 확인

완료되면 끝 부분에서 권장되는 다음 단계를 수행하여 이 시작 가이드의 단계를 계속 진행합니다.

## <a name="prerequisites"></a>필수 조건

- [다양한 Azure 구독에서 연결을 사용하도록 설정](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)하는 방법에 대한 설명서를 검토합니다.  

- 온-프레미스 환경을 Azure에 연결하기 위해 필요한 별도의 작동하는 ExpressRoute 회로(피어링의 경우 ‘회로 1’)입니다.

- ExpressRoute 공급자 서비스를 포함한 모든 게이트웨이가 4바이트 ASN(자율 시스템 번호)을 지원하는지 확인합니다. Azure VMware Solution은 경고를 알리는 데 4바이트 공용 ASN을 사용합니다.

>[!NOTE]
>기본 경로를 Azure(0.0.0.0/0)로 보급하는 경우 기본 경로 외에도 온-프레미스 네트워크를 포함하는 보다 구체적인 경로를 보급하여 Azure VMware Solution에 대한 관리 액세스를 사용하도록 설정해야 합니다. 성공적인 서비스 운영을 보장하기 위해 단일 0.0.0.0/0 경로는 Azure VMware Solution의 관리 네트워크에서 삭제됩니다.

## <a name="create-an-expressroute-auth-key-in-the-on-premises-expressroute-circuit"></a>온-프레미스 ExpressRoute 회로에서 ExpressRoute 권한 키를 만듭니다.

회로 소유자는 가상 네트워크 게이트웨이를 ExpressRoute 회로에 연결하기 위해 회로 사용자가 사용할 권한 부여 키를 만드는 권한 부여를 만듭니다. 권한 부여는 하나의 연결에만 유효합니다.

> [!NOTE]
> 각 연결에는 별도의 권한이 필요합니다.

1. **ExpressRoute 회로** 블레이드의 설정에서 **권한 부여** 를 선택합니다.

1. 권한 부여 키의 이름을 입력하고 **저장** 을 선택합니다.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="권한 부여를 선택하고 권한 부여 키의 이름을 입력합니다.":::

   만들기가 완료되면 회로의 권한 부여 키 목록에 새 키가 표시됩니다.

1. 권한 부여 키와 ExpressRoute ID를 복사합니다. 다음 단계에서 피어링을 완료하는 데 사용됩니다.

## <a name="peer-private-cloud-to-on-premises"></a>프라이빗 클라우드를 온-프레미스로 피어링 
프라이빗 클라우드 ExpressRoute 회로에 대한 권한 부여 키를 만들었으므로, 이제 온-프레미스 ExpressRoute 회로를 통해 피어링할 수 있습니다. **Azure Portal** 의 온-프레미스 ExpressRoute 회로에서 피어링이 수행됩니다. 프라이빗 클라우드 ExpressRoute 회로의 리소스 ID(ExpressRoute 회로 ID)와 권한 부여 키를 사용하여 피어링을 완료합니다.

1. 프라이빗 클라우드의 관리에서 **연결** > **ExpressRoute Global Reach** > **추가** 를 선택합니다.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Azure VMware Solution 프라이빗 클라우드의 ExpressRoute Global Reach 탭을 보여주는 스크린샷.":::

1. 이전 섹션에서 만든 ExpressRoute 경로 ID와 권한 부여 키를 입력합니다.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="연결 정보 입력 대화 상자 스크린샷":::   

1. **만들기** 를 선택합니다. 새 연결은 온-프레미스 클라우드 연결 목록에 표시됩니다.

>[!TIP]
>**추가** 를 선택하여 목록에서 연결을 삭제하거나 연결을 끊을 수 있습니다.  
>
>:::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Azure VMware Solution에서 온-프레미스 연결의 연결을 끊거나 삭제하는 방법을 보여 주는 스크린샷":::


## <a name="verify-on-premises-network-connectivity"></a>온-프레미스 네트워크 연결 확인

이제 **온-프레미스 에지 라우터** 에서 ExpressRoute가 NSX-T 네트워크 세그먼트와 Azure VMware Solution 관리 세그먼트를 연결하는 위치가 표시됩니다.

>[!IMPORTANT]
>모든 사용자는 다른 환경을 가지며, 일부는 이러한 경로를 온-프레미스 네트워크에 다시 전파하도록 허용해야 합니다.  

## <a name="next-steps"></a>다음 단계
Azure VMware Solution 프라이빗 클라우드에 VMware HCX 추가 기능을 설치하려면 다음 자습서로 계속 진행합니다.

> [!div class="nextstepaction"]
> [VMware HCX 설치](install-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
