---
title: Microsoft AppSource에서 Dynamics 365 Business Central 제품 만들기 (Azure Marketplace)
description: Microsoft AppSource (Azure Marketplace)에서 Dynamics 365 Business Central 제품을 만듭니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 11/24/2021
ms.openlocfilehash: 4aa8e4ea91bd50d66db8e3cd1e975dd0c353dc9f
ms.sourcegitcommit: 8178cd2d9a47a67bb324483bd0879a57591706a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133110311"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Dynamics 365 Business Central 제품 만들기

이 문서에서는 [Dynamics 365 비즈니스 센터](https://dynamics.microsoft.com/business-central/overview) 제품을 만드는 방법을 설명 합니다. 이 제품 유형은 ERP(전사적 자원 관리) 서비스이며 재무, 운영, 공급 체인, CRM, 프로젝트 관리 및 전자 상거래를 포함하여 광범위한 비즈니스 프로세스를 지원합니다. 모든 Dynamics 365 제안은 인증 프로세스를 통과해야 합니다.

시작하기 전에 [파트너 센터](create-account.md)에서 상업용 Marketplace 계정을 만들고 상업용 Marketplace 프로그램에 해당 계정이 등록되어 있는지 확인합니다.

## <a name="before-you-begin"></a>시작하기 전에

[Dynamics 365 제안 계획](marketplace-dynamics-365.md)을 검토합니다. 여기에는 이 제품에 대한 기술 요구 사항이 설명되어 있고 제품을 만들 때 필요한 정보와 자산이 나와 있습니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

1. [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2166002)에 로그인합니다.

1. 홈페이지에서 **Marketplace 상품** 타일을 선택합니다.

    [ ![파트너 센터 홈페이지에서 보여 주는 Marketplace 제안 타일](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Marketplace 제품 페이지에서 **+ 새로 만들기 제공**  >  **Dynamics 365 비즈니스 중부** 를 선택 합니다.

    [![새 제안 단추를 보여 줍니다. Dynamics 365 Business Central 제품 유형이 강조 표시 됩니다. ](media/dynamics-365/new-offer-dynamics-365-business-central-workspaces.png) ](media/dynamics-365/new-offer-dynamics-365-business-central-workspaces.png#lightbox)

> [!IMPORTANT]
> 제안을 게시한 후 파트너 센터에서 제안을 편집하면 제안을 다시 게시해야 편집한 내용이 Microsoft AppSource에 표시됩니다. 제안을 변경한 후에는 항상 다시 게시해야 합니다.

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **개요** 를 선택합니다.
1. 개요 페이지에서 **+ 새로 만들기 제공**  >  **Dynamics 365 비즈니스 중부** 를 선택 합니다.

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-business-central.png" alt-text="왼쪽 창 메뉴 옵션 및 '새 제품' 단추":::

> [!IMPORTANT]
> 제안을 게시한 후 파트너 센터에서 제안을 편집하면 제안을 다시 게시해야 편집한 내용이 Microsoft AppSource에 표시됩니다. 제안을 변경한 후에는 항상 다시 게시해야 합니다.

---

## <a name="new-offer"></a>새 제안

표시 되는 대화 상자에서 **제품 ID** 를 입력 합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 제안 웹 주소 및 Azure Resource Manager 템플릿(해당하는 경우)에서 고객에게 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. ID는 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 게시자 ID가 `testpublisherid`이고 **test-offer-1** 을 입력하는 경우 제안 웹 주소는 `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`이 됩니다.
- **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 AppSource에서 사용되지 않습니다. 고객에게 표시되는 제안 이름 및 다른 값과 다릅니다.
- **만들기** 를 선택한 후에는 이 이름을 변경할 수 없습니다.

**만들기** 를 선택하여 제안을 생성합니다. 파트너 센터에서 **제안 설정** 페이지를 엽니다.

## <a name="alias"></a>Alias

파트너 센터 내에서만 이 제안을 참조하는 데 사용할 설명이 포함된 이름을 입력합니다. 이 제안 별칭(제안을 만들 때 입력한 내용으로 미리 채워져 있음)은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 제안 이름과 다릅니다. 나중에 제안 이름을 업데이트하려면 [제안 목록](dynamics-365-business-central-offer-listing.md) 페이지를 참조하세요.

## <a name="setup-details"></a>설정 정보

제품(Biz Central)에 적용되는 **패키지 유형** 을 선택합니다.

- **추가 기능** 앱은 Dynamics 365 Business Central의 환경 및 기존 기능을 확장합니다. 자세한 내용은 [추가 기능 앱](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)을 참조하세요.
- **Connect** 앱은 Dynamics 365 Business Central과 타사 솔루션 또는 서비스 간에 지점 간 연결을 설정해야 하는 시나리오에서 사용할 수 있습니다. 자세한 내용은 [Connect 앱](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)을 참조하세요.

**잠재 고객이 이 목록 제안과 어떻게 상호 작용하기를 원하나요?** 에 대해서는 이 제안에 사용하려는 옵션을 선택합니다.

- **지금 받기(무료)** – 고객에게 무료 제품을 나열합니다.
- **무료 평가판(목록)** – 고객에게 무료 평가판에 대한 링크와 함께 제품을 나열합니다. 평가판을 나열하는 제품은 서비스에 의해 생성, 관리 및 구성되며 Microsoft에서 관리하는 구독을 포함하지 않습니다.

    > [!NOTE]
    > 평가판 링크를 통해 애플리케이션에서 수신하는 토큰은 앱에서 자동으로 계정을 만드는 Azure AD(Active Directory)를 통해 사용자 정보를 가져오는 데만 사용할 수 있습니다. 이 토큰을 사용하는 인증에 Microsoft 계정이 지원되지 않습니다.

- **연락처** – CRM(고객 관계 관리) 시스템을 연결하여 고객 연락처 정보를 수집합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 고객 세부 정보는 제품 이름, ID 및 Marketplace(고객이 제품을 찾은 소스)와 함께 구성된 CRM 시스템으로 전송됩니다. CRM을 구성하는 방법에 대한 자세한 내용은 [잠재 고객](#customer-leads)을 참조하세요.

## <a name="test-drive"></a>시험 사용

시험 사용은 "구입 전 체험" 옵션을 제공하여 잠재적인 고객에게 제품을 선보임으로써 구매 전환율을 높이고 적절한 잠재 고객을 창출할 수 있는 유용한 방법입니다. 자세히 알아보려면 [테스트 드라이브란?](what-is-test-drive.md)을 참조하세요.

일정 기간 동안 시험 사용을 사용하도록 설정하려면 **시험 사용을 사용하도록 설정** 확인란을 선택합니다. 제품에서 시험 사용을 제거하려면 이 확인란을 선택 취소합니다.

> [!TIP]
> 시험 사용은 평가판과 다릅니다. 시험 사용, 평가판 또는 둘 다를 제공할 수 있습니다. 둘 다 정해진 기간 동안 고객에게 솔루션을 제공합니다. 하지만 시험 사용에는 실제 구현 시나리오에서 입증된 제품의 주요 기능과 이점에 대한 실습 중심의 자기 주도형 체험도 포함됩니다.

## <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

자세한 내용은 [상업용 Marketplace 제안의 잠재 고객](partner-center-portal/commercial-marketplace-get-customer-leads.md)을 참조하세요.

왼쪽 탐색 메뉴 **속성** 에서 다음 탭을 계속 진행하려면 **초안 저장** 을 선택하세요.

## <a name="next-steps"></a>다음 단계

- [제안 속성 구성](dynamics-365-business-central-properties.md)
- [목록에 제품 추가 모범 사례](gtm-offer-listing-best-practices.md)
