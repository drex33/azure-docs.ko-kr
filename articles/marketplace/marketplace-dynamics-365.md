---
title: Microsoft AppSource에 대한 Dynamics 365 제품 플랜 - Azure
description: Microsoft AppSource에 대한 Dynamics 365 제품 플랜
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vamahtan
ms.author: vamahtan
ms.date: 04/30/2021
ms.openlocfilehash: f83fb2b1dbcadf9d40431c7670a1d8618557b047
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866960"
---
# <a name="plan-a-microsoft-dynamics-365-offer"></a>Dynamics 365 제품 플랜

이 문서에서는 상업용 Marketplace에 있는 Microsoft AppSource의 Dynamics 365 제품에 대한 다양한 옵션 및 기능을 설명합니다. AppSource는 Microsoft 365, Dynamics 365, Power Apps 및 Power BI를 기반으로 하거나 확장하는 제품을 포함합니다.

시작하기 전에 [파트너 센터](./create-account.md)에서 상업용 Marketplace 계정을 만들고 상업용 Marketplace 프로그램에 해당 계정이 등록되어 있는지 확인합니다. [게시 프로세스 및 지침](/office/dev/store/submit-to-appsource-via-partner-center)을 검토합니다.

## <a name="licensing-options"></a>라이선싱 옵션

새 제품 게시를 준비할 때 선택할 라이선싱 옵션을 결정해야 합니다. 파트너 센터에서 제품을 만들 때 나중에 제공해야 할 추가 정보를 결정합니다.

Dynamics 365 제품에 사용할 수 있는 라이선싱 옵션은 다음과 같습니다.

| 라이선싱 옵션 | 트랜잭션 프로세스 |
| --- | --- |
| 연락처 | CRM(고객 관계 관리) 시스템을 연결하여 고객 연락처 정보를 수집합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 고객 세부 정보는 제품 이름, ID 및 Marketplace(고객이 제품을 찾은 소스)와 함께 구성된 CRM 시스템으로 전송됩니다. CRM을 구성하는 방법에 대한 자세한 정보는 제품 유형에 있는 **제품 설정** 페이지의 **잠재 고객** 섹션을 참조하세요. |
| 평가판(목록) | 1개월, 3개월, 6개월 평가판을 고객에게 제공합니다. 평가판을 나열하는 제품은 서비스에 의해 생성, 관리 및 구성되며 Microsoft에서 관리하는 구독을 포함하지 않습니다. |
| 지금 받기(무료) | 고객에게 평가판으로 제공하는 제품을 나열합니다. |
| 지금 받기 | 파트너 센터에서 ISV app0 라이선스를 관리할 수 있습니다.<br>현재 다음 제품 유형에만 사용할 수 있습니다.<ul><li>Dynamics 365 for Customer Engagement 및 Power Apps</li></ul><br>이 옵션에 대한 자세한 내용은 [ISV 앱 라이선스 관리](isv-app-license.md)를 참조하세요. |
|||

## <a name="test-drive"></a>시험 사용

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>잠재 고객

파트너 센터를 통해 제품을 상업용 Marketplace에 게시하는 경우 CRM(고객 관계 관리) 시스템에 연결합니다. 이렇게 하면 누군가 제품에 관심을 보이거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다. 시험 사용을 하도록 설정하려면 CRM에 연결해야 합니다. 그러지 않으면 CRM에 연결하는 것은 선택 사항입니다. 파트너 센터는 Azure 테이블, Dynamics 365 Customer Engagement, HTTPS 엔드포인트, Marketo 및 Salesforce를 지원합니다.

## <a name="legal"></a>Legal

**사용 약관** 을 만듭니다. 고객은 이러한 사용 약관에 동의해야 제품을 사용할 수 있습니다. Microsoft에는 표준 계약이 있지만 Dynamics 365 제품에는 적용되지 않습니다.

## <a name="offer-listing-details"></a>제품 목록 세부 정보

다음은 Microsoft AppSource에서 제품 정보를 표시하는 방법의 예입니다. 나열된 가격은 예제 목적으로만 사용되며 실제 비용은 반영되지 않습니다.

:::image type="content" source="media/dynamics-365/example-dynamics-365-operations.png" alt-text="제품이 Microsoft AppSource에서 이렇게 표시됩니다.":::

> [!NOTE]
> 제품 설명이 “이 애플리케이션은 [영어가 아닌 언어]에서만 사용할 수 있습니다”라는 문구로 시작하는 경우 제품 목록 내용을 영어로 작성하지 않아도 됩니다.

제품을 보다 쉽게 만들 수 있도록 관련 항목 중 일부는 미리 준비합니다. 명시된 경우를 제외하고 모두 필요합니다.

- **이름** - 이 이름은 상업용 Marketplace에서 제품 목록의 제목으로 표시됩니다. 이름은 상표로 등록될 수 있습니다. 상표 및 저작권 기호가 아닌 경우 이모지를 포함할 수 없으며 50자로 제한됩니다.
- **검색 결과 요약** - 줄 바꿈 없이 100자 이하의 한 문장으로 제품의 목적 또는 기능을 설명합니다. 이 요약문은 상업용 Marketplace 목록 검색 결과에 사용됩니다.
- **설명** - 이 설명은 상업용 Marketplace 목록 개요에 표시됩니다. 가치 제안, 주요 이점, 대상 사용자 베이스, 범주 또는 산업 협회, 앱 내 구매 기회, 필요한 정보를 공개하고 자세한 정보를 볼 수 있는 링크를 포함하는 것이 좋습니다. 이 텍스트 상자에는 보다 설득력 있는 설명을 도와주는 다양한 텍스트 편집기 컨트롤이 들어 있습니다. 필요에 따라 서식을 지정하기 위해 HTML 태그를 사용합니다.
- **검색 키워드**(선택 사항) - 고객이 제품을 찾는 데 사용할 수 있는 최대 3개의 검색 키워드입니다. 제품 **이름** 및 **설명** 은 포함할 필요가 없습니다. 해당 텍스트는 검색에 자동으로 포함됩니다.
- **앱이 함께 작동** 하는 제품(선택 사항) - 제품에서 사용하는 최대 3 개 제품의 이름입니다.
- **도움말 및 개인 정보 취급 방침 링크** - 제품의 도움말과 회사의 개인 정보 취급 방침에 대한 URL입니다. 앱이 개인 정보 보호 법률 및 규정을 준수하는지 확인할 책임은 본인에게 있습니다.
- **연락처 정보**
  - **고객 지원팀 연락처** - 고객이 티켓을 열 때 사용할 Microsoft 파트너의 이름, 전화 번호 및 이메일입니다. 고객 지원팀 웹 사이트의 URL도 포함해야 합니다.
  - **엔지니어링 연락처** - 제품에 문제가 있을 때 Microsoft에서 직접 사용할 수 있는 이름, 전화 번호 및 이메일입니다. 이 연락처 정보는 상업용 Marketplace에 표시되지 않습니다.
- **지원 문서** - 백서, 브로슈어, 검사 목록 또는 PowerPoint 프레젠테이션과 같은 고객 관련 문서를 PDF 형식으로 3개까지 제공할 수 있습니다.
- **미디어**
    - **로고** - **큰** 로고에 대한 PNG 파일입니다. 파트너 센터는 이를 사용하여 기타 필요한 로고 크기를 만듭니다. 원한다면 나중에 다른 이미지로 바꿀 수 있습니다.
    - **스크린샷** - 제품의 작동 방식을 보여 주는 1~5개의 스크린샷입니다. 이미지는 PNG 형식에 1280 x 720 픽셀이며 캡션을 포함해야 합니다.
    - **동영상**(선택 사항) - 제품을 설명하는 동영상을 4개까지 추가할 수 있습니다. 이름, YouTube 또는 Vimeo URL 및 1280 x 720 픽셀 PNG 썸네일을 포함합니다.

> [!Note]
> 제품은 상업용 Marketplace에 게시되는 일반 [상업용 Marketplace 인증 정책을](/legal/marketplace/certification-policies#100-general) 충족해야 합니다.

## <a name="additional-sales-opportunities"></a>추가 영업 기회

Microsoft에서 지원하는 마케팅 및 영업 채널을 옵트인하도록 선택할 수 있습니다. 파트너 센터에서 제품을 만들 때 **Microsoft와 공동 판매** 프로세스가 끝날 때쯤 탭 두 개가 표시됩니다. 이 옵션을 사용하면 Microsoft 영업 팀에서 고객의 요구를 평가할 때 IP 공동 판매에 적합한 솔루션을 고려할 수 있습니다. 평가를 위해 제품을 준비하는 방법에 대한 세부 정보는 [파트너 센터의 공동 판매 옵션](./co-sell-configure.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

위에서 설명한 계획 항목을 고려한 후에는 다음 중 하나(왼쪽 목차에서 제공)를 선택하여 제품 만들기를 시작합니다.

| 게시 가이드    | 참고  |
| :------------------- | :-------------------|
| [Dynamics 365 for Operations](./dynamics-365-operations-offer-setup.md) | Enterprise Edition용으로 빌드하는 경우 먼저 이러한 추가 [게시 프로세스 및 지침](/dynamics365/fin-ops-core/dev-itpro/lcs-solutions/lcs-solutions-app-source)을 검토합니다. 제품 유형에는 상거래, 재무, 인적 자원, 프로젝트 작업 및 공급망 관리가 포함됩니다. |
| [Dynamics 365 for Business Central](dynamics-365-business-central-offer-setup.md) | 해당 없음 |
| [Dynamics 365 for Customer Engagement 및 Power Apps](dynamics-365-customer-engage-offer-setup.md) | 먼저 이러한 추가 [게시 프로세스 및 지침](/dynamics365/customer-engagement/developer/publish-app-appsource)을 검토합니다. 제품 유형에는 고객 서비스, 고객 음성, 프로젝트 작업, 현장 서비스, 마케팅, 혼합 현실, Power Apps, Power Automate, Power Virtual Agents, Project Service Automation 및 판매 등이 있습니다. |
|
