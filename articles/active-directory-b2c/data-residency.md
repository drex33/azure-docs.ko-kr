---
title: 지역 가용성 및 데이터 상주
titleSuffix: Azure AD B2C
description: 지역 가용성, 데이터 보존, Azure Active Directory B2C 미리 보기 테넌트에 관한 정보입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/16/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 92297e63739bca85a640120152ee864b85f0f640
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530366"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: 지역 가용성 및 데이터 상주

Azure AD B2C ID 데이터는 테넌트 생성 시 제공된 국가/지역에 따라 지리적 위치에 저장됩니다.

지역 가용성 및 데이터 상주는 Azure AD B2C에 적용되는 두 가지 다른 개념입니다. 이 문서에서는 두 개념의 차이점을 설명하고, 해당 개념이 Azure 및 Azure AD B2C에 적용되는 방식을 비교합니다.

Azure AD B2C는 **미국, 유럽 또는 아시아 태평양 또는 오스트레일리아** 의 **데이터 보존** 을 위한 옵션을 통해 **일반적으로 전 세계에서 사용할 수 있습니다**.

[지역 가용성](#region-availability)은 서비스를 사용할 수 있는 위치를 나타냅니다.

[데이터 상주](#data-residency)는 사용자 데이터가 저장된 위치를 나타냅니다.

## <a name="region-availability"></a>지역 가용성

Azure AD B2C는 Azure 퍼블릭 클라우드를 통해 전 세계에서 사용할 수 있습니다. Azure의 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/) 페이지 및 [Azure Active Directory B2C 가격 계산기](https://azure.microsoft.com/pricing/details/active-directory-b2c/)에서 이 기능의 예를 확인할 수 있습니다.

## <a name="data-residency"></a>데이터 상주

Azure AD B2C는 미국, 유럽 또는 아시아 태평양 지역 또는 오스트레일리아에 사용자 데이터를 저장합니다.

데이터 상주는 [Azure AD B2C 테넌트를 만들](tutorial-create-tenant.md) 때 선택하는 국가/지역에 따라 결정됩니다.

![국가 또는 지역을 선택하는 테넌트 만들기 양식의 스크린샷.](./media/data-residency/data-residency-b2c-tenant.png)

다음 국가/지역의 경우 데이터는 **미국** 에 상주합니다.

> 미국(US), 캐나다(CA), 코스타리카(CR), 도미니카 공화국(DO), 엘살바도르(SV), 과테말라(GT), 멕시코(MX), 파나마(PA), 푸에르토리코(PR) 및 트리니다드 토바고(TT)

다음 국가/지역의 경우 데이터는 **유럽** 에 상주합니다.

> 알제리(DZ), 오스트리아(AT), 아제르바이잔(AZ), 바레인(BH), 벨라루스(BY), 벨기에(BE), 불가리아(BG), 크로아티아(HR), 키프로스(CY), 체코 공화국(CZ), 덴마크(DK), 이집트(EG), 에스토니아(EE), 핀란드(FT), 프랑스(FR), 독일(DE), 그리스(GR), 헝가리(HU), 아이슬란드(IS), 아일랜드(IE), 이스라엘(IL), 이탈리아(IT), 요르단(JO), 카자흐스탄(KZ), 케냐(KE), 쿠웨이트(KW), 라트비아(LV), 레바논(LB), 리히텐슈타인(LI), 리투아니아(LT), 룩셈부르크(LU), 북마케도니아(ML), 몰타(MT), 몬테네그로(ME), 모로코(MA), 네덜란드(NL), 나이지리아(NG), 노르웨이(NO), 오만(OM), 파키스탄(PK), 폴란드(PL), 포르투갈(PT), 카타르(QA), 루마니아(RO), 러시아(RU), 사우디아라비아(SA), 세르비아(RS), 슬로바키아(SK), 슬로베니아(ST), 남아프리카 공화국(ZA), 스페인(ES), 스웨덴(SE), 스위스(CH), 튀니지(TN), 터키(TR), 우크라이나(UA), 아랍에미리트(AE), 영국(GB)

다음 국가/지역의 경우 데이터는 **아시아 태평양** 에 상주합니다.

> 아프가니스탄(AF), 홍콩 특별행정구(HK), 인도(IN), 인도네시아(ID), 일본(JP), 대한민국(KR), 말레이시아(MY), 필리핀(PH), 싱가포르(SG), 스리랑카(LK), 대만(TW), 태국(TH)

다음 국가/지역의 경우 데이터는 **오스트레일리아** 에 상주합니다.

> 오스트레일리아 및 뉴질랜드

다음 국가/지역은 목록에 추가되고 있습니다. 이제 위의 국가/지역을 선택하여 Azure AD B2C를 사용할 수 있습니다.

> 아르헨티나, 브라질, 칠레, 콜롬비아, 에콰도르, 이라크, 파라과이, 페루, 우루과이, 베네수엘라

## <a name="remote-profile-solution"></a>원격 프로필 솔루션

Azure AD B2C [사용자 지정 정책](custom-policy-overview.md)을 사용하면 [RESTful API 서비스](api-connectors-overview.md)와 통합하여 원격 데이터베이스(예: 마케팅 데이터베이스, CRM 시스템 또는 기간 업무 앱)에서 사용자 프로필을 저장하고 읽을 수 있습니다.  
- 가입 및 프로필 편집 흐름 중 Azure AD B2C는 사용자 지정 REST API를 호출하여 사용자 프로필을 원격 데이터 원본에 유지합니다. 사용자의 자격 증명은 Azure AD B2C 디렉터리에 저장됩니다. 
- 로그인할 때 로컬 또는 소셜 계정으로 자격 증명 유효성 검사 후 Azure AD B2C는 사용자의 고유 식별자를 사용자 기본 키(메일 주소 또는 사용자 objectId)로 보내는 REST API를 호출합니다. REST API는 원격 데이터베이스에서 데이터를 읽고 사용자 프로필을 반환합니다.  

가입, 프로필 편집 또는 로그인이 완료되면 Azure AD B2C는 애플리케이션으로 반환되는 액세스 토큰에 사용자 프로필을 포함합니다. 자세한 내용은 GitHub의 [Azure AD B2C 원격 프로필 샘플 솔루션](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 테넌트를 만듭니다](tutorial-create-tenant.md).
