---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책의 BuildingBlocks 요소를 지정하는 방법을 설명합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 92af0923e7422e6b99f7c99526cade2f4d49686d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131008088"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**BuildingBlocks** 요소는 [TrustFrameworkPolicy](trustframeworkpolicy.md) 요소 내에 추가됩니다.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
    <DisplayControls>
      ...
    </DisplayControls>
 </BuildingBlocks>
```

**BuildingBlocks** 요소에는 다음 요소가 포함됩니다. 이러한 요소는 정의된 순서대로 지정해야 합니다.

- [ClaimsSchema](claimsschema.md) - 정책의 일부로 참조할 수 있는 클레임 형식을 정의합니다. 클레임 스키마에서 클레임 형식을 정의합니다. 클레임 형식은 대다수 프로그래밍 언어의 변수와 비슷합니다. 클레임 형식을 사용하여 애플리케이션 사용자의 데이터를 수집하거나, 소셜 ID 공급자에서 클레임을 수신하거나, 사용자 지정 REST API에서 데이터를 전송 및 수신하거나, 사용자 지정 정책에 사용되는 내부 데이터를 저장할 수 있습니다.

- [Predicates 및 PredicateValidationsInput](predicates.md) - 적절한 형식의 데이터만 클레임에 입력하도록 유효성 검사 프로세스를 수행할 수 있습니다.

- [ClaimsTransformations](claimstransformations.md) - 정책에서 사용할 수 있는 클레임 변환의 목록을 포함합니다.  클레임 변환에서는 특정 클레임을 다른 클레임으로 변환합니다. 클레임 변환에서는 다음과 같은 변환 방법을 지정합니다.
  - 문자열 클레임의 대/소문자를 지정한 항목으로 변경합니다. 예를 들어 문자열을 소문자에서 대문자로 변경할 수 있습니다.
  - 두 클레임을 비교한 다음 한 클레임과 true(두 클레임이 일치함을 나타냄)를 반환하거나, 두 클레임이 일치하지 않는 경우 false를 반환합니다.
  - 정책에 제공된 매개 변수에서 문자열 클레임을 만듭니다.
  - 난수 생성기를 사용하여 임의 문자열을 만듭니다.
  - 입력한 형식 문자열에 따라 클레임 서식을 지정합니다. 이 변환에서는 C# `String.Format` 메서드를 사용합니다.

- InputValidation - 이 요소를 사용하면 *and* 및 *or* 과 비슷한 부울 집계를 수행할 수 있습니다.

- [ContentDefinitions](contentdefinitions.md) - 사용자 경험에서 사용할 HTML5 템플릿의 URL을 포함합니다. 사용자 지정 정책에서 콘텐츠 정의는 사용자 경험의 지정된 단계에 사용되는 HTML5 페이지 URI를 정의합니다. 로그인/등록, 암호 재설정, 오류 페이지 등을 예로 들 수 있습니다. HTML5 파일의 LoadUri를 재정의하여 모양과 느낌을 수정할 수 있습니다. 필요에 따라 새 콘텐츠 정의를 만들 수도 있습니다. 이 요소는 지역화 ID를 사용하는 지역화된 리소스 참조를 포함할 수 있습니다.

- [Localization](localization.md) - 여러 언어를 지원할 수 있습니다. 정책에서 지역화를 지원하면 정책에서 지원되는 언어 목록을 설정하고 기본 언어를 선택할 수 있습니다. 언어별 문자열 및 컬렉션도 지원됩니다.

- [DisplayControls](display-controls.md) - 페이지에 표시되는 컨트롤을 정의합니다. 표시 컨트롤에는 특수 기능이 있으며 백 엔드 유효성 검사 기술 프로필과 상호 작용합니다. 
