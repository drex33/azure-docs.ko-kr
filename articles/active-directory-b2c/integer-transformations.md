---
title: 사용자 지정 정책의 정수 클레임 변환 예제
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 IEF(Identity Experience Framework) 스키마용 정수 클레임 변환의 예제를 제공합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/10/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: dc637e6369a1dbaaa9a25ad5d7b91b7b5d95ffd6
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131036402"
---
# <a name="integer-claims-transformations"></a>정수 클레임 변환

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD B2C(Azure Active Directory B2C)에서 Identity Experience Framework 스키마의 정수 클레임 변환을 사용하는 예제를 제공합니다. 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

## <a name="adjustnumber"></a>AdjustNumber

숫자 클레임을 늘리거나 줄이고 새 클레임을 반환합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | int | 늘리거나 줄일 수를 포함하는 클레임 유형입니다. `inputClaim` 클레임 값이 null이면 기본값 0이 사용됩니다. |
| InputParameter | 연산자 | 문자열 | 가능한 값은 `INCREMENT`(기본값) 또는 `DECREMENT`입니다.|
| OutputClaim | outputClaim | int | 이 클레임 변환을 호출하고 나면 생성되는 클레임 형식입니다. |

이 클레임 변환을 사용하여 숫자 클레임 값을 늘리거나 줄입니다. 

```xml
<ClaimsTransformation Id="UpdateSteps" TransformationMethod="AdjustNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="steps" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="INCREMENT" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="steps" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>예 1

- 입력 클레임:
    - **inputClaim**: 1
- 입력 매개 변수:
    - **연산자**: INCREMENT
- 출력 클레임:
    - **outputClaim**: 2

### <a name="example-2"></a>예제 2

- 입력 클레임:
    - **inputClaim**: NULL
- 입력 매개 변수:
    - **연산자**: INCREMENT
- 출력 클레임:
    - **outputClaim**: 1


## <a name="assertnumber"></a>AssertNumber

숫자 클레임이 숫자보다 큰지, 작은지, 같은지 또는 같지 않은지를 결정합니다. 

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | int | 두 번째 숫자보다 큰지, 작은지, 같은지 또는 같지 않은지를 비교하는 첫 번째 숫자 클레임입니다. null 값은 예외를 throw합니다. |
| InputParameter | CompareToValue | int | 첫 번째 숫자보다 큰지, 작은지, 같은지 또는 같지 않은지 비교하는 두 번째 숫자입니다. |
| InputParameter | 연산자 | 문자열 | 가능한 값: `LESSTHAN`, `GREATERTHAN`, `GREATERTHANOREQUAL`, `LESSTHANOREQUAL`, `EQUAL`, `NOTEQUAL`. |
| InputParameter | throwError | boolean | 비교 결과가 `true`인 경우 이 어설션에서 오류를 throw해야 하는지 여부를 지정합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. <br />&nbsp;<br />`true`(어설션 모드)로 설정되고 비교 결과가 `true`이면 예외가 throw됩니다. `false`(평가 모드)로 설정된 경우 결과는 값이 `true` 또는 `false`인 새 부울 클레임 유형입니다.| 
| OutputClaim | outputClaim | boolean | `ThrowError`가 `false`로 설정된 경우 이 출력 클레임에는 비교 결과에 따라 `true` 또는 `false`가 포함됩니다. |

### <a name="assertion-mode"></a>어설션 모드

`throwError` 입력 매개 변수가 `true`(기본값)로 설정된 경우 **AssertNumber** 클레임 변환은 항상 [자체 어설션 기술 프로필](self-asserted-technical-profile.md)에서 호출한 [유효성 검사 기술 프로필](validation-technical-profile.md)에서 실행됩니다. 

자체 어설션된 **AssertNumberError** 기술 프로필 메타데이터는 기술 프로필에서 사용자에게 제공하는 오류 메시지를 제어합니다. 오류 메시지는 [지역화](localization-string-ids.md#claims-transformations-error-messages)될 수 있습니다.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="AssertNumberError">You've reached the maximum logon attempts</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

어설션 모드에서 클레임 변환을 호출하는 방법에 대한 자세한 내용은 [AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal), [AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) 및 [AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) 클레임 변환을 참조하세요.

### <a name="assertion-mode-example"></a>어설션 모드 예

다음 예에서는 시도 횟수를 5회 이상으로 어설션합니다. 클레임 변환은 비교 결과에 따라 오류를 throw합니다. 

```xml
<ClaimsTransformation Id="isOverLimit" TransformationMethod="AssertNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="attempts" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="GREATERTHAN" />
    <InputParameter Id="CompareToValue" DataType="int" Value="5" />
    <InputParameter Id="throwError" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```

- 입력 클레임:
    - **inputClaim**: 10
- 입력 매개 변수:
    - **연산자**: GREATERTHAN
    - **CompareToValue**: 5
    - **throwError**: true
- 결과: 오류가 throw됨

### <a name="evaluation-mode-example"></a>평가 모드 예

다음 예에서는 시도 횟수가 5회를 초과하는지 평가 합니다. 출력 클레임에는 비교 결과에 따라 부울 값이 포함됩니다. 클레임 변환에서 오류를 throw하지 않습니다. 

```xml
<ClaimsTransformation Id="isOverLimit" TransformationMethod="AssertNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="attempts" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="GREATERTHAN" />
    <InputParameter Id="CompareToValue" DataType="int" Value="5" />
    <InputParameter Id="throwError" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="attemptsCountExceeded" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- 입력 클레임:
    - **inputClaim**: 10
- 입력 매개 변수:
    - **연산자**: GREATERTHAN
    - **CompareToValue**: 5
    - **throwError**: false
- 출력 클레임:
    - **outputClaim**: true


## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

long 데이터 형식을 문자열 데이터 형식으로 변환합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | 문자열로 변환할 클레임 유형입니다. |
| OutputClaim | outputClaim | 문자열 | 이 클레임 변환을 호출하고 나면 생성되는 클레임 형식입니다. |

이 예제에서는 값 형식이 long인 `numericUserId` 클레임이 값 형식이 문자열인 `UserId` 클레임으로 변환됩니다.

```xml
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
    - **inputClaim**: 12334(long)
- 출력 클레임:
    - **outputClaim**: "12334"(문자열)

