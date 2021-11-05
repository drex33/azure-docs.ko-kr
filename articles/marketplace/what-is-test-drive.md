---
title: Microsoft AppSource 시험 사용이란?
description: Microsoft AppSource 시험 사용 기능에 대한 설명입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/26/2021
ms.openlocfilehash: 9769e52457a65ab3b37135cdbe7d7fdd78e1f1e6
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853855"
---
# <a name="what-is-a-test-drive"></a>시험 사용이란?

시험 사용은 구입 전 체험 옵션을 제공하여 잠재적인 고객에게 제품을 선보임으로써 적절한 잠재 고객을 창출하고 구매 전환율을 높일 수 있는 유용한 방법입니다. 시험 사용은 실제 구현 시나리오에서 제품을 사용합니다. 제품을 사용하는 고객은 비슷한 솔루션을 구입하기 위한 명확한 의도를 보여 줍니다. 이를 통해 더 많은 고급 잠재 고객을 유도할 수 있습니다.

고객도 시험 사용을 활용할 수 있는 이점이 있습니다. 제품을 먼저 사용해 볼 수 있도록 하면 구매 프로세스의 마찰을 줄일 수 있습니다. 또한 시험 사용은 미리 프로비저닝됩니다. 즉, 고객이 제품을 다운로드하거나 설정하거나 구성할 필요가 없습니다.

> [!TIP]
> 상용 marketplace에서 고객의 시험에서 볼 수 있는 내용을 보려면 [Azure Marketplace 무엇 인가요?](/marketplace/azure-marketplace-overview#take-action-on-a-listing) 및 [Microsoft AppSource](/marketplace/appsource-overview)정의를 참조 하세요.

## <a name="how-does-it-work"></a>작동 원리

시험 사용은 요청하는 고객에 대해 솔루션 또는 주문형 애플리케이션을 실행하는 관리되는 인스턴스입니다. 시험 사용 인스턴스가 할당되면 해당 고객이 설정된 기간 동안 사용할 수 있습니다. 기간이 종료된 후에는 다른 고객을 위한 공간을 확보하기 위해 삭제됩니다.

게시자는 파트너 센터에서 시험 사용 설정을 관리하고 구성합니다. 기술 구성 세부 정보는 제품 유형에 따라 달라집니다. 자세한 지침은 [시험 사용 기술 구성](./test-drive-technical-configuration.md)을 참조하세요.

잠재적 고객은 [AppSource](https://appsource.microsoft.com/en-US/)에서 제품의 CTA로 시험 사용을 검색할 수 있습니다. 연락처 정보를 제공하고 제품의 사용 약관 및 개인 정보 취급 방침에 동의한 다음, 미리 구성된 환경에 대한 액세스 권한을 획득하여 일정 기간 동안 사용해 볼 수 있습니다. 고객은 제품의 주요 기능 및 장점에 대한 실습, 자기 주도형 평가판을 받고 게시자는 귀중한 잠재 고객을 얻게 됩니다.

## <a name="types-of-test-drives"></a>시험 사용 유형

제품 유형, 시나리오 및 현재 위치의 마켓플레이스에 따라 다양한 시험 사용을 상업용 마켓플레이스에서 사용할 수 있습니다.

- Azure Resource Manager
    - Azure 애플리케이션
    - SaaS
    - Virtual Machines
- 호스트형 시험 사용
    - Dynamics 365 for Business Central(현재 지원되지 않음)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- 논리 앱(지원 모드에서만)
- Power BI

이러한 시험 사용 중 하나를 구성하는 방법에 대한 자세한 내용은 [시험 사용 기술 구성](./test-drive-technical-configuration.md)을 참조하세요. 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager 시험 사용

이 배포 템플릿에는 솔루션을 구성하는 모든 Azure 리소스가 포함되어 있습니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용합니다. Azure Resource Manager 시험 사용은 다음 제품 유형에 사용할 수 있습니다. 

- Azure 애플리케이션
- SaaS
- 가상 머신

>[!NOTE]
>가상 머신 및 Azure 애플리케이션 제품의 경우 이 옵션이 유일한 시험 사용 옵션입니다.

### <a name="hosted-test-drive-recommended"></a>호스트형 시험 사용(권장)

호스트형 시험 사용은 Microsoft가 시험 사용 사용자 프로비저닝 및 프로비저닝 해제를 수행하는 서비스를 호스트하고 관리하도록 함으로써 설치의 복잡성이 줄어듭니다. Microsoft AppSource에 대한 제품이 있는 경우 Dynamics AX/CRM 인스턴스를 사용하여 연결하는 시험 사용을 빌드합니다. 사용할 수 있는 AppSource 제품 유형은 다음과 같습니다.

- 영업, 서비스, 프로젝트 서비스 및 현장 서비스와 같은 고객 참여 시스템에 대해 [Dynamics 365 for Customer Engagement 및 Power Apps](dynamics-365-customer-engage-offer-setup.md)를 사용합니다.
- 재무, 운영, 제조, 공급망 등의 재무 및 운영 엔터프라이즈 리소스 계획 시스템에 대해 [Dynamics 365 for Operations](./dynamics-365-operations-offer-setup.md)를 사용합니다.

### <a name="logic-app-test-drive"></a>논리 앱 시험 사용

이 유형의 시험 사용은 Microsoft에서 호스트하지 않으며 Dynamics AX/CRM 제품 유형에 대해 ARM(Azure Resource Manager) 템플릿을 사용합니다. Azure 구독에 필요한 리소스를 만들려면 ARM 템플릿을 실행해야 합니다. 논리 앱 시험 사용은 현재 지원 모드 전용이며 Microsoft에서 권장하지 않습니다. 논리 앱 시험 사용을 구성하는 방법에 대한 자세한 내용은 [시험 사용 기술 구성](./test-drive-technical-configuration.md)을 참조하세요.

### <a name="power-bi-test-drive"></a>Power BI 시험 사용

이 유형은 단순히 사용자가 작성한 대시보드에 대한 포함 링크입니다. 대화형 Power BI 시각적 개체만 보여 주는 제품은 이 유형의 시험 사용을 사용해야 합니다.

## <a name="transforming-examples"></a>변환 예

리소스의 아키텍처를 시험 사용으로 변환하는 프로세스는 까다로울 수 있습니다. 현재 아키텍처를 가장 잘 변환하는 방법에 대한 다음 예를 확인하세요.

[웹 사이트 템플릿을 시험 사용으로 변환](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[가상 머신 템플릿을 시험 사용으로 변환](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[기존 Resource Manager 템플릿을 시험 사용으로 변환](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>시험 사용으로부터 잠재 고객 생성

상업용 마켓플레이스 시험 사용은 마케터에 적합한 도구입니다. 더 많은 비즈니스 잠재 고객을 생성하기 위해서는 제품 출시 전에 이러한 시험 사용을 통합하는 것이 좋습니다. 자세한 내용은 [상업용 Marketplace 제품의 잠재 고객](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md)을 참조하세요.

시험 사용 거래를 성사시킨 후에는 [Microsoft Partner Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect)에 거래를 등록합니다. 또한 시험 사용을 활용하여 고객을 확보한 사례를 Microsoft에도 알려 주시기 바랍니다.

## <a name="other-resources"></a>기타 리소스

추가 시험 사용 리소스:

- [시험 사용 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [개요](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)(PDF. 팝업 차단이 해제되어 있는지 확인)

## <a name="next-steps"></a>다음 단계

- [시험 사용 기술 구성](test-drive-technical-configuration.md)