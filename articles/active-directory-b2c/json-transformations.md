---
title: 사용자 지정 정책의 JSON 클레임 변환 예제
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 IEF(Identity Experience Framework) 스키마에 관한 JSON 클레임 변환의 예제입니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/27/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 939f173d6b34941302fab67a81bff5df01375adc
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007715"
---
# <a name="json-claims-transformations"></a>JSON 클레임 변환

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD B2C(Azure Active Directory B2C)에서 Identity Experience Framework 스키마의 JSON 클레임 변환을 사용하는 방법에 관한 예제를 제공합니다. 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

## <a name="generatejson"></a>GenerateJson

클레임 값 또는 상수를 사용하여 JSON 문자열을 생성합니다. 점 표기법 뒤에 오는 경로 문자열은 JSON 문자열에 데이터를 삽입할 위치를 나타내는 데 사용됩니다. 점으로 분할한 후 정수는 JSON 배열의 인덱스로 해석되고 정수가 아닌 값은 JSON 개체의 인덱스로 해석됩니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | 점 표기법 뒤에 오는 모든 문자열 | 문자열 | 클레임 값이 삽입될 JSON의 JsonPath |
| InputParameter | 점 표기법 뒤에 오는 모든 문자열 | 문자열 | 상수 문자열 값이 삽입될 JSON의 JsonPath |
| OutputClaim | outputClaim | 문자열 | 생성된 JSON 문자열 |

### <a name="example-1"></a>예제 1

다음 예제에서는 상수 문자열뿐만 아니라 “email” 및 “otp”의 클레임 값을 기반으로 JSON 문자열을 생성합니다.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

다음 클레임 변환은 SendGrid(타사 메일 공급자)에 전송된 요청의 본문이 될 JSON 문자열 클레임을 출력합니다. JSON 개체의 구조는 InputParameters의 점 표기법의 ID와 InputClaims의 TransformationClaimTypes로 정의됩니다. 점 표기법의 숫자는 배열을 의미합니다. 값은 InputClaims의 값과 InputParameters의 “Value” 속성에서 가져옵니다.

- 입력 클레임:
  - **email**, 변환 클레임 유형 **personalizations.0.to.0.email**: “someone@example.com”
  - **otp**, 변환 클레임 유형 **personalizations.0.dynamic_template_data.otp** “346349”
- 입력 매개 변수:
  - **template_id**: “d-4c56ffb40fa648b1aa6822283df94f60”
  - **from.email**: “service@contoso.com”
  - **personalizations.0.subject** “Contoso 계정 메일 확인 코드”
- 출력 클레임:
  - **requestBody**: JSON 값

```json
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

### <a name="example-2"></a>예 2

다음 예제에서는 상수 문자열뿐만 아니라 클레임 값을 기반으로 JSON 문자열을 생성합니다.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="customerEntity.email" />
    <InputClaim ClaimTypeReferenceId="objectId" TransformationClaimType="customerEntity.userObjectId" />
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="customerEntity.firstName" />
    <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="customerEntity.lastName" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="customerEntity.role.name" DataType="string" Value="Administrator"/>
    <InputParameter Id="customerEntity.role.id" DataType="long" Value="1"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

다음 클레임 변환은 REST API에 전송된 요청의 본문이 될 JSON 문자열 클레임을 출력합니다. JSON 개체의 구조는 InputParameters의 점 표기법의 ID와 InputClaims의 TransformationClaimTypes로 정의됩니다. 값은 InputClaims의 값과 InputParameters의 “Value” 속성에서 가져옵니다.

- 입력 클레임:
  - **email**, 변환 클레임 유형 **customerEntity.email**: “john.s@contoso.com”
  - **objectId**, 변환 클레임 유형 **customerEntity.userObjectId** “01234567-89ab-cdef-0123-456789abcdef”
  - **givenName**, 변환 클레임 유형 **customerEntity.firstName** “John”
  - **surname**, 변환 클레임 유형 **customerEntity.lastName** “Smith”
- 입력 매개 변수:
  - **customerEntity.role.name**: “Administrator”
  - **customerEntity.role.id** 1
- 출력 클레임:
  - **requestBody**: JSON 값

```json
{
   "customerEntity":{
      "email":"john.s@contoso.com",
      "userObjectId":"01234567-89ab-cdef-0123-456789abcdef",
      "firstName":"John",
      "lastName":"Smith",
      "role":{
         "name":"Administrator",
         "id": 1
      }
   }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

JSON 데이터에서 지정된 요소를 가져옵니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | 문자열 | 클레임 변환에서 항목을 가져오는 데 사용하는 ClaimType입니다. |
| InputParameter | claimToExtract | 문자열 | 추출할 JSON 요소의 이름입니다. |
| OutputClaim | extractedClaim | 문자열 | 이 클레임 변환을 호출하고 나면 생성되는 ClaimType입니다(_claimToExtract_ 입력 매개 변수에 지정된 요소 값). |

다음 예제에서는 클레임 변환이 JSON 데이터에서 `emailAddress` 요소를 추출했습니다. `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```xml
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- 입력 매개 변수:
    - **claimToExtract**: emailAddress
- 출력 클레임:
  - **extractedClaim**: someone@example.com

GetClaimFromJson 클레임 변환은 JSON 데이터에서 단일 요소를 가져옵니다. 앞의 예제에서는 emailAddress입니다. displayName을 가져오려면 다른 클레임 변환을 만듭니다. 예를 들면 다음과 같습니다.

```xml
<ClaimsTransformation Id="GetDispalyNameClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="displayName" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- 입력 클레임:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- 입력 매개 변수:
    - **claimToExtract**: displayName
- 출력 클레임:
  - **extractedClaim**: Someone

## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

JSON 데이터에서 지정된 요소의 목록을 가져옵니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | 문자열 | 클레임 변환에서 클레임을 가져오는 데 사용하는 ClaimType입니다. |
| InputParameter | errorOnMissingClaims | boolean | 클레임 중 하나가 없으면 오류를 throw할지 여부를 지정합니다. |
| InputParameter | includeEmptyClaims | 문자열 | 빈 클레임을 포함할지 여부를 지정합니다. |
| InputParameter | jsonSourceKeyName | 문자열 | 요소 키 이름입니다. |
| InputParameter | jsonSourceValueName | 문자열 | 요소 값 이름입니다. |
| OutputClaim | 컬렉션 | string, int, boolean, datetime |추출할 클레임 목록입니다. 클레임 이름은 _jsonSourceClaim_ 입력 클레임에 지정된 이름과 같아야 합니다. |

다음 예제에서 클레임 변환은 JSON 데이터에서 email(string), displayName(string), membershipNum(int), active(boolean) 및 birthdate(datetime) 클레임을 추출합니다.

```json
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```xml
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```

- 입력 클레임:
  - **jsonSourceClaim**: [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- 입력 매개 변수:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: key
    - **jsonSourceValueName**: value
- 출력 클레임:
  - **email**: "someone@example.com"
  - **displayName**: "Someone"
  - **membershipNum**: 6353399
  - **active**: true
  - **birthdate**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

JSON 데이터에서 지정된 숫자 (long) 요소를 가져옵니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | 문자열 | 클레임 변환에서 클레임을 가져오는 데 사용하는 ClaimType입니다. |
| InputParameter | claimToExtract | 문자열 | 추출할 JSON 요소의 이름입니다. |
| OutputClaim | extractedClaim | long | 이 ClaimsTransformation을 호출하고 나면 생성되는 ClaimType입니다(_claimToExtract_ 입력 매개 변수에 지정된 요소 값). |

다음 예제에서는 클레임 변환이 JSON 데이터에서 `id` 요소를 추출합니다.

```json
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```xml
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone", "id" : 6353399}
- 입력 매개 변수
    - **claimToExtract**: id
- 출력 클레임:
    - **extractedClaim**: 6353399

## <a name="getsingleitemfromjson"></a>GetSingleItemFromJson

JSON 데이터에서 첫 번째 요소를 가져옵니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | 문자열 | 클레임 변환에서 JSON 데이터의 항목을 가져오는 데 사용하는 ClaimType입니다. |
| OutputClaim | key | 문자열 | JSON의 첫 번째 요소 키입니다. |
| OutputClaim | 값 | 문자열 | JSON의 첫 번째 요소 값입니다. |

다음 예제에서는 클레임 변환이 JSON 데이터의 첫 번째 요소(이름)를 추출합니다.

```xml
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
  - **inputJson**: {"givenName": "Emilty", "lastName": "Smith"}
- 출력 클레임:
  - **key**: givenName
  - **value**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

JSON 데이터 배열에서 첫 번째 요소를 가져옵니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | 문자열 | 클레임 변환에서 JSON 배열의 항목을 가져오는 데 사용하는 ClaimType입니다. |
| OutputClaim | extractedClaim | 문자열 | 이 ClaimsTransformation을 호출하고 나면 생성되는 ClaimType입니다(JSON 배열의 첫 번째 요소). |

다음 예제에서는 클레임 변환이 JSON 배열의 첫 번째 요소(전자 메일 주소)를 추출합니다. `["someone@example.com", "Someone", 6353399]`

```xml
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
  - **inputJsonClaim**: ["someone@example.com", "Someone", 6353399]
- 출력 클레임:
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

XML 데이터를 JSON 형식으로 변환합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Xml | 문자열 | 클레임 변환에서 데이터를 XML에서 JSON 형식으로 변환하는 데 사용하는 ClaimType입니다. |
| OutputClaim | json : | 문자열 | 이 ClaimsTransformation을 호출하고 나면 생성되는 ClaimType입니다(JSON 형식의 데이터). |

```xml
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

다음 예제에서는 클레임 변환이 다음 XML 데이터를 JSON 형식으로 변환합니다.

#### <a name="example"></a>예
입력 클레임:

```xml
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

출력 클레임:

```json
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


