---
title: 사용자 지정 정책의 부울 클레임 변환 예제
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 IEF(ID 경험 프레임워크) 스키마용 부울 클레임 변환의 예제를 제공합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 7ec9e12d8418845e163bd1cc2e578814720c96f2
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131008126"
---
# <a name="boolean-claims-transformations"></a>부울 클레임 변환

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD B2C(Active Directory B2C) 내 ID 경험 프레임워크 스키마의 부울 클레임 변환 사용을 위한 예제를 제공합니다. 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

## <a name="andclaims"></a>AndClaims

두 부울 inputClaim의 And 연산을 수행하고 연산 결과를 사용하여 outputClaim을 설정합니다.

| 항목  | TransformationClaimType  | 데이터 형식  | 메모 |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | 평가할 첫 번째 ClaimType입니다. |
| InputClaim | inputClaim2  | boolean | 평가할 두 번째 ClaimType입니다. |
|OutputClaim | outputClaim | boolean | 이 클레임 변환을 호출하고 나면 생성되는 ClaimType(true 또는 false)입니다. |

다음 클레임 변환은 두 부울 ClaimType `isEmailNotExist` 및 `isSocialAccount`의 And 연산을 수행하는 방법을 보여 줍니다. 두 입력 클레임의 값이 모두 `true`이면 출력 클레임 `presentEmailSelfAsserted`는 `true`로 설정됩니다. 소셜 계정 전자 메일이 비어 있는 경우에 한해 오케스트레이션 단계에서 전제 조건을 사용하여 자체 어설션된 페이지를 미리 설정할 수 있습니다.

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-andclaims"></a>AndClaims의 예

- 입력 클레임:
    - **inputClaim1**: true
    - **inputClaim2**: false
- 출력 클레임:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

두 클레임의 부울 값이 같은지 확인하고 같지 않으면 예외를 throw합니다.

| 항목 | TransformationClaimType  | 데이터 형식  | 메모 |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | 어설션할 ClaimType입니다. |
| InputParameter |valueToCompareTo | boolean | 비교할 값(true 또는 false)입니다. |

**AssertBooleanClaimIsEqualToValue** 클레임 변환은 항상 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)을 통해 호출되는 [유효성 검사 기술 프로필](validation-technical-profile.md)에서 실행됩니다. **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** 자체 어설션된 기술 프로필 메타데이터는 기술 프로필이 사용자에게 표시하는 오류 메시지를 제어합니다. 오류 메시지는 [지역화](localization-string-ids.md#claims-transformations-error-messages)될 수 있습니다.

![AssertStringClaimsAreEqual execution](./media/boolean-transformations/assert-execution.png)

다음 클레임 변환은 `true` 값을 사용하여 부울 ClaimType의 값을 확인하는 방법을 보여 줍니다. `accountEnabled` ClaimType의 값이 false이면 오류 메시지가 throw됩니다.

```xml
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


`login-NonInteractive` 유효성 검사 기술 프로필은 `AssertAccountEnabledIsTrue` 클레임 변환을 호출합니다.

```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

자체 어설션된 기술 프로필은 **login-NonInteractive** 유효성 검사 기술 프로필을 호출합니다.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example-of-assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue의 예

- 입력 클레임:
    - **inputClaim**: false
    - **valueToCompareTo**: true
- 결과: 오류가 throw됨

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

클레임의 부울 값이 `true` 또는 `false`와 같은지 확인하고 압축 결과를 반환합니다.

| 항목 | TransformationClaimType  | 데이터 형식  | 메모 |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | inputClaim | boolean | 어설션할 ClaimType입니다. |
| InputParameter |valueToCompareTo | boolean | 비교할 값(true 또는 false)입니다. |
| OutputClaim | compareResult | boolean | 이 ClaimsTransformation이 호출된 후에 생성되는 ClaimType입니다. |

다음 클레임 변환은 `true` 값을 사용하여 부울 ClaimType의 값을 확인하는 방법을 보여 줍니다. `IsAgeOver21Years` ClaimType의 값이 `true`와 같으면 클레임 변환에서 `true`를 반환하고 다르면 `false`로 반환합니다.

```xml
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue의 예

- 입력 클레임:
    - **inputClaim**: false
- 입력 매개 변수:
    - **valueToCompareTo**: true
- 출력 클레임:
    - **compareResult**: false

## <a name="notclaims"></a>NotClaims

부울 inputClaim의 Not 연산을 수행하고 연산 결과를 사용하여 outputClaim을 설정합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolean | 연산을 수행할 클레임입니다. |
| OutputClaim | outputClaim | boolean | 이 ClaimsTransformation을 호출하고 나면 생성되는 ClaimType(true 또는 false)입니다. |

클레임에 대해 논리 부정을 수행하려면 다음 클레임 변환을 사용합니다.

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-notclaims"></a>NotClaims의 예

- 입력 클레임:
    - **inputClaim**: false
- 출력 클레임:
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims

두 부울 inputClaim의 or을 계산하고 연산 결과를 사용하여 outputClaim을 설정합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | 평가할 첫 번째 ClaimType입니다. |
| InputClaim | inputClaim2 | boolean | 평가할 두 번째 ClaimType입니다. |
| OutputClaim | outputClaim | boolean | 이 ClaimsTransformation을 호출하고 나면 생성되는 ClaimType(true 또는 false)입니다. |

다음 클레임 변환은 두 부울 ClaimType의 `Or` 연산을 수행하는 방법을 보여 줍니다. 클레임 중 하나의 값이 `true`이면 오케스트레이션 단계에서 전제 조건을 사용하여 자체 어설션된 페이지를 미리 설정할 수 있습니다.

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-orclaims"></a>OrClaims의 예

- 입력 클레임:
    - **inputClaim1**: true
    - **inputClaim2**: false
- 출력 클레임:
    - **outputClaim**: true
