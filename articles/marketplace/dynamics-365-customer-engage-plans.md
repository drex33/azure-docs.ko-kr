---
title: Microsoft AppSource(Azure Marketplace)에서 Dynamics 365 for Customer Engagement & Power Apps 플랜을 만듭니다.
description: 타사 앱 관리 제품을 사용하도록 선택한 경우 Dynamics 365 for Customer Engagement & Power Apps 제품 플랜을 구성합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/30/2021
ms.openlocfilehash: 086457e916f3eeef41023efe56cc011e714ab343
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866331"
---
# <a name="create-dynamics-365-for-customer-engagement--power-apps-plans"></a>Dynamics 365 for Customer Engagement & Power Apps 플랜 만들기

제품에 앱 라이선스 관리를 사용하도록 설정한 경우 다음 스크린샷에 표시된 것처럼 **플랜** 탭이 나타납니다. 그렇지 않으면 [Dynamics 365 for Customer Engagement & Power Apps 제품 기술 구성 설정](dynamics-365-customer-engage-technical-configuration.md)으로 이동합니다.

:::image type="content" source="./media/third-party-license/plan-tab.png" alt-text="타사 앱 라이선스에 대해 사용하도록 설정된 Dynamics 365 for Customer Engagement & Power Apps 제품의 플랜 개요 탭 스크린샷.":::

제품에 앱 라이선스 관리를 사용하도록 설정한 경우 플랜을 하나 이상 정의해야 합니다. 동일한 제품에 다양한 옵션을 사용하여 다양한 플랜을 만들 수 있습니다. 해당 플랜(SKU라고도 함)은 수익 창출 또는 서비스 계층 측면에서 다를 수 있습니다. 나중에 이러한 플랜에 대해 Dynamics 플랫폼의 런타임 라이선스 검사를 사용하도록 설정하려면 솔루션 패키지에서 이러한 플랜의 서비스 ID를 매핑합니다. 솔루션 패키지에서 각 플랜의 서비스 ID를 매핑합니다. 그러면 Dynamics 플랫폼이 이러한 플랜에 대해 라이선스 검사를 실행할 수 있습니다.

## <a name="create-a-plan"></a>계획 만들기

1. **플랜 개요** 페이지의 상단 근처에 있는 **+ 새 플랜 만들기** 를 선택합니다.
1. 표시되는 대화 상자의 **플랜 ID** 상자에 고유한 플랜 ID를 입력합니다. 최대 50자의 영숫자 소문자, 대시 또는 밑줄을 사용합니다. **만들기** 를 선택한 후에는 플랜 ID를 수정할 수 없습니다.
1. **플랜 이름** 상자에 이 플랜의 고유한 이름을 입력합니다. 최대 50자를 사용할 수 있습니다.
1. **만들기** 를 선택합니다.

## <a name="define-the-plan-listing"></a>플랜 목록 정의

**플랜 목록** 탭에서 상업용 Marketplace에 표시할 플랜 이름과 설명을 정의할 수 있습니다. 이 정보는 Microsoft AppSource 목록 페이지에 표시됩니다.

1. **플랜 이름** 상자에는 이 플랜에 대해 이전에 제공한 이름이 표시됩니다. 이는 언제든지 변경할 수 있습니다. 이 이름은 상업용 Marketplace에서 제품 소프트웨어 플랜의 제목으로 표시됩니다.
1. **플랜 설명** 상자에 이 소프트웨어 플랜의 고유한 특징과 제품 내 다른 소프트웨어 플랜과의 차이점을 설명합니다. 이 설명에는 최대 500자까지 포함할 수 있습니다.
1. **초안 저장** 을 선택한 다음, 왼쪽 상단에서 **플랜 개요** 를 선택합니다.

    :::image type="content" source="./media/third-party-license/bronze-plan.png" alt-text="파트너 센터에서 제품의 플랜 목록 페이지에 있는 플랜 개요 링크를 보여 주는 스크린샷.":::

1. 이 제품에 대한 다른 플랜을 만들려면 **플랜 개요** 탭 상단에서 **+ 새 플랜 만들기** 를 선택합니다. 그런 다음, [플랜 만들기](#create-a-plan) 섹션의 단계를 반복합니다. 그렇지 않으면 플랜 만들기를 완료한 경우 다음 섹션인 서비스 ID 복사로 이동합니다.

## <a name="copy-the-service-ids"></a>서비스 ID 복사

다음 단계에서 솔루션 패키지에 매핑할 수 있도록 생성된 각 계획의 서비스 ID를 복사해야 합니다.

- 생성된 각 계획의 서비스 ID를 안전한 장소에 복사합니다. 다음 단계에서 솔루션 패키지에 추가할 것입니다. 서비스 ID는 **플랜 개요** 페이지에 `ISV name.offer name.plan ID` 형식으로 나열됩니다. 예를 들면 Fabrikam.F365.bronze입니다.

    :::image type="content" source="./media/third-party-license/service-id.png" alt-text="플랜 개요 페이지의 스크린샷. 플랜의 서비스 ID가 강조 표시되어 있습니다.":::

## <a name="add-service-ids-to-your-solution-package"></a>솔루션 패키지에 서비스 ID 추가

1. 이전 단계에서 복사한 서비스 ID를 솔루션 패키지에 추가합니다. 방법을 알아보려면 [솔루션에 라이선스 메타데이터 추가](/powerapps/developer/data-platform/appendix-add-license-information-to-your-solution) 및 [앱의 AppSource 패키지 만들기](/powerapps/developer/data-platform/create-package-app-appsource)를 참조하세요.
1. CRM 패키지 .zip 파일을 만든 후 Azure Blob Storage에 업로드합니다. 업로드된 CRM 패키지 .zip 파일을 포함하는 Azure Blob Storage 계정의 SAS URL을 제공해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Dynamics 365 for Customer Engagement & Power Apps 제품 기술 구성 설정](dynamics-365-customer-engage-technical-configuration.md)으로 이동하여 솔루션 패키지를 제품에 업로드합니다.
