---
title: Azure VMware Solution에 대한 호스트 할당량 요청
description: Azure VMware Solution에 대한 호스트 할당량/용량을 요청하는 방법을 알아봅니다. 기존 Azure VMware Solution 프라이빗 클라우드에서 더 많은 호스트를 요청할 수도 있습니다.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 09/27/2021
ms.openlocfilehash: a95f152c5e55b1750aa34c9a4093e311eb27df29
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504496"
---
# <a name="request-host-quota-for-azure-vmware-solution"></a>Azure VMware Solution에 대한 호스트 할당량 요청

이 방법에서는 [Azure VMware Solution](introduction.md)에 대한 호스트 할당량/용량을 요청합니다. 새 배포용이든 기존 배포용이든 관계없이 호스트가 할당되도록 지원 티켓을 제출합니다. 

기존 Azure VMware Solution 프라이빗 클라우드가 있고 더 많은 호스트를 할당하려는 경우에도 동일한 프로세스를 진행합니다.

>[!IMPORTANT]
>요청된 수에 따라 호스트를 할당하는 데 최대 5일(영업일)이 걸릴 수 있습니다.  따라서 프로비저닝에 필요한 항목을 요청하여 자주 할당량 증가를 요청할 필요가 없도록 하세요.

## <a name="eligibility-criteria"></a>자격 기준

다음 조건 중 하나를 준수하는 Azure 구독에 Azure 계정이 필요합니다.

- Microsoft와의 [Azure EA](../cost-management-billing/manage/ea-portal-agreements.md)(기업계약)에 따른 구독.
- 기존 CSP(클라우드 솔루션 공급자) Azure 제품 계약 또는 Azure 플랜에 따른 CSP 관리 구독.
- Microsoft와의 [Microsoft 고객 계약](../cost-management-billing/understand/mca-overview.md) .

## <a name="request-host-quota-for-ea-customers"></a>EA 고객을 위한 호스트 할당량 요청

1. Azure Portal의 **도움말 + 지원** 에서 **[새 지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)** 을 만들고, 다음 정보를 제공합니다.
   - **문제 유형:** 기술
   - **구독**: 이용 중인 구독을 선택합니다
   - **서비스:** 모든 서비스 > Azure VMware Solution
   - **리소스:** 일반 질문 
   - **요약:** 용량 필요
   - **문제 유형:** 용량 관리 문제
   - **문제 하위 유형:** 호스트 할당량/용량을 추가하기 위한 고객 요청

1. 지원 티켓의 **설명** 에 있는 **세부 정보** 탭에서 다음 정보를 입력합니다.
 
   - 지역 이름
   - 호스트 수
   - 기타 세부 정보

   >[!NOTE]
   >Azure VMware Solution은 최소 3개의 호스트가 필요하며 N+1개의 호스트에 대한 중복성을 권장합니다. 

1. **검토 + 만들기** 를 선택하여 요청을 제출합니다.


## <a name="request-host-quota-for-csp-customers"></a>CSP 고객을 위한 호스트 할당량 요청 

CSP는 [Microsoft 파트너 센터](https://partner.microsoft.com)를 사용해야만 고객용 Azure VMware Solution을 사용하도록 설정할 수 있습니다. 이 문서에서는 파트너를 위한 구매 프로시저를 보여 주기 위해 [CSP Azure 플랜](/partner-center/azure-plan-lp)를 예시로 사용합니다.

파트너 센터에서 AOBO(**Admin On Behalf Of**) 프로시저를 사용해 Azure Portal에 액세스합니다.

>[!IMPORTANT] 
>Azure VMware Solution 서비스는 필수적인 다중 테넌트를 제공하지 않습니다. 이를 필요로 하는 호스팅 파트너는 지원을 받을 수 없습니다. 

1. CSP Azure 플랜을 구성합니다.

   1. **파트너 센터** 에서 **CSP** 를 선택하여 **고객** 영역에 액세스합니다.
   
      :::image type="content" source="media/pre-deployment/csp-customers-screen.png" alt-text="Microsoft 파트너 센터 고객 영역을 보여주는 스크린샷" lightbox="media/pre-deployment/csp-customers-screen.png":::
   
   1. 고객을 선택하고 **제품 추가** 를 선택합니다.
   
      :::image type="content" source="media/pre-deployment/csp-partner-center.png" alt-text="Microsoft 파트너 센터에서 선택한 Azure 플랜을 보여주는 스크린샷" lightbox="media/pre-deployment/csp-partner-center.png":::
   
   1. **Azure 플랜** 을 선택한 다음 **카트에 추가** 를 선택합니다. 
   
   1. 고객용 Azure 플랜 구독의 일반 설정을 검토하고 완료합니다. 자세한 내용은 [Microsoft 파트너 센터 설명서](/partner-center/azure-plan-manage)를 참조하세요.

1. Azure 플랜을 구성하고 구독 대신 필요한 [Azure RBAC 권한](/partner-center/azure-plan-manage)이 있다면 Azure 플랜 구독에 대한 할당량을 요청합니다. 

   1. AOBO(**Admin On Behalf Of**) 프로시저를 이용해 [Microsoft 파트너 센터](https://partner.microsoft.com)에서 Azure Portal에 액세스합니다.
   
   1. **CSP** 를 선택하여 **고객** 영역에 액세스합니다.
   
   1. 고객 세부 정보를 확장하고 **Microsoft Azure 관리 포털** 을 선택합니다.
   
   1. Azure Portal의 **도움말 + 지원** 에서 **[새 지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)** 을 작성하고, 티켓에 대한 다음 정보를 제공합니다.
      - **문제 유형:** 기술
      - **구독**: 이용 중인 구독을 선택합니다
      - **서비스:** 모든 서비스 > Azure VMware Solution
      - **리소스:** 일반 질문 
      - **요약:** 용량 필요
      - **문제 유형:** 용량 관리 문제
      - **문제 하위 유형:** 호스트 할당량/용량을 추가하기 위한 고객 요청
   
   1. 지원 티켓의 **설명** 에 있는 **세부 정보** 탭에서 다음 정보를 입력합니다.
   
      - 지역 이름
      - 호스트 수
      - 기타 세부 정보
      - 여러 고객을 호스트할 예정인가요?
   
      >[!NOTE]
      >Azure VMware Solution은 최소 3개의 호스트가 필요하며 N+1개의 호스트에 대한 중복성을 권장합니다. 
   
   1. **검토 + 만들기** 를 선택하여 요청을 제출합니다.


## <a name="next-steps"></a>다음 단계

Azure VMware Solution을 배포하려면 먼저 구독에 [리소스 공급자를 등록](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider)하여 서비스를 사용하도록 설정해야 합니다.   
