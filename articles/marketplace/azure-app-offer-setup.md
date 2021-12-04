---
title: Azure Marketplace에서 Azure 애플리케이션 제품 만들기
description: 상업용 Marketplace 포털을 사용하여 Azure Marketplace에서 또는 CSP(클라우드 솔루션 공급자) 프로그램을 통해 등록 또는 판매할 새 Azure 애플리케이션 제품을 만듭니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/03/2021
ms.openlocfilehash: 7518d7942c03553fd2e023e27fa8f9af6775d42a
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133543221"
---
# <a name="create-an-azure-application-offer"></a>Azure 애플리케이션 제품 만들기

상업용 마켓플레이스 게시자로서 사용자는 잠재 고객이 솔루션을 구매할 수 있도록 Azure 애플리케이션 제품을 만들 수 있습니다. 이 문서에서는 Microsoft 상업용 마켓플레이스를 위한 Azure 애플리케이션 제품을 만드는 프로세스에 대해 설명합니다.

[상업용 마켓플레이스를 위한 Azure 애플리케이션 제품 계획](plan-azure-application-offer.md)을 아직 읽어 보지 않았다면 읽어 보세요. 이는 리소스를 제공하고, 제품을 만들 때 필요한 정보 및 자산을 수집하는 데 도움이 됩니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.

1. 홈페이지에서 **Marketplace 상품** 타일을 선택합니다.

    [ ![파트너 센터 홈페이지에서 보여 주는 Marketplace 제안 타일](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Marketplace 제품 페이지에서 **+ 새 제품**  >  **Azure 애플리케이션** 을 선택 합니다.

    [![Marketplace 제품 페이지의 새 제품 단추를 보여 줍니다. ](./media/create-new-azure-app-offer/new-offer-azure-app-workspaces.png) ](./media/create-new-azure-app-offer/new-offer-azure-app-workspaces.png#lightbox)

1. **새 Azure 애플리케이션** 대화 상자에서 **제품 ID** 를 입력 합니다. 계정의 각 제품에 대한 고유 식별자입니다. 이 ID는 상업용 마켓플레이스 목록의 URL 및 Azure Resource Manager 템플릿에 표시됩니다(해당하는 경우). 예를 들어, 이 입력란에 test-offer-1을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.

     * 계정의 각 제품에는 고유한 제품 ID가 있어야 합니다.
     * 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다.
     * **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

1. **제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

     * 이 이름은 파트너 센터에서 표시되며, 고객에게 표시되는 제품 이름 및 기타 값과 다릅니다.
     * **만들기** 를 선택한 후에는 제품 별칭을 변경할 수 없습니다.

1. 제품을 생성하고 계속하려면 **만들기** 를 선택합니다.

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.

1. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스** > **개요** 를 선택합니다.

1. 개요 페이지에서 **+ 새 제품** > **Azure 애플리케이션** 을 선택합니다.

    ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. **새 제품** 대화 상자에서 **제품 ID** 를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다. 이 ID는 상업용 마켓플레이스 목록의 URL 및 Azure Resource Manager 템플릿에 표시됩니다(해당하는 경우). 예를 들어, 이 입력란에 test-offer-1을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.

     * 계정의 각 제품에는 고유한 제품 ID가 있어야 합니다.
     * 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다.
     * **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

1. **제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

     * 이 이름은 파트너 센터에서 표시되며, 고객에게 표시되는 제품 이름 및 기타 값과 다릅니다.
     * **만들기** 를 선택한 후에는 제품 별칭을 변경할 수 없습니다.

1. 제품을 생성하고 계속하려면 **만들기** 를 선택합니다.

---

## <a name="configure-your-azure-application-offer-setup-details"></a>Azure 애플리케이션 제품 설정 세부 정보 구성

**제품 설정** 탭의 **설정 세부 정보** 에서, 시험 사용할 구성 여부를 선택합니다. 또한 CRM(고객 관계 관리) 시스템을 상업용 마켓플레이스 제품과 연결합니다.

### <a name="enable-a-test-drive-optional"></a>시험 사용을 사용하도록 설정(선택 사항)

시험 사용은 잠재 고객에게 정해진 시간 동안 미리 구성된 환경에 대한 액세스 권한을 부여하여 제품을 소개하는 좋은 방법입니다. 시험 사용을 제공하면 전환율이 증가하고 충분한 자격을 갖춘 잠재 고객이 생성됩니다. 시험 사용에 대한 자세한 내용은 [시험 사용](plan-azure-application-offer.md#test-drive)을 참조하세요.

#### <a name="to-enable-a-test-drive"></a>시험 사용을 사용하도록 설정하기

- **시험 사용** 에서 **시험 사용을 사용하도록 설정** 확인란을 선택합니다.

### <a name="customer-lead-management"></a>잠재 고객 관리

고객이 관심을 표시하거나 제품을 배포할 때 고객 연락처 정보를 받을 수 있도록 CRM(고객 관계 관리) 시스템을 상업용 마켓플레이스 제품에 연결합니다.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>파트너 센터에서 연결 정보 구성하기

1. **잠재 고객** 아래에서 **연결** 링크를 선택합니다.
1. **연결 정보** 대화 상자의 목록에서 잠재 고객 대상을 선택합니다.
1. 표시되는 필드를 완성합니다. 자세한 단계는 다음 문서를 참조하세요.

   - [Azure 테이블로 잠재 고객을 보내도록 제품 구성](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Dynamics 365 Customer Engagement로 잠재 고객을 보내도록 제품 구성](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)
   - [HTTPS 엔드포인트로 잠재 고객을 보내도록 제품 구성](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Marketo로 잠재 고객을 보내도록 제품 구성](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Salesforce로 잠재 고객을 보내도록 제품 구성](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 제공된 구성의 유효성을 검사하려면 **유효성 검사** 링크를 선택합니다(해당하는 경우).
1. 대화 상자를 닫으려면 **연결** 을 선택합니다.
1. 다음 탭(속성)으로 진행하기 전에 **초안 저장** 을 선택합니다.

> [!NOTE]
> 잠재 고객을 잃지 않도록 잠재 고객 대상에 대한 연결을 최신 상태로 유지하세요. 항목이 변경될 때마다 이러한 연결을 업데이트해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 속성 구성](azure-app-properties.md)
