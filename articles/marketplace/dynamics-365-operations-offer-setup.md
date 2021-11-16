---
title: Microsoft AppSource(Azure Marketplace)에서 Dynamics 365 for Operations 제품 만들기
description: Microsoft AppSource(Azure Marketplace)에서 Dynamics 365 for Operations 제품을 만듭니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 09/27/2021
ms.openlocfilehash: 7b155c07edebf80ee45467829c57024d0ad2845f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552283"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Dynamics 365 for Operations 제품 만들기

이 문서에서는 [Dynamics 365 for Operations](https://dynamics.microsoft.com/finance-and-operations) 제품을 만드는 방법을 설명합니다. 이 제품 유형은 고급 운영, 재무, 제조 및 공급망 관리를 지원하는 ERP(전사적 자원 관리) 서비스입니다. 모든 Dynamics 365 제안은 인증 프로세스를 통과해야 합니다.

시작하기 전에 [파트너 센터](./create-account.md)에서 상업용 Marketplace 계정을 만들고 상업용 Marketplace 프로그램에 해당 계정이 등록되어 있는지 확인합니다.

## <a name="before-you-begin"></a>시작하기 전에

[Dynamics 365 제안 계획](marketplace-dynamics-365.md)을 검토합니다. 이 제안에 대한 기술 요구 사항과 이 제안을 만들 때 필요한 정보 및 자산이 설명되어 있습니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

1. [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2166002)에 로그인합니다.

1. 홈페이지에서 **Marketplace 상품** 타일을 선택합니다.

    [ ![파트너 센터 홈페이지에서 보여 주는 Marketplace 제안 타일](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Marketplace 제안 페이지에서 **+ 새 제안**  >  **Dynamics 365를 작업으로** 선택합니다.

    [![Marketplace 제안 페이지의 '새 제안' 단추 스크린샷은 Dynamics 365 for Operations 제안 유형을 강조 표시합니다. ](media/dynamics-365/new-offer-dynamics-365-operations-workspaces.png) ](media/dynamics-365/new-offer-dynamics-365-operations-workspaces.png#lightbox)

> [!IMPORTANT]
> 제안을 게시한 후 파트너 센터에서 제안을 편집하면 제안을 다시 게시해야 편집한 내용이 Microsoft AppSource에 표시됩니다. 제안을 변경한 후에는 항상 다시 게시해야 합니다.

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **개요** 를 선택합니다.
1. 개요 페이지에서 **+ 새 제품** > **Dynamics 365 for Operations** 를 선택합니다.

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-operations.png" alt-text="왼쪽 창 메뉴 옵션과 '새 제품' 단추":::

> [!IMPORTANT]
> 제안을 게시한 후 파트너 센터에서 제안을 편집하면 제안을 다시 게시해야 편집한 내용이 Microsoft AppSource에 표시됩니다. 제안을 변경한 후에는 항상 다시 게시해야 합니다.

---

## <a name="new-offer"></a>새 제안

**제품 ID** 를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 제안 웹 주소 및 Azure Resource Manager 템플릿(해당하는 경우)에서 고객에게 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. ID는 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 **test-offer-1** 을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
- **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 AppSource에서 사용되지 않습니다. 고객에게 표시되는 제안 이름 및 다른 값과 다릅니다.
- **만들기** 를 선택한 후에는 이 이름을 변경할 수 없습니다.

**만들기** 를 선택하여 제안을 생성합니다. 파트너 센터에서 **제안 설정** 페이지를 엽니다.

## <a name="alias"></a>Alias

파트너 센터 내에서만 이 제안을 참조하는 데 사용할 설명이 포함된 이름을 입력합니다. 이 제안 별칭(제안을 만들 때 입력한 내용으로 미리 채워져 있음)은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 제안 이름과 다릅니다. 나중에 제안 이름을 업데이트하려면 [제안 목록](dynamics-365-operations-offer-listing.md) 페이지를 참조하세요.

## <a name="setup-details"></a>설정 정보

**잠재 고객이 이 목록 제안과 상호 작용하도록 하려면 어떻게 해야 할까요?** 에서 **연락처를** 선택합니다.

CRM(고객 관계 관리) 시스템을 연결하여 고객 연락처 정보를 수집합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 고객이 제안을 찾은 제안 이름, ID 및 마켓플레이스 원본과 함께 이러한 고객 세부 정보는 구성한 CRM 시스템으로 전송됩니다. CRM을 구성하는 방법에 대한 자세한 내용은 [잠재 고객](#customer-leads)을 참조하세요.

## <a name="test-drive"></a>시험 사용

시험 사용은 잠재 고객에게 정해진 시간 동안 미리 구성된 환경에 대한 액세스 권한을 부여하여 제안을 소개하는 좋은 방법입니다. 시험 사용을 제공하면 전환율이 증가하고 충분한 자격을 갖춘 잠재 고객이 생성됩니다. 자세한 내용은 [What is a test drive?](what-is-test-drive.md)(시험 사용이란?)를 참조하세요.

> [!TIP]
> 시험 사용은 평가판과 다릅니다. 시험 사용, 평가판 또는 둘 다를 제공할 수 있습니다. 둘 다 정해진 기간 동안 고객에게 솔루션을 제공합니다. 하지만 시험 사용에는 실제 구현 시나리오에서 입증된 제품의 주요 기능과 이점에 대한 실습 중심의 자기 주도형 체험도 포함됩니다.

시험 사용을 사용하도록 설정하려면 **시험 사용을 사용하도록 설정** 확인란을 선택하고 **시험 사용 유형** 을 선택합니다. 나중에 시험 사용을 구성합니다. 시험 사용에서는 또한 잠재 고객의 CRM 시스템에 대한 제안을 구성해야 합니다(다음 섹션 참조). 제품에서 시험 사용을 제거하려면 이 확인란을 선택 취소합니다.

## <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

자세한 내용은 [상업용 Marketplace 제안의 잠재 고객](partner-center-portal/commercial-marketplace-get-customer-leads.md)을 참조하세요.

## <a name="business-applications-isv-program"></a>비즈니스 애플리케이션 ISV 프로그램

제품은 초기에 표준 계층에 등록됩니다.

제안을 게시하기 전에 [공동 판매 모듈을](https://aka.ms/BizAppsISVProgram)완료합니다.

왼쪽 탐색 메뉴 **속성** 에서 다음 탭을 계속 진행하려면 **초안 저장** 을 선택하세요.

## <a name="next-steps"></a>다음 단계

- 제품 [속성](dynamics-365-operations-properties.md) 구성
- [목록에 제품 추가 모범 사례](gtm-offer-listing-best-practices.md)
