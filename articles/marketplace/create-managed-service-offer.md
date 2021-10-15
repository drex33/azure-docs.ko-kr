---
title: 상업용 Marketplace를 위한 관리되는 서비스 제품 만들기
description: Azure Marketplace를 위한 새 관리되는 서비스 제품을 만듭니다.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 6aa296d3dfa6f85504974083887de18b42d71577
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045373"
---
# <a name="create-a-managed-service-offer-for-the-commercial-marketplace"></a>상업용 Marketplace를 위한 관리되는 서비스 제품 만들기

이 문서에서는 파트너 센터를 사용하여 Microsoft 상용 마켓플레이스에 대한 관리형 서비스 제품을 만드는 방법을 설명합니다.

관리형 서비스 제품을 게시하려면 클라우드 플랫폼에서 Microsoft 골드 또는 실버 역량을 달성해야 합니다. [상용 마켓플레이스를 위한 관리형 서비스 제품 계획](./plan-managed-service-offer.md)을 아직 읽어 보지 않았다면 읽어 보세요. 파트너 센터에서 제품을 만들 때 필요한 자산을 준비하는 데 도움이 됩니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

1. [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2166002)에 로그인합니다.

1. 홈페이지에서 **Marketplace 상품** 타일을 선택합니다.

    [ ![파트너 센터 홈페이지에서 보여 주는 Marketplace 제안 타일](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Marketplace 제안 페이지에서 **+ 새 제안**  >  **관리형 서비스를** 선택합니다.

    [![관리 서비스 제안 유형 옵션을 보여 줍니다. ](./media/new-offer-managed-service-workspaces.png) ](./media/new-offer-managed-service-workspaces.png#lightbox)

1. 새 **관리형 서비스** 대화 상자에서 **제안 ID** 를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다. 이 ID는 상업용 마켓플레이스 목록의 URL 및 Azure Resource Manager 템플릿에 표시됩니다(해당하는 경우). 예를 들어, 이 입력란에 test-offer-1을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.

    - 계정의 각 제품에는 고유한 제품 ID가 있어야 합니다.
    - 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다.
    - **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

1. **제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다. 이 이름은 온라인 스토어에는 표시되지 않으며 고객에게 표시되는 제품 이름과는 다릅니다.
1. 제품을 생성하고 계속하려면 **만들기** 를 선택합니다.

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **상용 마켓플레이스** > **개요** 를 선택합니다.
1. 개요 탭에서 **+ 새 제품** > **관리형 서비스** 를 선택합니다.

    :::image type="content" source="./media/new-offer-managed-service.png" alt-text="왼쪽 탐색 메뉴를 보여 줍니다.":::

1. **새 제품** 대화 상자에서 **제품 ID** 를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다. 이 ID는 상업용 마켓플레이스 목록의 URL 및 Azure Resource Manager 템플릿에 표시됩니다(해당하는 경우). 예를 들어, 이 입력란에 test-offer-1을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.

    - 계정의 각 제품에는 고유한 제품 ID가 있어야 합니다.
    - 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다.
    - **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

1. **제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다. 이 이름은 온라인 스토어에는 표시되지 않으며 고객에게 표시되는 제품 이름과는 다릅니다.
1. 제품을 생성하고 계속하려면 **만들기** 를 선택합니다.

---

## <a name="setup-details"></a>설정 정보

이 섹션은 해당 제품 유형에 적용되지 않습니다.

## <a name="customer-leads"></a>잠재 고객

고객이 컨설팅 서비스에 관심을 표시할 때 고객 연락처 정보를 받을 수 있도록 CRM(고객 관계 관리) 시스템을 상업용 마켓플레이스 제품에 연결합니다. 제품 생성 도중이나 생성 후에 언제든지 이 연결을 수정할 수 있습니다. 자세한 내용은 [상업용 마켓플레이스 제품의 잠재 고객](./partner-center-portal/commercial-marketplace-get-customer-leads.md)을 참조하세요.

파트너 센터에서 리드 관리를 구성하려면:

1. 파트너 센터에서 **제품 설정** 탭으로 이동합니다.
2. **잠재 고객** 아래에서 **연결** 링크를 선택합니다.
3. **연결 정보** 대화 상자의 목록에서 잠재 고객 대상을 선택합니다.
4. 표시되는 필드를 완성합니다. 자세한 단계는 다음 문서를 참조하세요.

    - [Azure 테이블로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    - [Dynamics 365 Customer Engagement로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)(이전 명칭: Dynamics CRM Online)
    - [HTTPS 엔드포인트로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    - [Marketo로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    - [Salesforce로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. 제공된 구성의 유효성을 검사하려면 **유효성 검사** 링크를 선택합니다.
6. 연결 세부 정보를 구성한 경우 **연결** 을 선택합니다.
7. **초안 저장** 을 선택합니다.

파트너 센터에서 게시할 제품을 제출하면 연결의 유효성이 검사되고 테스트 잠재 고객을 보낼 수 있습니다. 제품을 라이브로 전환하기 전에 미리 볼 때 미리 보기 환경에서 직접 제품을 구매하여 잠재 고객 연결을 테스트합니다.

> [!TIP]
> 잠재 고객을 잃어버리는 경우가 없도록 잠재 고객 대상에 대한 연결을 업데이트된 상태로 유지해야 합니다.

다음 **속성** 탭으로 진행하기 전에 **초안 저장** 을 선택합니다.

## <a name="next-step"></a>다음 단계

- 제품 [속성](create-managed-service-offer-properties.md) 구성
