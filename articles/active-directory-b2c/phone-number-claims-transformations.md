---
title: 사용자 지정 정책의 전화 번호 클레임 변환
titleSuffix: Azure AD B2C
description: Azure AD B2C의 전화 번호 클레임 변환에 대한 사용자 지정 정책 참조.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: e22bb4ebc3a115bdde874b88675547ef2db1cd23
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131044478"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Azure AD B2C의 전화 번호 클레임 변환 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD B2C(Azure Active Directory B2C)에서 Identity Experience Framework 스키마의 전화 번호 클레임 변환을 사용하기 위한 참조 및 예제를 제공합니다. 일반적인 클레임 변환에 대한 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

`phoneNumber` 데이터 형식을 `string` 데이터 형식으로 변환합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  문자열로 변환할 ClaimType입니다. |
| OutputClaim | phoneNumberString | 문자열 | 이 클레임 변환을 호출하고 나면 생성되는 ClaimType입니다. |

이 예제에서는 값 형식이 `phoneNumber`인 cellPhoneNumber 클레임은 값 형식이 `string`인 cellPhone 클레임으로 변환됩니다.

```xml
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
  - **phoneNumber**: +11234567890(phoneNumber)
- 출력 클레임:
  - **phoneNumberString**: +11234567890(문자열)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

이 클레임 변환은 전화 번호의 형식에 대한 유효성을 검사합니다. 유효한 형식이면 Azure AD B2C에서 사용하는 표준 형식으로 변경합니다. 제공된 전화 번호의 형식이 잘못된 경우 오류 메시지가 반환됩니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | 문자열 |  전화 번호에 대한 문자열 클레임입니다. 전화 번호는 국제 형식이어야 하며 앞에 "+"와 국가/지역 코드로 완료해야 합니다. 입력 클레임 `country`가 제공되는 경우 전화 번호는 지역 형식(국가/지역 코드 제외)입니다. |
| InputClaim | country | 문자열 | [선택 사항] ISO3166 형식의 전화 번호 국가/지역 코드에 대한 문자열 클레임(2자 ISO-3166 국가/지역 코드). |
| OutputClaim | outputClaim | phoneNumber | 이 클레임 변환의 결과입니다. |

**ConvertStringToPhoneNumberClaim** 클레임 변환은 항상 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md) 또는 [표시 컨트롤](display-controls.md)을 통해 호출되는 [유효성 검사 기술 프로필](validation-technical-profile.md)에서 실행됩니다. **UserMessageIfClaimsTransformationInvalidPhoneNumber** 자체 어설션된 기술 프로필 메타데이터는 사용자에게 표시되는 오류 메시지를 제어합니다.

![오류 메시지 실행 경로 다이어그램](./media/phone-authentication/assert-execution.png)

이 클레임 변환을 사용하여 제공된 문자열 클레임이 유효한 전화 번호인지 확인할 수 있습니다. 두 값이 같지 않으면 오류 메시지가 throw됩니다. 다음 예제에서는 **phoneString** ClaimType이 실제로 유효한 전화 번호인지 확인한 다음, 표준 Azure AD B2C 형식으로 전화 번호를 반환합니다. 그렇지 않으면 오류 메시지가 throw됩니다.

```xml
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

이 클레임 변환이 포함된 유효성 검사 기술 프로필을 호출하는 자체 어설션된 기술 프로필은 오류 메시지를 정의할 수 있습니다.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>예 1

- 입력 클레임:
  - **phoneNumberString**: 033 456-7890
  - **국가**: DK
- 출력 클레임:
  - **outputClaim**: +450334567890

### <a name="example-2"></a>예제 2

- 입력 클레임:
  - **phoneNumberString**: +1(123) 456-7890
- 출력 클레임:
  - **outputClaim**: +11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

입력 클레임에서 국가/지역 코드와 국가 번호를 추출하고, 제공된 전화 번호가 올바르지 않은 경우 필요에 따라 예외를 throw합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | 문자열 | 전화 번호의 문자열 클레임입니다. 전화 번호는 국제 형식이어야 하며 앞에 "+"와 국가/지역 코드로 완료해야 합니다. |
| InputParameter | throwExceptionOnFailure | boolean | [선택 사항] 전화 번호가 잘못된 경우 예외가 throw되는지 여부를 나타내는 매개 변수입니다. 기본값은 False입니다. |
| InputParameter | countryCodeType | 문자열 | [선택 사항] 출력 클레임에서 국가/지역 코드의 형식을 나타내는 매개 변수입니다. 사용 가능한 값은 **CallingCode**(국가/지역에 대한 국제 호출 코드(예: +1)) 또는 **ISO3166**(2자 ISO-3166 국가/지역 코드)입니다. |
| OutputClaim | nationalNumber | 문자열 | 전화 번호의 국가별 번호에 대한 문자열 클레임입니다. |
| OutputClaim | countryCode | 문자열 | 전화 번호의 국가/지역 코드에 대한 문자열 클레임입니다. |


**GetNationalNumberAndCountryCodeFromPhoneNumberString** 클레임 변환이 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md) 또는 [표시 제어 작업](display-controls.md#display-control-actions)을 통해 호출된 [효율성 검사 기술 프로필](validation-technical-profile.md)에서 실행된 경우 **UserMessageIfPhoneNumberParseFailure** 자체 어설션된 기술 프로필 메타데이터가 사용자에게 표시되는 오류 메시지를 제어합니다.

![오류 메시지 실행 경로 다이어그램](./media/phone-authentication/assert-execution.png)

이 클레임 변환을 사용하여 전체 전화 번호를 국가/지역 코드와 국가별 번호로 분할할 수 있습니다. 제공된 전화 번호가 잘못된 경우 오류 메시지를 throw하도록 선택할 수 있습니다.

다음 예제에서는 전화 번호를 국가별 번호와 국가/지역 코드로 분할하려고 합니다. 전화 번호가 유효한 경우 전화 번호는 국가별 번호로 재정의됩니다. 전화 번호가 잘못된 경우 예외가 throw되지 않으며 전화 번호에 원래 값이 남아 있습니다.

```xml
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

이 클레임 변환이 포함된 유효성 검사 기술 프로필을 호출하는 자체 어설션된 기술 프로필은 오류 메시지를 정의할 수 있습니다.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>예 1

- 입력 클레임:
  - **phoneNumber**: +49(123) 456-7890
- 입력 매개 변수:
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: ISO3166
- 출력 클레임:
  - **nationalNumber**: 1234567890
  - **countryCode**: DE

### <a name="example-2"></a>예제 2

- 입력 클레임:
  - **phoneNumber**: +49(123) 456-7890
- 입력 매개 변수
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: CallingCode
- 출력 클레임:
  - **nationalNumber**: 1234567890
  - **countryCode**: +49
