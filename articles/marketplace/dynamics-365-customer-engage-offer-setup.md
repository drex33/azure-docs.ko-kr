---
title: Microsoft AppSource(Azure Marketplace)에서 Dynamics 365 for Customer Engagement 및 Power Apps 제안을 만듭니다.
description: Microsoft AppSource(Azure Marketplace)에서 Dynamics 365 for Customer Engagement 및 Power Apps 제안을 만듭니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/30/2021
ms.openlocfilehash: 1a4bd560603b16a1a2b9fe0a66bc4f1adce25a73
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867474"
---
# <a name="how-to-create-a-dynamics-365-for-customer-engagement--power-apps-offer"></a>Dynamics 365 for Customer Engagement 및 Power Apps 제안을 만드는 방법

이 문서에서는 Dynamics 365 for Customer Engagement 및 Power Apps 제안을 만드는 방법에 대해 설명합니다. 모든 Dynamics 365 제안은 인증 프로세스를 통과해야 합니다. 평가판 환경을 사용하면 사용자가 솔루션을 라이브 Dynamics 365 환경에 배포할 수 있습니다.

시작하기 전에 [파트너 센터](./create-account.md)에서 상업용 Marketplace 계정을 만들고 상업용 Marketplace 프로그램에 해당 계정이 등록되어 있는지 확인합니다.

## <a name="before-you-begin"></a>시작하기 전에

[Dynamics 365 제안 계획](marketplace-dynamics-365.md)을 검토합니다. 이 제안에 대한 기술 요구 사항과 이 제안을 만들 때 필요한 정보 및 자산이 설명되어 있습니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **개요** 를 선택합니다.
3. 개요 페이지에서 **+ 새 제품** > **Dynamics 365 for Customer Engagement 및 Power Apps** 를 선택합니다.

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-customer-engagement.png" alt-text="왼쪽 창 메뉴 옵션과 Customer Engagement가 선택된 ‘새 제안’ 단추를 보여 줍니다.":::

> [!IMPORTANT]
> 제안을 게시한 후 파트너 센터에서 제안을 편집하면 제안을 다시 게시해야 편집한 내용이 Microsoft AppSource에 표시됩니다. 제안을 변경한 후에는 항상 다시 게시해야 합니다.

## <a name="new-offer"></a>새 제안

**제품 ID** 를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 제안 웹 주소 및 Azure Resource Manager 템플릿(해당하는 경우)에서 고객에게 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. ID는 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 게시자 ID가 `testpublisherid`이고 **test-offer-1** 을 입력하는 경우 제안 웹 주소는 `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`이 됩니다.
- **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 AppSource에서 사용되지 않습니다. 고객에게 표시되는 제안 이름 및 다른 값과 다릅니다.
- **만들기** 를 선택한 후에는 이 이름을 변경할 수 없습니다.

**만들기** 를 선택하여 제안을 생성합니다. 파트너 센터에서 **제안 설정** 페이지를 엽니다.

## <a name="alias"></a>Alias

파트너 센터 내에서만 이 제안을 참조하는 데 사용할 설명이 포함된 이름을 입력합니다. 이 제안 별칭(제안을 만들 때 입력한 내용으로 미리 채워져 있음)은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 제안 이름과 다릅니다. 나중에 제안 이름을 업데이트하려면 [제안 목록](dynamics-365-customer-engage-offer-listing.md) 페이지를 참조하세요.

## <a name="setup-details"></a>설정 정보

**잠재 고객이 이 제품 목록과 상호 작용하게 하려면 어떻게 해야 하나요?** 에 대해 이 제품에 사용할 옵션을 선택합니다.

- **Microsoft를 통해 앱 라이선스 관리 사용** – Microsoft를 통해 앱 라이선스를 관리합니다. 고객이 라이선스 없이 앱의 기본 기능을 실행하고 라이선스를 구매한 후 프리미엄 기능을 실행하도록 하려면 **라이선스가 할당되지 않았더라도 고객이 앱을 설치할 수 있도록 허용 상자** 를 선택합니다. 두 번째 상자를 선택하는 경우 라이선스를 요구하지 않도록 솔루션 패키지를 구성해야 합니다.

    > [!NOTE]
    > 제품을 게시한 후에는 이 설정을 변경할 수 없습니다. 이 설정에 대한 자세한 내용은 [앱 라이선스 관리](isv-app-license.md)를 참조하세요.

- **지금 받기(무료)** – 고객에게 무료 제품을 나열합니다.
- **무료 평가판(목록)** – 고객에게 무료 평가판에 대한 링크와 함께 제품을 나열합니다. 평가판을 나열하는 제품은 서비스에 의해 생성, 관리 및 구성되며 Microsoft에서 관리하는 구독을 포함하지 않습니다.

    > [!NOTE]
    > 평가판 링크를 통해 애플리케이션에서 수신하는 토큰은 앱에서 자동으로 계정을 만드는 Azure AD(Active Directory)를 통해 사용자 정보를 가져오는 데만 사용할 수 있습니다. 이 토큰을 사용하는 인증에 Microsoft 계정이 지원되지 않습니다.

- **연락처** – CRM(고객 관계 관리) 시스템을 연결하여 고객 연락처 정보를 수집합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 고객 세부 정보는 제품 이름, ID 및 Marketplace(고객이 제품을 찾은 소스)와 함께 구성된 CRM 시스템으로 전송됩니다. CRM을 구성하는 방법에 대한 자세한 내용은 [잠재 고객](#customer-leads)을 참조하세요.

## <a name="test-drive"></a>시험 사용

시험 사용은 잠재 고객에게 정해진 시간 동안 미리 구성된 환경에 대한 액세스 권한을 부여하여 제안을 소개하는 좋은 방법입니다. 시험 사용을 제공하면 전환율이 증가하고 충분한 자격을 갖춘 잠재 고객이 생성됩니다. 자세한 내용은 [What is a test drive?](what-is-test-drive.md)(시험 사용이란?)를 참조하세요.

> [!TIP]
> 시험 사용은 평가판과 다릅니다. 시험 사용, 평가판 또는 둘 다를 제공할 수 있습니다. 둘 다 정해진 기간 동안 고객에게 솔루션을 제공합니다. 하지만 시험 사용에는 실제 구현 시나리오에서 입증된 제품의 주요 기능과 이점에 대한 실습 중심의 자기 주도형 체험도 포함됩니다.

시험 사용을 사용하도록 설정하려면 **시험 사용을 사용하도록 설정** 확인란을 선택하고 **시험 사용 유형** 을 선택합니다. 나중에 시험 사용을 구성합니다. 시험 사용에서는 또한 잠재 고객의 CRM 시스템에 대한 제안을 구성해야 합니다(다음 섹션 참조). 제품에서 시험 사용을 제거하려면 이 확인란을 선택 취소합니다.

## <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

자세한 내용은 [상업용 Marketplace 제안의 잠재 고객](partner-center-portal/commercial-marketplace-get-customer-leads.md)을 참조하세요.

왼쪽 탐색 메뉴 **속성** 에서 다음 탭을 계속 진행하려면 **초안 저장** 을 선택하세요.

## <a name="next-steps"></a>다음 단계

- [제안 속성 구성](dynamics-365-customer-engage-properties.md)
- [목록에 제품 추가 모범 사례](gtm-offer-listing-best-practices.md)