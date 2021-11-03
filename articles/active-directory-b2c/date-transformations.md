---
title: 사용자 지정 정책의 날짜 클레임 변환 예제
description: Azure Active Directory B2C의 IEF(ID 경험 프레임워크) 스키마용 날짜 클레임 변환의 예제를 제공합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/16/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 1f812a0afad78fc81d7844ebf1ec3b9a472f8862
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007394"
---
# <a name="date-claims-transformations"></a>날짜 클레임 변환

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD B2C(Azure Active Directory B2C)에서 ID 경험 프레임워크 스키마의 날짜 클레임 변환을 사용하는 예제를 제공합니다. 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

하나의 날짜 및 시간 클레임(문자열 데이터 형식)이 두 번째 날짜 및 시간 클레임(문자열 데이터 형식)보다 크고 예외가 throw되는지 확인합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | 문자열 | 첫 번째 클레임의 유형이며, 두 번째 클레임보다 나중에 나와야 합니다. |
| InputClaim | rightOperand | 문자열 | 두 번째 클레임의 유형이며, 첫 번째 클레임보다 먼저 나와야 합니다. |
| InputParameter | AssertIfEqualTo | boolean | 왼쪽 피연산자가 오른쪽 피연산자와 같으면 이 어설션에서 오류를 throw해야 하는지 여부를 지정합니다. 왼쪽 연산자가 오른쪽 연산자와 같고 값이 `true`로 설정된 경우 오류가 throw됩니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| InputParameter | AssertIfRightOperandIsNotPresent | boolean | 오른쪽 피연산자가 없으면 이 어설션에서 전달해야 하는지 여부를 지정합니다. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | 두 날짜 /시간 사이의 시간을 동일하게 간주하도록 허용할 시간(밀리초)을 지정합니다(예: 클럭 스큐). |

**AssertDateTimeIsGreaterThan** 클레임 변환은 항상 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)을 통해 호출되는 [유효성 검사 기술 프로필](validation-technical-profile.md)에서 실행됩니다. 자체 어설션된 **DateTimeGreaterThan** 기술 프로필 메타데이터는 기술 프로필에서 사용자에게 제공하는 오류 메시지를 제어합니다. 오류 메시지는 [지역화](localization-string-ids.md#claims-transformations-error-messages)될 수 있습니다.

![AssertStringClaimsAreEqual execution](./media/date-transformations/assert-execution.png)

다음 예제에서는 `currentDateTime` 클레임과 `approvedDateTime` 클레임을 비교합니다. `currentDateTime`이 `approvedDateTime`보다 큰 경우 오류가 throw됩니다. 차이가 5분(30,000밀리초) 이내인 경우 변환에서 값을 동일하게 처리합니다. `AssertIfEqualTo`가 `false`로 설정되어 있으므로 값이 같으면 오류가 throw되지 않습니다.

```xml
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

> [!NOTE]
> 위의 예제에서 `AssertIfEqualTo` 입력 매개 변수를 제거하고 `currentDateTime`이 `approvedDateTime`과 같으면 오류가 throw됩니다. `AssertIfEqualTo` 기본값은 `true`입니다.
>

`login-NonInteractive` 유효성 검사 기술 프로필은 `AssertApprovedDateTimeLaterThanCurrentDateTime` 클레임 변환을 호출합니다.
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

자체 어설션된 기술 프로필은 **login-NonInteractive** 유효성 검사 기술 프로필을 호출합니다.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>예제

- 입력 클레임:
    - **leftOperand**: 2020-03-01T15:00:00.0000000Z
    - **rightOperand**: 2020-03-01T14:00:00.0000000Z
- 결과: 오류가 throw됨

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

**Date** ClaimType을 **DateTime** ClaimType으로 변환합니다. 클레임 변환은 시간 형식을 변환하고 날짜에 12:00:00 AM을 추가합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | date | 변환할 ClaimType입니다. |
| OutputClaim | outputClaim | dateTime | 이 ClaimsTransformation이 호출된 후에 생성되는 ClaimType입니다. |

다음 예제에서는 `dateOfBirth` 클레임(date 데이터 형식)을 다른 `dateOfBirthWithTime` 클레임(dateTime 데이터 형식)으로 변환하는 방법을 보여 줍니다.

```xml
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
    - **inputClaim**: 2020-15-03
- 출력 클레임:
    - **outputClaim**: 2020-15-03T00:00:00.0000000Z

## <a name="convertdatetimetodateclaim"></a>ConvertDateTimeToDateClaim

**DateTime** ClaimType을 **Date** ClaimType으로 변환합니다. 클레임 변환은 날짜에서 시간 형식을 제거합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | dateTime | 변환할 ClaimType입니다. |
| OutputClaim | outputClaim | date | 이 ClaimsTransformation이 호출된 후에 생성되는 ClaimType입니다. |

다음 예제에서는 `systemDateTime` 클레임(dateTime 데이터 형식)을 다른 `systemDate` 클레임(date 데이터 형식)으로 변환하는 방법을 보여줍니다.

```xml
<ClaimsTransformation Id="ConvertToDate" TransformationMethod="ConvertDateTimeToDateClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDate" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
  - **inputClaim**: 2020-15-03T11:34:22.0000000Z
- 출력 클레임:
  - **outputClaim**: 2020-15-03

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

현재 UTC 날짜 및 시간을 가져오고, 이 값을 ClaimType에 추가합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | dateTime | 이 ClaimsTransformation이 호출된 후에 생성되는 ClaimType입니다. |

```xml
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

* 출력 클레임:
    * **currentDateTime**: 2020-15-03T11:40:35.0000000Z

## <a name="datetimecomparison"></a>DateTimeComparison

한 dateTime이 다른 dateTime보다 나중인지, 이전인지 또는 같은지 확인합니다. 결과는 `true` 또는 `false` 값이 있는 새 ClaimType 부울입니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | dateTime | 두 번째 dateTime보다 이전인지 또는 나중인지를 비교할 첫 번째 dateTime입니다. null 값은 예외를 throw합니다. |
| InputClaim | secondDateTime | dateTime | 첫 번째 dateTime보다 이전인지 또는 나중인지를 비교할 두 번째 dateTime입니다. Null 값은 현재 datetTime로 처리됩니다. |
| InputParameter | operator | 문자열 | same(같음), later than(보다 이후) 또는 earlier than(보다 이전) 값 중 하나입니다. |
| InputParameter | timeSpanInSeconds | int | 첫 번째 datetime에 시간 간격을 추가합니다. |
| OutputClaim | result | boolean | 이 ClaimsTransformation이 호출된 후에 생성되는 ClaimType입니다. |

이 클레임 변환을 사용하여 두 ClaimTypes가 서로 같은지, 하나가 더 나중인지 또는 이전인지를 확인합니다. 예를 들어 사용자가 TOS(서비스 약관)에 마지막으로 동의한 시간을 저장할 수 있습니다. 3개월 후 사용자에게 TOS에 다시 액세스하도록 요청할 수 있습니다.
클레임 변환을 실행하려면 먼저 현재 dateTime 및 사용자가 TOS에 마지막으로 동의한 시간을 가져와야 합니다.

```xml
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
    - **firstDateTime**: 2020-01-01T00:00:00.100000Z
    - **secondDateTime**: 2020-04-01T00:00:00.100000Z
- 입력 매개 변수:
    - **operator**: 보다 나중
    - **timeSpanInSeconds**: 7776000(90일)
- 출력 클레임:
    - **result**: true
