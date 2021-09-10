---
title: ISV 앱 라이선스 관리 - Microsoft AppSource 및 Azure Marketplace
description: Microsoft를 통해 ISV 앱 라이선스를 관리하는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.date: 04/30/2021
ms.openlocfilehash: 7d91cacf68cca5dcff377cf151a004d05b4c3fbc
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113111522"
---
# <a name="isv-app-license-management"></a>ISV 앱 라이선스 관리

다음 제안 유형에 적용됩니다.

- Dynamics 365 for Customer Engagement 및 Power Apps

_ISV 앱 라이선스 관리_ 는 Dynamics 365 제품군을 사용하여 솔루션을 빌드하는 ISV(독립 소프트웨어 공급 업체)가 Microsoft에서 제공하는 시스템을 사용하여 솔루션에 대한 라이선스를 관리하고 적용할 수 있도록 합니다. 이 방법을 채택하여 다음을 수행할 수 있습니다.

- 고객이 Office 및 Dynamics 라이선스를 관리하는 데 사용하는 Microsoft 365 관리 센터와 같은 익숙한 도구를 사용하여 솔루션 라이선스를 할당하고 할당을 취소할 수 있도록 합니다.
- 라이선스가 있는 사용자만 솔루션에 액세스할 수 있도록 하려면 런타임에 Power Platform이 라이선스를 적용하도록 합니다.
- 고유한 라이선스 관리 및 적용 시스템을 구축하고 유지 관리하는 데 필요한 노력을 절감합니다.


> [!NOTE]
> ISV 앱 라이선스 관리는 ISV 연결 프로그램에 참여하는 ISV만 가능합니다. Microsoft는 라이선스 판매에 관여하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

ISV 앱 라이선스를 관리하려면 다음 사전 준비 사항을 따라야 합니다.

1. 유효한 [Microsoft 파트너 네트워크 계정](/partner-center/mpn-create-a-partner-center-account)이 있어야 합니다.
1. 상업용 Marketplace 프로그램에 등록해야 합니다. 자세한 내용은 [파트너 센터에서 상업용 Marketplace 계정 만들기](create-account.md)를 참조하세요.
1. [ISV Connect 프로그램](https://partner.microsoft.com/solutions/business-applications/isv-overview)에 등록해야 합니다. 자세한 내용은 [Microsoft Business Applications ISV(Independent Software Vendor) 연결 프로그램 온보딩 가이드](business-applications-isv-program.md)를 참조하세요.
1. 개발자 팀에는 Dataverse 솔루션을 만드는 데 필요한 개발 환경 및 도구가 있어야 합니다. Dataverse 솔루션에는 모델 기반의 애플리케이션이 포함되어 있어야 합니다. 현재는 라이선스 관리 기능을 통해 지원되는 유일한 솔루션 구성 요소의 유형입니다.

## <a name="high-level-process"></a>상위 수준 프로세스

다음 표에서는 ISV 앱 라이선스를 관리하는 고급 프로세스를 보여 줍니다.

| 단계 | 세부 정보 |
| ------------ | ------------- |
| 1단계 - 제품 만들기 | ISV는 파트너 센터에서 제품을 만들고 Microsoft를 통해 이 제품에 대한 라이선스를 관리하도록 선택합니다. 여기에 제품에 대한 하나 이상의 라이선스 플랜을 정의하는 작업이 포함됩니다. 자세한 정보는 [Microsoft AppSource에서 고객 참여용 Dynamics 365 및 Power Apps 제안 만들기](dynamics-365-customer-engage-offer-setup.md)를 참조하세요. |
| 2단계: 패키지 업데이트 | ISV는 라이선스 플랜 정보를 메타데이터로 포함하는 제품에 대한 솔루션 패키지를 만들고 Microsoft AppSource에 게시하기 위해 파트너 센터에 업로드합니다. 자세히 알아보려면 [솔루션에 라이선스 메타데이터 추가](/powerapps/developer/data-platform/appendix-add-license-information-to-your-solution)를 참조하세요. |
| 3단계: 라이선스 구매 | 고객이 AppSource에서 또는 ISV의 웹 사이트에서 직접 ISV의 제품을 검색합니다. 고객은 ISV에서 직접 원하는 플랜에 대한 라이선스를 구매합니다(현재 이러한 제품은 AppSource를 통해 구입할 수 없습니다). |
| 4단계 - 거래 등록 | ISV는 파트너 센터에서 Microsoft에 구매한 제품을 등록합니다. ISV는 [거래 등록](/partner-center/csp-commercial-marketplace-licensing#register-isv-connect-deal-in-deal-registration)의 일부로 고객이 구입한 각 라이선스 플랜의 유형 및 수량을 지정합니다. |
| 5단계: 라이선스 관리 | 라이선스 플랜은 고객이 조직의 [사용자 또는 그룹에](/microsoft-365/commerce/licenses/manage-third-party-app-licenses) 할당할 수 있도록 Microsoft 365 관리 센터에 표시됩니다. 또한 고객은 Power Platform 관리 센터를 통해 해당 테넌트에 애플리케이션을 설치할 수 있습니다. |
| 6단계: 라이선스 검사 수행 | 고객 조직 내의 사용자가 애플리케이션을 실행하려고 하면 Microsoft에서 해당 사용자에게 애플리케이션 실행을 허용하기 전에 라이선스가 있는지 확인합니다. 사용자에게 라이선스가 없는 경우 관리자에게 라이선스에 대해 문의해야 한다는 메시지가 표시됩니다. |
| 7단계: 보고서 보기 | ISV는 일정 기간 동안 및 지역별로 프로비저닝되고 할당된 라이선스에 대한 정보를 볼 수 있습니다. |
|||

## <a name="enabling-app-license-management-through-microsoft"></a>Microsoft를 통해 앱 라이선스 관리 사용

제품을 만들 때 제품에 ISV 앱 라이선스 관리를 사용하도록 설정하는 데 사용하는 두 개의 확인란이 제품 설정 탭에 있습니다.

### <a name="enable-app-license-management-through-microsoft-check-box"></a>Microsoft를 통해 앱 라이선스 관리 사용 확인란

작동 방식은 다음과 같습니다.

- **Microsoft를 통해 앱 라이선스 관리 사용** 확인란을 선택한 후 제품에 대한 라이선스 플랜을 정의할 수 있습니다.
- 고객은 AppSource의 제품 목록 페이지에서 **지금 가져오기** 단추를 볼 수 있습니다. 고객이 이 단추를 선택하여 앱의 라이선스 구매에 대해 문의할 수 있습니다.

### <a name="allow-customers-to-install-my-app-even-if-licenses-are-not-assigned-check-box"></a>라이선스가 할당되지 않았더라도 고객이 내 앱을 설치할 수 있도록 허용 확인란

첫 번째 확인란을 선택하면 **라이선스가 할당되지 않았더라도 고객이 내 앱을 설치할 수 있도록 허용** 상자가 표시됩니다. 이 옵션은 모든 사용자에게 무료로 제공되는 솔루션의 몇 가지 기본 기능을 제공하고 프리미엄 기능에 대해 요금을 부과하려는 “Freemium” 라이선스 전략을 사용하는 경우에 유용합니다. 반대로 제품에 대한 라이선스를 현재 소유하고 있는 테넌트만 AppSource에서 다운로드할 수 있도록 하려면 이 옵션을 선택하지 마세요.

> [!NOTE]
> 이 옵션을 선택하는 경우 라이선스를 요구하지 않도록 솔루션 패키지를 구성해야 합니다.

작동 방식은 다음과 같습니다.

- 모든 AppSource 사용자에게 제품 목록 페이지에 **나에게 연락** 단추와 함께 **지금 가져오기** 단추가 표시되며 제품을 다운로드하고 설치할 수 있습니다.
- 이 옵션을 선택하지 않으면 AppSource에서 사용자의 테넌트에 솔루션에 대한 라이선스가 한 개 이상 있는지 확인한 후 **지금 가져오기** 단추를 표시합니다. 사용자의 테넌트에 라이선스가 없는 경우 **나에게 연락** 단추만 표시됩니다.

제품을 구성하는 방법에 대한 자세한 내용은 [Dynamics 365 for Customer Engagement 및 Power App 제품을 만드는 방법](dynamics-365-customer-engage-offer-setup.md)을 참조하세요.

## <a name="offer-listing-page-on-appsource"></a>AppSource의 제품 목록 페이지

제품을 게시하면 선택한 옵션에 따라 사용자에게 표시되는 단추가 결정됩니다. 이 스크린샷에서는 **지금 가져오기** 및 **나에게 연락** 단추가 있는 AppSource의 제품 목록 페이지를 보여 줍니다.

:::image type="content" source="./media/third-party-license/f365.png" alt-text="AppSource의 제품 목록 페이지 스크린샷. 지금 가져오기 및 나에게 연락 단추가 표시되어 있습니다.":::

***그림 1: Microsoft AppSource의 제품 목록 페이지***

## <a name="next-steps"></a>다음 단계

- [Dynamics 365 제품 계획](marketplace-dynamics-365.md)
- [Dynamics 365 for Customer Engagement 및 Power Apps 제품을 만드는 방법](dynamics-365-customer-engage-offer-setup.md)
