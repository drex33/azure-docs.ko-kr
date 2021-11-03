---
title: 파트너 센터의 Microsoft AppSource에 대 한 Power BI 시각적 제안 계획
description: 파트너 센터에서 Power BI 시각적 제품을 제출 하는 데 필요한 정보를 알아봅니다.
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: 898fcfd97e6c73b530717a99d82f27e0ed3cfe53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082382"
---
# <a name="plan-a-power-bi-visual-offer"></a>Power BI 시각적 제안 계획

이 문서에는 Microsoft [appsource](https://appsource.microsoft.com)에 대 한 파트너 센터에서 Power BI 시각적 제품을 게시 하기 위해 준비 하거나 완료 해야 하는 콘텐츠와 요구 사항이 강조 표시 되어 있습니다. Power BI 시각적 개체는 사용자가 제공 하는 데이터를 시각화 하는 데 도움이 되는 패키지입니다. 누구나 사용자 지정 시각적 개체를 만들고 Power BI 보고서로 가져올 수 있는 단일 .pbiviz 파일로 패키지할 수 있습니다.

시작 하기 전에 파트너 센터에서 [상용 marketplace 계정을 만들고](./create-account.md) 상업적 marketplace 프로그램에 등록 되었는지 확인 합니다. 또한 게시 [프로세스](/office/dev/store/submit-to-appsource-via-partner-center) 및 [지침](/legal/marketplace/rating-review-policies)을 검토 합니다.

## <a name="publishing-benefits"></a>게시 이점

AppSource에 게시할 경우의 이점:

- Microsoft 브랜드를 활용하여 회사를 홍보할 수 있습니다.
- 잠재적으로 AppSource의 Microsoft 365 및 Dynamics 365 사용자는 1억 명이 넘고, Azure Marketplace를 사용하는 조직은 200,000곳이 넘습니다.
- Microsoft 현장 팀 및 통신 판매 팀을 통해 서비스를 홍보할 수 있습니다.

## <a name="overview"></a>개요

만들기 프로세스를 시작 하기 전에 제대로 준비 되었는지 확인 하려면 다음을 읽어 보세요.

다음 몇 가지 항목에서 다루는 주요 게시 단계는 아래와 같습니다.

1. Power BI 시각적 개체를 [패키지](/power-bi/developer/visuals/package-visual) 합니다.
2. 공식 이름, 설명 및 로고와 같은 [마케팅 세부 정보](./gtm-offer-listing-best-practices.md#online-store-offer-details)를 준비 합니다.
3. 사용 약관, 개인 정보 취급 방침, 지원 정책 및 사용자 도움말과 같은 제품의 법률 및 지원 문서에 대 한 링크를 수집 합니다.
4. 시각적 개체 만들기 – 파트너 센터를 사용 하 여 시각적 설명, 마케팅 자료, 법적 정보, 지원 정보 및 자산 사양을 비롯 한 세부 정보를 입력 합니다.
5. 파트너 센터에서 프로세스를 모니터링 합니다. 여기서 AppSource 온 보 딩 팀은 시각적 개체를 테스트 하 고, 유효성을 검사 하 고, 인증 합니다. 인증된 후에는 해당 테스트 환경에서 앱을 검토하고 릴리스합니다. 이 "라이브"는 AppSource에 나열 됩니다.

## <a name="technical-requirements"></a>기술 요구 사항

게시 된 Power BI 시각적 제품을 얻기 위한 기술 요구 사항은 [Power BI 시각적 제품의 기술 구성](power-bi-visual-technical-configuration.md)에 자세히 설명 되어 있습니다.

Power BI visual을 appsource에 제출 하기 전에 Power BI 시각적 개체 [지침](/power-bi/developer/visuals/guidelines-powerbi-visuals) 을 읽고 시각적 개체를 [테스트](/power-bi/developer/visuals/submission-testing) 했는지 확인 합니다.

## <a name="legal-contracts"></a>법적 계약

Power BI 시각적 개체에 대 한 **EULA (최종 사용자 사용권 계약)** 파일을 제공 합니다.

## <a name="offer-listing-details"></a>제품 목록 세부 정보

> [!NOTE]
> 제품 설명이 “이 애플리케이션은 [영어가 아닌 언어]에서만 사용할 수 있습니다”라는 문구로 시작하는 경우 제품 목록 내용을 영어로 작성하지 않아도 됩니다.

제품을 보다 쉽게 만들 수 있도록 관련 항목 중 일부는 미리 준비합니다. 명시된 경우를 제외하고 모두 필요합니다.

시각적 개체를 가장 효과적으로 Power BI 하려면 다음 [지침](./power-bi-visual-offer-listing.md)을 참조 하세요.

- **이름** – [이름은](/office/dev/store/reserve-solution-name) 상용 marketplace에서 제품의 제목으로 표시 됩니다. 이름은 상표로 등록될 수 있습니다. 상표 및 저작권 기호가 아닌 경우 이모지를 포함할 수 없으며 50자로 제한됩니다.
- **요약** -100 자 이하의 줄 바꿈 없이 단일 문장으로 제공 되는 제품의 목적 또는 기능입니다. 이 요약문은 상업용 Marketplace 목록 검색 결과에 사용됩니다.
- **설명** – 상업적 marketplace 목록 개요에 표시 됩니다. 가치 제안, 주요 이점, 의도 된 사용자 기반, 범주 또는 업계 연결, [앱 내 구매](./power-bi-visual-offer-setup.md) 기회, 필요한 공개 및 자세한 내용을 볼 수 있는 링크를 포함 하는 것이 좋습니다. 이 텍스트 상자에는 보다 설득력 있는 설명을 도와주는 다양한 텍스트 편집기 컨트롤이 들어 있습니다. 필요에 따라 서식을 지정하기 위해 HTML 태그를 사용합니다.
- **도움말/개인 정보 취급 방침 링크** – 회사 도움말과 개인 정보 취급 방침에 대한 URL입니다. 귀하는 귀하의 제품이 개인 정보 법률 및 규정을 준수 하는지 확인 해야 합니다.
- **미디어** - 
    - **로고** - **큰** 로고에 대한 PNG 파일입니다. 파트너 센터는 이를 사용하여 기타 필요한 로고 크기를 만듭니다. 원한다면 나중에 다른 이미지로 바꿀 수 있습니다.
    - **스크린샷** - 제품의 작동 방식을 보여 주는 1~5개의 스크린샷입니다. 이미지는 PNG 형식에 1280 x 720 픽셀이며 캡션을 포함해야 합니다.
    - **동영상**(선택 사항) - 제품을 설명하는 동영상을 4개까지 추가할 수 있습니다. 이름, YouTube 또는 Vimeo URL 및 1280 x 720 픽셀 PNG 썸네일을 포함합니다.

>[!NOTE]
> 제품을 제출 하기 전에 제품 목록 [지침](./marketplace-criteria-content-validation.md) 및 [모범 사례](./gtm-offer-listing-best-practices.md) 를 신중 하 게 검토 합니다.
>
> 제품은 상용 marketplace에 게시 되는 일반 [상용 marketplace 인증 정책을](/legal/marketplace/certification-policies#100-general) 준수 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Power BI 시각적 제품 만들기](power-bi-visual-offer-setup.md) 시작