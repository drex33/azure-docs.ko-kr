---
title: ClaimsTransformations - Azure Active Directory B2C
description: Azure Active Directory B2C의 ID 경험 프레임워크 스키마에 포함된 ClaimsTransformations 요소의 정의에 대해 설명합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: d1b4aa49d6aecdf06ec445dd036be05805ffb128
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007993"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ClaimsTransformations** 요소에는 [사용자 지정 정책](custom-policy-overview.md)의 일부분으로 사용자 경험에서 사용할 수 있는 클레임 변환 함수 목록이 포함되어 있습니다. 클레임 변환에서는 지정된 클레임을 다른 클레임으로 변환합니다. 클레임 변환에서는 문자열 컬렉션에 항목을 추가하거나 문자열 대/소문자를 변경하는 등의 변환 메서드를 지정합니다.

사용자 경험에서 사용할 수 있는 클레임 변환 함수 목록을 포함하려면 정책의BuildingBlocks 섹션에서 ClaimsTransformations XML 요소를 선언해야 합니다.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

**ClaimsTransformation** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute |필수 | 설명 |
| --------- |-------- | ----------- |
| Id |예 | 클레임 변환을 고유하게 식별하는 데 사용되는 식별자입니다. 정책의 다른 XML 요소에서 식별자를 참조합니다. |
| TransformationMethod | 예 | 클레임 변환에서 사용할 변환 메서드입니다. 각 클레임 변환에는 고유한 값이 있습니다. 사용 가능한 값의 전체 목록은 [클레임 변환 참조](#claims-transformations-reference)를 참조하세요. |

## <a name="claimstransformation"></a>ClaimsTransformation

**ClaimsTransformation** 요소에는 다음과 같은 요소가 포함됩니다.

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| 요소 | 발생 수 | 설명 |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | 클레임 변환의 입력으로 가져올 클레임 유형을 지정하는 **InputClaim** 요소 목록입니다. 이러한 각 요소는 정책의 ClaimsSchema 섹션에 이미 정의되어 있는 ClaimType에 대한 참조를 포함합니다. |
| InputParameters | 0:1 | 클레임 변환에 대한 입력으로 제공되는 **InputParameter** 요소 목록입니다.
| OutputClaims | 0:1 | ClaimsTransformation을 호출하고 나면 생성되는 클레임 유형을 지정하는 **OutputClaim** 요소 목록입니다. 이러한 각 요소는 ClaimsSchema 섹션에 이미 정의되어 있는 ClaimType에 대한 참조를 포함합니다. |

### <a name="inputclaims"></a>InputClaims

**InputClaims** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | 필요한 입력 클레임 유형입니다. |

#### <a name="inputclaim"></a>InputClaim

**InputClaim** 요소에는 다음 특성이 포함됩니다.

| attribute |필수 | 설명 |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |예 | 정책의 ClaimsSchema 섹션에 이미 정의되어 있는 ClaimType에 대한 참조입니다. |
| TransformationClaimType |예 | 변환 클레임 유형을 참조하는 식별자입니다. 각 클레임 변환에는 고유한 값이 있습니다. 사용 가능한 값의 전체 목록은 [클레임 변환 참조](#claims-transformations-reference)를 참조하세요. |

### <a name="inputparameters"></a>InputParameters

**InputParameters** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| InputParameter | 1:n | 필요한 입력 매개 변수니다. |

#### <a name="inputparameter"></a>InputParameter

| attribute | 필수 |설명 |
| --------- | ----------- |----------- |
| Id | 예 | 클레임 변환 메서드의 매개 변수에 대한 참조인 식별자입니다. 각 클레임 변환 메서드에는 고유한 값이 있습니다. 사용 가능한 값의 전체 목록은 클레임 변환 표를 참조하세요. |
| DataType | 예 | 사용자 지정 정책 XML 스키마의 DataType 열거에 따른 String, Boolean, Int, DateTime 등의 매개 변수 데이터 형식입니다. 이 형식은 산술 연산을 정확하게 수행하는 데 사용됩니다. 각 클레임 변환에는 고유한 값이 있습니다. 사용 가능한 값의 전체 목록은 [클레임 변환 참조](#claims-transformations-reference)를 참조하세요. |
| 값 | 예 | 그대로 정확하게 변환에 전달되는 값입니다. 임의로 전달되는 값도 있고 클레임 변환 메서드에서 사용자가 직접 선택하는 값도 있습니다. |

### <a name="outputclaims"></a>OutputClaims

**OutputClaims** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | 필요한 출력 클레임 유형입니다. |

#### <a name="outputclaim"></a>OutputClaim

**OutputClaim** 요소에는 다음 특성이 포함됩니다.

| attribute |필수 | 설명 |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | 예 | 정책의 ClaimsSchema 섹션에 이미 정의되어 있는 ClaimType에 대한 참조입니다.
| TransformationClaimType | 예 | 변환 클레임 유형을 참조하는 식별자입니다. 각 클레임 변환에는 고유한 값이 있습니다. 사용 가능한 값의 전체 목록은 [클레임 변환 참조](#claims-transformations-reference)를 참조하세요. |

클레임 변환에 사용되는 입력 및 출력 클레임은 고유해야 합니다. 동일한 입력 클레임을 출력 클레임으로 사용할 수 없습니다.

## <a name="example"></a>예제

사용자가 동의한 서비스 약관의 마지막 버전을 저장하는 경우를 예로 들어 보겠습니다. 그러면 서비스 약관을 업데이트할 때 사용자에게 새 버전 동의 여부를 질문할 수 있습니다. 다음 예에서 **HasTOSVersionChanged** 클레임 변환은 **TOSVersion** 클레임의 값을 **LastTOSAcceptedVersion** 클레임의 값과 비교한 다음 부울 **TOSVersionChanged** 클레임을 반환합니다.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>클레임 변환 참조

클레임 변환의 예는 다음 참조 페이지를 참조하세요.

- [Boolean](boolean-transformations.md)
- [날짜](date-transformations.md)
- [Integer](integer-transformations.md)
- [JSON](json-transformations.md)
- [전화 번호](phone-number-claims-transformations.md)
- [일반](general-transformations.md)
- [소셜 계정](social-transformations.md)
- [String](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

