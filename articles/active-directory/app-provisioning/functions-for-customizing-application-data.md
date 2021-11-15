---
title: Azure Active Directory 애플리케이션 프로비저닝에서 특성 매핑에 대한 식을 작성하기 위한 참조
description: Azure Active Directory에서 SaaS 앱 개체의 자동화된 프로비전 중 허용되는 형식으로 특성 값을 변환하기 위해 식 매핑을 사용하는 방법에 대해 알아봅니다. 함수의 참조 목록을 포함합니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 2962c033ee42b91913324f22dbba3ca3cae49fdf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456369"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Azure Active Directory에서 특성 매핑에 대한 식을 작성하기 위한 참조

SaaS 애플리케이션에 프로비전을 구성하면 식 매핑은 지정할 수 있는 특성 매핑의 유형 중 하나입니다. 이러한 매핑의 경우 사용자의 데이터를 SaaS 애플리케이션에 대해 사용하는 형식으로 변환할 수 있는 스크립트 방식의 식을 작성해야 합니다.

## <a name="syntax-overview"></a>구문 개요

특성 매핑을 위한 식의 구문은 VBA(Visual Basic Applications) 함수를 연상시킵니다.

* 전체 식은 이름 뒤 괄호 안에 인수가 있는 함수(*FunctionName(`<<argument 1>>`,`<<argument N>>`)* )를 기준으로 정의해야 합니다.
* 서로 함수를 중첩할 수 있습니다. 예:  *FunctionOne(FunctionTwo(`<<argument1>>`))*
* 함수에 3가지 다른 유형의 인수를 전달할 수 있습니다.
  
  1. 특성은 대괄호로 묶어야 합니다. 예: [attributeName]
  2. 문자열 상수는 큰따옴표로 묶어야 합니다. 예: "미국"
  3. 기타 함수 예: FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* 문자열 상수의 경우, 백슬래시 (\) 또는 따옴표(")가 문자열에 필요한 경우 백슬래시(\) 기호로 이스케이프되어야 합니다. 예: "회사 이름: \\Contoso\\"
* 구문은 대/소문자를 구분하므로 함수에 구문을 문자열로 입력할 때 이를 고려해야 하며, 아니면 여기서 바로 복사하여 붙여넣습니다. 

## <a name="list-of-functions"></a>함수 목록

[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [AppRoleAssignmentsComplex](#approleassignmentscomplex) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [CDate](#cdate) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateAdd](#dateadd) &nbsp;&nbsp;&nbsp;&nbsp; [DateDiff](#datediff) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [IgnoreFlowIfNullOrEmpty](#ignoreflowifnullorempty) &nbsp;&nbsp;&nbsp;&nbsp;[IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp;[InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) &nbsp;&nbsp; &nbsp;&nbsp; [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Now](#now) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate) &nbsp;&nbsp;&nbsp;&nbsp; [PCase](#pcase) &nbsp;&nbsp;&nbsp;&nbsp; [RandomString](#randomstring) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Append

**함수:** Append(source, suffix)

**설명:** 원본 문자열 값을 문자열의 끝에 접미사로 추가합니다.

**매개 변수:**

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |대개는 원본 개체의 특성 이름입니다. |
| **접미사** |필수 |String |원본 값의 끝에 추가하려는 문자열입니다. |


#### <a name="append-constant-suffix-to-user-name"></a>사용자 이름에 상수 접미사 추가
예: Salesforce 샌드박스를 사용하는 경우 동기화하기 전에 모든 사용자 이름에 다른 접미사를 추가해야 할 수 있습니다.

**식:**  
`Append([userPrincipalName], ".test")`

**샘플 입/출력:** 

* **입력**: (userPrincipalName): “John.Doe@contoso.com”
* **출력**:  “John.Doe@contoso.com.test”

---
### <a name="approleassignmentscomplex"></a>AppRoleAssignmentsComplex

**함수:** AppRoleAssignmentsComplex([appRoleAssignments])

**설명:** 사용자에 대해 여러 역할을 프로비저닝하는 데 사용됩니다. 자세한 사용 방법은 [자습서 - Azure Active Directory에서 SaaS 애플리케이션에 대한 사용자 프로비저닝 특성 매핑 사용자 지정](customize-application-attributes.md#provisioning-a-role-to-a-scim-app)을 참조하세요.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |필수 |String |**[appRoleAssignments]** 개체. |

---
### <a name="bitand"></a>BitAnd
**함수:** BitAnd(value1, value2)

**설명:** 이 함수는 두 매개 변수를 전부 이진 표현으로 변환시키고 비트를 다음과 같이 설정합니다.

- 0 - value1 및 value2의 해당 비트 중 하나 또는 둘 모두가 0인 경우
- 1 - 2개 모두 해당 비트일 경우 1입니다.

즉, 두 매개 변수의 해당 비트가 1일 경우를 제외하는 모든 경우에는 0을 반환합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **value1** |필수 |Num |value2를 사용하여 AND로 입력해야 하는 숫자 값|
| **value2** |필수 |Num |value1을 사용하여 AND로 입력해야 하는 숫자 값|

**예:** 
`BitAnd(&HF, &HF7)`

11110111 AND 00000111 = 00000111이므로 `BitAnd`는 7, 즉 이진 값으로 00000111을 반환합니다.

---
### <a name="cbool"></a>CBool
**함수:**  
`CBool(Expression)`

**설명:**  
`CBool`은 계산된 식에 따라 부울을 반환합니다. 식이 0이 아닌 값으로 계산되는 경우 `CBool`은 *True* 를 반환하고, 그러지 않으면 *False* 를 반환합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **식** |필수 | 식 | 유효한 식 |

**예:** 
`CBool([attribute1] = [attribute2])`                                                                    
두개의 속성이 같은 동일한 값을 가지면 True로 반환합니다.

---
### <a name="cdate"></a>CDate
**함수:**  
`CDate(expression)`

**설명:**  
CDate 함수는 문자열에서 UTC 날짜/시간을 반환합니다. DateTime은 네이티브 특성 형식이 아니지만 [FormatDateTime](#formatdatetime) 및 [DateAdd](#dateadd)와 같은 날짜 함수 내에서 사용할 수 있습니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **식** |필수 | 식 | 날짜/시간을 나타내는 유효한 문자열입니다. 지원되는 형식은 [.NET 사용자 지정 날짜 및 시간 형식 문자열](/dotnet/standard/base-types/custom-date-and-time-format-strings)을 참조하세요. |

**설명:**  
반환된 문자열은 항상 UTC이며 **M/d/yyyy h:mm:ss tt** 형식을 따릅니다.

**예제 1:** <br> 
`CDate([StatusHireDate])`  
**샘플 입/출력:** 

* **입력**(StatusHireDate): "2020-03-16-07:00"
* **출력**: "3/16/2020 7:00:00 AM" <-- *위의 DateTime에 해당하는 UTC가 반환됩니다.*

**예 2:** <br> 
`CDate("2021-06-30+08:00")`  
**샘플 입/출력:** 

* **입력**: "2021-06-30+08:00"
* **출력**: "6/29/2021 4:00:00 PM" <-- *위의 DateTime에 해당하는 UTC가 반환됩니다.*

**예제 3:** <br> 
`CDate("2009-06-15T01:45:30-07:00")`  
**샘플 입/출력:** 

* **입력**: "2009-06-15T01:45:30-07:00"
* **출력**: "6/15/2009 8:45:30 AM" <-- *위의 DateTime에 해당하는 UTC가 반환됩니다.*

---
### <a name="coalesce"></a>Coalesce
**함수:** Coalesce(source1, source2, ..., defaultValue)

**설명:** NULL이 아닌 첫 번째 원본 값을 반환합니다. 모든 인수가 NULL이고 defaultValue가 있는 경우 defaultValue가 반환됩니다. 모든 인수가 NULL이고 defaultValue가 없으면 Coalesce는 NULL을 반환합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **source1  … sourceN** | 필수 | String |필수, 가변 횟수. 대개는 원본 개체의 특성 이름입니다. |
| **defaultValue** | 선택 | 문자열 | 모든 원본 값이 NULL일 때 사용할 기본값입니다. 빈 문자열("")일 수 있습니다.

#### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>mail 값이 NULL이 아닌 경우 userPrincipalName을 전달
예: mail 특성이 있는 경우 이를 전달하고 그렇지 않은 경우에는 userPrincipalName 값을 대신 전달하려고 합니다.

**식:**  
`Coalesce([mail],[userPrincipalName])`

**샘플 입/출력:** 

* **입력**(mail): NULL
* **입력**(userPrincipalName): “John.Doe@contoso.com”
* **출력**:  “John.Doe@contoso.com”


---
### <a name="converttobase64"></a>ConvertToBase64
**함수:** ConvertToBase64(source)

**설명:** ConvertToBase64 함수는 문자열을 유니코드 base64 문자열로 변환합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |base 64로 변환할 문자열|

**예:** 
`ConvertToBase64("Hello world!")`

"SABlAGwAbABvACAAdwBvAHIAbABkACEA"를 반환합니다.

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**함수:** ConvertToUTF8Hex(source)

**설명:** ConvertToUTF8Hex 함수는 문자열을 UTF8 16진수로 인코딩된 값으로 변환합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |UTF8 16진수로 변환할 문자열|

**예:** 
`ConvertToUTF8Hex("Hello world!")`

48656C6C6F20776F726C6421을 반환합니다.

---
### <a name="count"></a>개수
**함수:** Count(attribute)

**설명:** Count 함수는 다중값 특성의 요소 수를 반환합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **attribute** |필수 |특성 |요소 수를 계산할 다중값 특성|

---
### <a name="cstr"></a>CStr
**함수:** CStr(value)

**설명:** CStr 함수는 값을 문자열 데이터 형식으로 변환합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **value** |필수 | 숫자, 참조 또는 부울 | 숫자 값, 참조 특성 또는 부울입니다. |

**예:** 
`CStr([dn])`

“cn=Joe,dc=contoso,dc=com”을 반환

---
### <a name="dateadd"></a>DateAdd
**함수:**  
`DateAdd(interval, value, dateTime)`

**설명:**  
지정된 시간 간격이 추가된 날짜를 나타내는 날짜/시간 문자열을 반환합니다. 반환된 날짜는 **M/d/yyyy h:mm:ss tt** 형식입니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **interval** |필수 | 문자열 | 추가하려는 시간 간격입니다. 이 표 아래에서 허용되는 값을 참조하세요. |
| **value** |필수 | Number | 추가하려는 단위 수 입니다. 양수(미래 날짜) 또는 음수(과거 날짜)가 될 수 있습니다. |
| **dateTime** |필수 | DateTime | 간격이 추가되는 날짜를 나타내는 날짜/시간입니다. |

**interval** 문자열은 다음 값 중 하나가 있어야 합니다. 
 * yyyy 년 
 * m 월
 * d 일
 * ww 주
 * h 시간
 * n 분
 * s 초

**예 1: 고용 날짜에 7일 추가**  
`DateAdd("d", 7, CDate([StatusHireDate]))`
* **입력**(StatusHireDate): 2012-03-16-07:00
* **출력**: 3/23/2012 7:00:00 AM

**예 2: 고용 날짜 10일 전 날짜 가져오기**  
`DateAdd("d", -10, CDate([StatusHireDate]))`
* **입력**(StatusHireDate): 2012-03-16-07:00
* **출력**: 3/6/2012 7:00:00 AM

**예 3: 고용 날짜에 2주 추가**  
`DateAdd("ww", 2, CDate([StatusHireDate]))`
* **입력**(StatusHireDate): 2012-03-16-07:00
* **출력**: 3/30/2012 7:00:00 AM

**예 4: 고용 날짜에 10개월 추가**  
`DateAdd("m", 10, CDate([StatusHireDate]))`
* **입력**(StatusHireDate): 2012-03-16-07:00
* **출력**: 1/16/2013 7:00:00 AM

**예 5: 고용 날짜에 2년 추가**  
`DateAdd("yyyy", 2, CDate([StatusHireDate]))`
* **입력**(StatusHireDate): 2012-03-16-07:00
* **출력**: 3/16/2014 7:00:00 AM
---
### <a name="datediff"></a>DateDiff
**함수:**  
`DateDiff(interval, date1, date2)`

**설명:**  
이 함수는 *interval* 매개 변수를 사용하여 두 입력 날짜 간의 차이를 나타내는 숫자를 반환합니다. 을 반환합니다. 
  * date2 > date1이면 양수, 
  * date2 < date1이면 음수, 
  * date2 == date1이면 0

**매개 변수:** 

| Name | 필수/선택 | 형식 | 참고 |
| --- | --- | --- | --- |
| **interval** |필수 | 문자열 | 차이를 계산하는 데 사용할 시간 간격입니다. |
| **date1** |필수 | DateTime | 유효한 날짜를 나타내는 DateTime입니다. |
| **date2** |필수 | DateTime | 유효한 날짜를 나타내는 DateTime입니다. |

**interval** 문자열은 다음 값 중 하나가 있어야 합니다. 
 * yyyy 년 
 * m 월
 * d 일
 * ww 주
 * h 시간
 * n 분
 * s 초

**예 1: 다양한 간격을 사용하여 현재 날짜와 Workday의 고용 날짜를 비교** <br>
`DateDiff("d", Now(), CDate([StatusHireDate]))`

| 예제 | interval | date1 | date2 | output |
| --- | --- | --- | --- | --- |
| 두 날짜 간의 양수 차이(일) | 일 | 2021-08-18+08:00 | 2021-08-31+08:00 | 13 |
| 두 날짜 간의 음수 차이(일) | 일 | 8/25/2021 5:41:18 PM | 2012-03-16-07:00 | -3449 |
| 두 날짜 간의 차이(주) | ww | 8/25/2021 5:41:18 PM | 2012-03-16-07:00 | -493 | 
| 두 날짜 간의 차이(월) | 분 | 8/25/2021 5:41:18 PM | 2012-03-16-07:00 | -113 | 
| 두 날짜 간의 연 차이 | yyyy | 8/25/2021 5:41:18 PM | 2012-03-16-07:00 | -9 | 
| 두 날짜가 동일한 경우의 차이 | 일 | 2021-08-31+08:00 | 2021-08-31+08:00 | 0 | 
| 두 날짜 간의 차이(시간) | h | 2021-08-24 | 2021-08-25 | 24 | 
| 두 날짜 간의 차이(분) | n | 2021-08-24 | 2021-08-25 | 1440 | 
| 두 날짜 간의 차이(초) | 초 | 2021-08-24 | 2021-08-25 | 86400 | 

**예제 2: DateDiff 함수를 IIF 함수와 결합하여 특성 값 설정** <br>
Workday에서 계정이 활성 상태인 경우 고용 날짜가 앞으로 5일 이내인 경우에만 사용자의 *accountEnabled* 특성을 True로 설정합니다. 

```
Switch([Active], , 
  "1", IIF(DateDiff("d", Now(), CDate([StatusHireDate])) > 5, "False", "True"), 
  "0", "False")
```

---

### <a name="datefromnum"></a>DateFromNum
**함수:** DateFromNum(value)

**설명:** DateFromNum 함수는 AD의 날짜 값 형식을 DateTime 형식으로 변환합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **value** |필수 | 날짜 | DateTime 형식으로 변환할 AD 날짜 |

**예:** 
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

2012년 1월 1일 오후 11시를 나타내는 DateTime을 반환합니다.

---
### <a name="formatdatetime"></a>FormatDateTime
**함수:** FormatDateTime(source, dateTimeStyles, inputFormat, outputFormat)

**설명:** 한 형식의 날짜 문자열을 다른 형식으로 변환합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |대개는 원본 개체의 특성 이름입니다. |
| **dateTimeStyles** | 선택 | 문자열 | 이 함수를 사용하여 일부 날짜 및 시간 구문 분석 메서드에 대해 문자열 구문 분석을 사용자 지정하는 서식 옵션을 지정할 수 있습니다. 지원되는 값은 [DateTimeStyles 문서](/dotnet/api/system.globalization.datetimestyles)를 참조하세요. 빈 상태로 두면 사용되는 기본값은 DateTimeStyles.RoundtripKind, DateTimeStyles.AllowLeadingWhite, DateTimeStyles.AllowTrailingWhite입니다.  |
| **inputFormat** |필수 |String |원본 값의 예상된 형식입니다. 지원되는 형식은 [.NET 사용자 지정 날짜 및 시간 형식 문자열](/dotnet/standard/base-types/custom-date-and-time-format-strings)을 참조하세요. |
| **outputFormat** |필수 |String |출력 날짜의 형식입니다. |



#### <a name="output-date-as-a-string-in-a-certain-format"></a>특정 형식에서 문자열로 출력 날짜
예: ServiceNow와 같은 SaaS 애플리케이션에 특정 형식으로 된 날짜를 보내려고 합니다. 이때 다음 식의 사용을 고려할 수 있습니다. 

**식:** 

`FormatDateTime([extensionAttribute1], , "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**샘플 입/출력:**

* **입력** (extensionAttribute1): "20150123105347.1Z"
* **출력**: “2015-01-23”


---
### <a name="guid"></a>Guid
**함수:** Guid()

**설명:** Guid 함수는 임의의 새 GUID를 생성합니다.

**예:** <br>
`Guid()`<br>
샘플 출력: "1088051a-cd4b-4288-84f8-e02042ca72bc"

---
### <a name="ignoreflowifnullorempty"></a>IgnoreFlowIfNullOrEmpty
**함수:** IgnoreFlowIfNullOrEmpty(expression)

**설명:** IgnoreFlowIfNullOrEmpty 함수는 묶은 함수 또는 특성이 NULL이거나 비어 있는 경우 특성을 무시하고 흐름에서 삭제하도록 프로비저닝 서비스에 지시합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **식** | 필수 | 식 | 계산할 식 |

**예 1: 특성이 Null인 경우 전달하지 않음** <br>
`IgnoreFlowIfNullOrEmpty([department])` <br>
위 식은 부서 특성이 Null이거나 비어 있는 경우 프로비저닝 흐름에서 삭제합니다. <br>

**예 2: 식 매핑이 빈 문자열 또는 Null로 평가되는 경우 특성을 전달하지 않음** <br>
SuccessFactors 특성 *prefix* 가 다음 식 매핑을 사용하여 온-프레미스 Active Directory 특성 *personalTitle* 에 매핑된다고 가정해 보겠습니다. <br>
`IgnoreFlowIfNullOrEmpty(Switch([prefix], "", "3443", "Dr.", "3444", "Prof.", "3445", "Prof. Dr."))` <br>
위 식은 먼저 [Switch](#switch) 함수를 평가합니다. *prefix* 특성에 *Switch* 함수 내에 나열된 값이 없으면 *Switch* 는 빈 문자열을 반환하고 *personalTitle* 은 온-프레미스 Active Directory에 대한 프로비저닝 흐름에 포함되지 않습니다.

---
### <a name="iif"></a>IIF
**함수:** IIF(condition,valueIfTrue,valueIfFalse)

**함수:** IIF 함수는 지정된 조건에 따라 가능한 값 집합 중 하나를 반환합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **condition** |필수 |변수 또는 식 |true 또는 false로 평가될 수 있는 값 또는 식입니다. |
| **valueIfTrue** |필수 |변수 또는 문자열 | 조건이 true로 평가되는 경우 반환된 값입니다. |
| **valueIfFalse** |필수 |변수 또는 문자열 |조건이 false로 평가되는 경우 반환된 값입니다.|

**예:** 
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**함수:** InStr(value1, value2, start, compareType)

**설명:** InStr 함수는 문자열에서 첫 번째 하위 문자열을 찾습니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **value1** |필수 |String |검색할 문자열 |
| **value2** |필수 |String |검색될 문자열 |
| **start** |선택 |정수 |하위 문자열을 찾을 시작 위치|
| **compareType** |선택 사항 |열거형 |vbTextCompare 또는 vbBinaryCompare |

**예:** 
`InStr("The quick brown fox","quick")`

5로 계산됩니다.

`InStr("repEated","e",3,vbBinaryCompare)`

7로 계산합니다.

---
### <a name="isnull"></a>IsNull
**함수:** IsNull(Expression)

**설명:** 식이 Null로 계산되면 IsNull 함수는 true를 반환합니다. 특성이 없는 경우 Null로 표현됩니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **식** |필수 |식 |계산할 식 |

**예:** 
`IsNull([displayName])`

특성이 없으면 True를 반환합니다.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**함수:** IsNullOrEmpty(Expression)

**설명:** 식이 null 또는 빈 문자열일 경우 IsNullOrEmpty 함수는 true를 반환합니다. 특성이 없거나, 빈 문자열로 존재하는 경우 True로 계산합니다.
이 함수의 역원을 IsPresnt라고 합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **식** |필수 |식 |계산할 식 |

**예:** 
`IsNullOrEmpty([displayName])`

특성이 없거나 빈 문자열인 경우 True를 반환합니다.

---
### <a name="ispresent"></a>IsPresent
**함수:** IsPresent(Expression)

**설명:** 식이 Null이 아니고 비어 있지 않은 문자열로 계산되는 경우 IsPresent 함수는 true를 반환합니다. 이 함수의 역함수는 IsNullOrEmpty으로 지칭됩니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **식** |필수 |식 |계산할 식 |

**예:** 
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**함수:** IsString(Expression)

**설명:** 식이 문자열 형식으로 계산될 수 있는 경우 IsString 함수는 True로 계산됩니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **식** |필수 |식 |계산할 식 |

---
### <a name="item"></a>항목
**함수:** Item(attribute, index)

**함수:** Item 함수는 다중값 문자열/특성에서 하나의 항목을 반환합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **attribute** |필수 |attribute |검색할 다중 값 특성 |
| **index** |필수 |정수 | 다중값 문자열에서 항목에 대한 인덱스|

**예:** 
`Item([proxyAddresses], 1)`은 다중값 특성의 첫 번째 항목을 반환합니다. 인덱스 0은 사용할 수 없습니다. 

---
### <a name="join"></a>Join
**함수:** Join(separator, source1, source2, …)

**설명:** 다중 **source** 문자열 값을 단일 문자열로 결합할 수 있다는 점을 제외하고 Join()은 Append()와 유사하며, 각 값은 **separator** 문자열로 구분됩니다.

원본 값 중 하나가 다중 값 특성인 경우, 해당 특성의 모든 값은 함께 조인되며 구분 기호 값에 의해 구분됩니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **separator** |필수 |String |문자열이 하나의 문자열로 연결되면 원본 값을 구분하는데 문자열을 사용합니다. 구분 기호가 필요하지 않은 경우 ""일 수 있습니다. |
| **source1  … sourceN** |필수, 시간 변수 |String |값이 함께 조인될 문자열입니다. |

---
### <a name="left"></a>왼쪽
**함수:** Left(String, NumChars)

**설명:** Left 함수는 문자열 왼쪽부터 지정된 수의 문자를 반환합니다. numChars = 0 인 경우, 빈 문자열을 반환합니다.
numCahrs < 0,인 경우, 입력된 문자열을 반환합니다.
문자열이 null이면 빈 문자열을 반환합니다.
문자열이 numChars 내에서 숫자가 지정한 문자보다 적은 문자를 포함하는 경우, 문자열과 동일한 문자열(즉, 매개 변수 1의 모든 문자가 포함)이 반환됩니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **String** |필수 |attribute | 문자를 반환할 문자열 |
| **NumChars** |필수 |정수 | 문자열의 시작(왼쪽)부터 반환할 문자 수를 나타내는 숫자|

**예:** 
`Left("John Doe", 3)`

"Joh"를 반환합니다.

---
### <a name="mid"></a>Mid
**함수:** Mid(source, start, length)

**설명:** 원본 값의 부분 문자열을 반환합니다. 부분 문자열은 원본 문자열에서 문자 중 일부만 포함하는 문자열입니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |일반적으로 특성 이름입니다. |
| **start** |필수 |정수 |하위 문자열이 시작되어야 하는 **source** 문자열의 인덱스입니다. 문자열의 첫번째 문자에는 인덱스 1이 있고, 두번째 문자에는 인덱스 2가 있습니다. |
| **length** |필수 |정수 |부분 문자열의 길이입니다. 길이가 **원본** 문자열 외부에서 종료되면 함수는 **시작** 인덱스부터 **원본** 문자열 끝까지의 substring을 반환합니다. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**함수:** NormalizeDiacritics(source)

**설명:** 문자열 인수가 하나 필요합니다. 문자열을 반환하지만 해당하는 비분음 부호 문자로 대체된 분음 부호 문자를 포함합니다. 일반적으로 분음 부호 문자(악센트 부호)를 포함하는 이름 및 성을 다양한 사용자 ID(예: 사용자 계정 이름, SAM 계정 이름 및 이메일 주소)에 사용할 수 있는 올바른 값으로 변환하는 데 사용됩니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String | 일반적으로 이름 또는 성 특성입니다. |


| 분음 부호가 있는 문자  | 정규화된 문자 | 분음 부호가 있는 문자  | 정규화된 문자 | 
| --- | --- | --- | --- | 
| ä, à, â, ã, å, á, ą, ă, ā, ā́, ā̀, ā̂, ā̃, ǟ, ā̈, ǡ, a̱, å̄ | a | Ä, À, Â, Ã, Å, Á, Ą, Ă, Ā, Ā́, Ā̀, Ā̂, Ā̃, Ǟ, Ā̈, Ǡ, A̱, Å̄ | A | 
| æ, ǣ | ae | Æ, Ǣ | AE | 
| ç, č, ć, c̄, c̱ | c | Ç, Č, Ć, C̄, C̱ | C | 
| ď, d̄, ḏ | 일 | Ď, D̄, Ḏ | D | 
| ë, è, é, ê, ę, ě, ė, ē, ḗ, ḕ, ē̂, ē̃, ê̄, e̱, ë̄, e̊̄ | e | Ë, È, É, Ê, Ę, Ě, Ė, Ē, Ḗ, Ḕ, Ē̂, Ē̃, Ê̄, E̱, Ë̄, E̊̄ | E | 
| ğ, ḡ, g̱ | g | Ğ, Ḡ, G̱ | G | 
| ï, î, ì, í, ı, ī, ī́, ī̀, ī̂, ī̃, i̱ | i | Ï, Î, Ì, Í, İ, Ī, Ī́, Ī̀, Ī̂, Ī̃, I̱ | I |  
| ľ, ł, l̄, ḹ, ḻ | l |  Ł, Ľ, L̄, Ḹ, Ḻ | L | 
| ñ, ń, ň, n̄, ṉ | n |  Ñ, Ń, Ň, N̄, Ṉ | N | 
| ö, ò, ő, õ, ô, ó, ō, ṓ, ṑ, ō̂, ō̃, ȫ, ō̈, ǭ, ȭ, ȱ, o̱ | o |  Ö, Ò, Ő, Õ, Ô, Ó, Ō, Ṓ, Ṑ, Ō̂, Ō̃, Ȫ, Ō̈, Ǭ, Ȭ, Ȱ, O̱ | O | 
| ø, ø̄, œ̄  | oe |  Ø, Ø̄, Œ̄ | OE | 
| ř, r̄, ṟ, ṝ | r |  Ř, R̄, Ṟ, Ṝ | R | 
| ß | ss | | | 
| š, ś, ș, ş, s̄, s̱ | 초 |  Š, Ś, Ș, Ş, S̄, S̱ | S | 
| ť, ț, t̄, ṯ | t | Ť, Ț, T̄, Ṯ | T | 
| ü, ù, û, ú, ů, ű, ū, ū́, ū̀, ū̂, ū̃, u̇̄, ǖ, ṻ, ṳ̄, u̱ | u |  Ü, Ù, Û, Ú, Ů, Ű, Ū, Ū́, Ū̀, Ū̂, Ū̃, U̇̄, Ǖ, Ṻ, Ṳ̄, U̱ | U | 
| ÿ, ý, ȳ, ȳ́, ȳ̀, ȳ̃, y̱ | y | Ÿ, Ý, Ȳ, Ȳ́, Ȳ̀, Ȳ̃, Y̱ | Y | 
| ź, ž, ż, z̄, ẕ | z | Ź, Ž, Ż, Z̄, Ẕ | Z | 


#### <a name="remove-diacritics-from-a-string"></a>문자열에서 분음 부호 제거
예: 악센트 기호를 포함하지 않는 해당 문자로 악센트 기호를 포함하는 문자로 대체합니다.

**식:** NormalizeDiacritics([givenName])

**샘플 입/출력:** 

* **입력** (givenName): "Zoë"
* **출력**:  "Zoe"


---
### <a name="not"></a>Not
**함수:** Not(source)

**설명:** **원본** 의 부울 값을 대칭 이동합니다. **원본** 값이 True인 경우 False를 반환합니다. 그렇지 않은 경우 True를 반환합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |부울 문자열 |필요한 **source** 값은 "True"나 "False"입니다. |

---
### <a name="now"></a>지금
**함수:** Now()

**설명:**  
Now 함수는 현재 UTC DateTime을 나타내는 문자열을 **M/d/yyyy h:mm:ss tt** 형식으로 반환합니다.

**예:** 
`Now()` <br>
반환 값 예 *7/2/2021 3:33:38 PM*

---
### <a name="numfromdate"></a>NumFromDate
**함수:** NumFromDate(value)

**설명:** NumFromDate 함수는 DateTime 값을 [accountExpires](/windows/win32/adschema/a-accountexpires)와 같은 특성을 설정하는 데 필요한 Active Directory 형식으로 변환합니다. 이 함수를 사용하면 Workday 및 SuccessFactors와 같은 클라우드 HR 앱에서 받은 DateTime 값을 그에 상응하는 AD 표현으로 변환할 수 있습니다. 

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **value** |필수 | String | 지원되는 형식의 날짜 및 시간 문자열입니다. 지원되는 형식은 https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx를 참조하세요. |

**예:**
* Workday를 예로 들어 Workday에서 *2020-12-31-08:00* 형식으로 된 *ContractEndDate* 특성을 AD의 *accountExpires* 필드로 매핑하는 경우 이 함수를 사용하여 로캘에 맞게 표준 시간대 오프셋을 변경하는 방법은 다음과 같습니다. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], ,"yyyy-MM-ddzzz", "yyyy-MM-dd"), " 23:59:59-08:00"))`

* SuccessFactors를 예로 들어 SuccessFactors에서 *M/d/yyyy hh:mm:ss tt* 형식으로 된 *endDate* 특성을 AD의 *accountExpires* 필드로 매핑하는 경우 이 함수를 사용하여 로캘에 맞게 표준 시간대 오프셋을 변경하는 방법은 다음과 같습니다.
  `NumFromDate(Join("",FormatDateTime([endDate], ,"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd")," 23:59:59-08:00"))`


---
### <a name="pcase"></a>PCase
**함수:** PCase(source, wordSeparators)

**설명:** PCase 함수는 문자열 내의 각 단어의 첫 문자를 대문자로 변환하고 다른 모든 문자를 소문자로 변환합니다.

**매개 변수:** 

| Name | 필수/선택 | 형식 | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |적절한 대/소문자로 변환할 **원본** 값입니다. |
| **wordSeparators** |선택 |문자열 |단어 구분 기호로 사용되는 문자 집합을 지정합니다(예: ",-'"). |

**설명:**

* *wordSeparators* 매개 변수가 지정되지 않은 경우 PCase는 내부적으로 .NET 함수 [ToTitleCase](/dotnet/api/system.globalization.textinfo.totitlecase)를 호출하여 *원본* 문자열을 적절한 대/소문자로 변환합니다. .NET 함수 *ToTitleCase* 는 단어 구분 기호로 포괄적인 [유니코드 문자 범주](https://www.unicode.org/reports/tr44/#General_Category_Values)의 집합을 지원합니다. 
  * 공백 문자
  * 줄 바꿈 문자
  * CRLF와 같은 *제어* 문자
  * *형식* 제어 문자
  * 밑줄과 같은 *ConnectorPunctuation* 문자
  * 대시, 하이픈과 같은 *DashPunctuation* 문자(En 대시, Em 대시, 이중 하이픈과 같은 문자 포함)
  * 괄호, 중괄호, 꺾쇠 괄호 등과 같은 쌍으로 발생하는 *OpenPunctuation* 및 *ClosePunctuation* 문자 
  * 작은따옴표, 큰따옴표 및 각따옴표와 같은 *InitialQuotePunctuation* 및 *FinalQuotePunctuation* 문자 
  * 느낌표, 숫자 기호, 백분율 기호, 앰퍼샌드, 별표, 쉼표, 마침표, 콜론, 세미 콜론과 같은 *OtherPunctuation* 문자 
  * 더하기 기호, 보다 작음, 보다 큼 부호, 세로줄, 물결표, 등호와 같은 *MathSymbol* 문자
  * 달러 기호, 센트 기호, 파운드 기호, 유로 기호 등과 같은 *CurrencySymbol* 문자 
  * 매크로, 악센트, 화살표 헤드 등과 같은 *ModifierSymbol* 문자 
  * 저작권 기호, 학위 기호, 등록된 서명 등과 같은 *OtherSymbol* 문자 
* *wordSeparators* 매개 변수가 지정된 경우 PCase는 단어 구분 기호로 지정된 문자만 사용합니다. 

**예:**

SAP SuccessFactors에서 *firstName* 및 *lastName* 특성을 소싱하고 있으며 HR에서는 이 두 특성이 모두 대문자라고 가정해 봅니다. PCase 함수를 사용하여 아래와 같이 이름을 적절한 대/소문자로 변환할 수 있습니다. 

| 식 | 입력 | 출력 | 참고 |
| --- | --- | --- | --- |
| `PCase([firstName])` | *firstName* = "PABLO GONSALVES(SECOND)" | "Pablo Gonsalves(Second)" | *wordSeparators* 매개 변수가 지정되지 않은 경우 *PCase* 함수는 기본 단어 구문 문자 집합을 사용합니다. |
| `PCase([lastName]," '-")` | *lastName* = "PINTO-DE'SILVA" | "Pinto-De'Silva" | *PCase* 함수는 *wordSeparators* 매개 변수의 문자를 사용하여 단어를 식별하고 이를 적절한 대/소문자로 변환합니다. |
| `PCase(Join(" ",[firstName],[lastName]))` | *firstName* = GREGORY, *lastName* = "JAMES" | "Gregory James" | PCase 내에서 조인 함수를 중첩할 수 있습니다. *wordSeparators* 매개 변수가 지정되지 않은 경우 *PCase* 함수는 기본 단어 구문 문자 집합을 사용합니다.  |


---

### <a name="randomstring"></a>RandomString
**함수:** RandomString(Length, MinimumNumbers, MinimumSpecialCharacters, MinimumCapital, MinimumLowerCase, CharactersToAvoid)

**설명:** RandomString 함수는 지정된 조건에 따라 임의 문자열을 생성합니다. 허용되는 문자를 [여기](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference)에서 식별할 수 있습니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **길이** |필수 |Number |임의 문자열의 총 길이입니다. 이 값은 MinimumNumbers, MinimumSpecialCharacters 및 MinimumCapital의 합계보다 크거나 같아야 합니다. 최대 256자입니다.|
| **MinimumNumbers** |필수 |Number |임의 문자열의 최소 숫자입니다.|
| **MinimumSpecialCharacters** |필수 |Number |최소 특수 문자 수입니다.|
| **MinimumCapital** |필수 |Number |임의 문자열의 최소 대문자 수입니다.|
| **MinimumLowerCase** |필수 |Number |임의 문자열의 최소 소문자 수입니다.|
| **CharactersToAvoid** |선택 |문자열 |임의 문자열을 생성할 때 제외할 문자입니다.|


**예제 1:** - 특수 문자 제한 없이 임의 문자열을 생성합니다. `RandomString(6,3,0,0,3)`
6자로 임의 문자열을 생성합니다. 문자열은 3개의 숫자와 3개의 소문자(1a73qt)를 포함합니다.

**예제 2:** - 특수 문자 제한이 있는 임의 문자열을 생성합니다. `RandomString(10,2,2,2,1,"?,")`
10자로 임의 문자열을 생성합니다. 문자열은 2개 이상의 숫자, 2개의 특수 문자, 2개의 대문자, 1개의 소문자를 포함하고 "?" 및 ","(1 @! 2BaRg53) 문자를 제외합니다.

---

### <a name="removeduplicates"></a>RemoveDuplicates
**함수:** RemoveDuplicates(attribute)

**설명:** RemoveDuplicates 함수는 다중값 문자열을 사용하여 개별 값을 고유하게 만듭니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **attribute** |필수 |다중값 특성 |중복 항목을 제거할 다중값 특성|

**예:** 
`RemoveDuplicates([proxyAddresses])`모든 중복 값을 제거한 삭제된 proxyAddress 특성을 반환합니다.

---
### <a name="replace"></a>바꾸기
**함수:** Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**설명:** 대/소문자를 구분하는 방식으로 문자열 내의 값을 바꿉니다. 함수는 제공된 매개 변수에 따라 다르게 작동합니다.

* **oldValue** 및 **replacementValue** 가 제공된 경우:
  
  * **source** 에서 발생하는 모든 **oldValue** 를 **replacementValue** 로 바꿉니다.
* **oldValue** 및 **template** 가 제공된 경우:
  
  * **template** 의 모든 **oldValue** 를 **source** 값으로 바꿉니다.
* **regexPattern** 및 **replacementValue** 가 제공되는 경우:

  * 함수에서 **regexPattern** 을 **source** 문자열에 적용하고, regex 그룹 이름을 사용하여 **replacementValue** 에 대한 문자열을 생성할 수 있습니다.
* **regexPattern**, **regexGroupName**, **replacementValue** 가 제공되는 경우:
  
  * 함수에서 **regexPattern** 을 **source** 문자열에 적용하고, **regexGroupName** 과 일치하는 모든 값을 **replacementValue** 로 바꿉니다.
* **regexPattern**, **regexGroupName**, **replacementAttributeName** 이 제공되는 경우:
  
  * **source** 에 값이 없는 경우 **source** 가 반환됩니다.
  * **source** 에 값이 있는 경우 함수에서 **regexPattern** 을 **source** 문자열에 적용하고, **regexGroupName** 과 일치하는 모든 값을 **replacementAttributeName** 과 연결된 값으로 바꿉니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |일반적으로 **source** 개체의 특성 이름입니다. |
| **oldValue** |선택 |문자열 |**원본** 또는 **템플릿** 에서 대체될 값입니다. |
| **regexPattern** |선택 |문자열 |**source** 에서 바뀔, 값의 Regex 패턴입니다. 또는 **replacementPropertyName** 을 사용하는 경우 **replacementPropertyName** 에서 값을 추출하는 패턴입니다. |
| **regexGroupName** |선택 |문자열 |**regexPattern** 내 그룹의 이름입니다. **replacementPropertyName** 을 사용하는 경우에만 **replacementPropertyName** 에서 이 그룹의 값을 **replacementValue** 로 추출합니다. |
| **replacementValue** |선택 |문자열 |이전 값과 대체할 새로운 값입니다. |
| **replacementAttributeName** |선택 |문자열 |대체 값에 사용할 특성의 이름입니다. |
| **template** |선택 |문자열 |**template** 값이 제공되는 경우 템플릿 내에서 **oldValue** 를 찾아서 **source** 값으로 바꿉니다. |

#### <a name="replace-characters-using-a-regular-expression"></a>정규식을 사용하여 문자를 바꿈
예: 정규식 값과 일치하는 문자를 찾아 제거해야 합니다.

**식:** 

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**샘플 입/출력:**

* **INPUT** (mailNickname: "john_doe72"
* **출력**: "72"


---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**함수:** SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**설명:** 최소 두 개의 인수가 필요하며, 이는 식을 사용하여 정의된 고유한 값 생성 규칙입니다. 함수는 각 규칙을 평가한 후 대상 앱/디렉터리에서 생성된 값이 고유한지 확인합니다. 발견된 첫 번째 고유한 값이 반환됩니다. 모든 값이 이미 대상에 있는 경우 항목은 위탁되고 감사 로그에 이유가 기록됩니다. 제공할 수 있는 인수 수에 상한은 없습니다.


 - 이 함수는 최상위 수준에 있어야 하며 중첩할 수 없습니다.
 - 우선 순위가 일치하는 특성에는 이 함수를 적용할 수 없습니다.     
 - 이 함수는 항목 만들기에만 사용할 수 있습니다. 특성과 함께 사용할 경우 **매핑 적용** 속성을 **개체를 만드는 동안만** 으로 설정합니다.
 - 이 함수는 현재 “Workday에서 Active Directory로의 사용자 프로비저닝” 및 “SuccessFactors에서 Active Directory로의 사용자 프로비저닝”에서만 지원됩니다. 다른 프로비저닝 애플리케이션에서는 사용할 수 없습니다. 
 - *SelectUniqueValue* 기능이 온-프레미스 Active Directory에서 수행하는 LDAP 검색은 분음 부호와 같은 특수 문자를 이스케이프하지 않습니다. 특수 문자가 포함된 "Jéssica Smith"와 같은 문자열을 전달하면 처리 오류가 발생합니다. 아래 예제에 표시된 것과 같이 [NormalizeDiacritics](#normalizediacritics) 문자를 중첩하여 특수 문자를 정규화하세요. 


**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |2개 이상 필요, 상한 없음 |String | 평가할 고유한 값 생성 규칙 목록입니다. |

#### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>UPN(userPrincipalName) 특성의 고유한 값 생성
예: 사용자의 이름, 중간 이름, 성에 따라, UPN 특성에 값을 할당하려면 먼저 해당 UPN 특성에 대해 값을 생성하고 대상 AD 디렉터리에서 해당 값이 고유한지 확인해야 합니다.

**식:** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**샘플 입/출력:**

* (PreferredFirstName) **입력**: “John”
* (PreferredLastName) **입력**: “Smith”
* **출력**: John.Smith@contoso.com의 UPN 값이 디렉터리에 아직 없는 경우 “John.Smith@contoso.com”
* **출력**: John.Smith@contoso.com의 UPN 값이 디렉터리에 이미 있는 경우 “J.Smith@contoso.com”
* **출력**: 위의 두 UPN 값이 디렉터리에 이미 있는 경우 “Jo.Smith@contoso.com”



---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**함수:** SingleAppRoleAssignment([appRoleAssignments])

**설명:** 지정된 애플리케이션에 대해 사용자에게 할당된 모든 appRoleAssignment 목록에서 단일 appRoleAssignment를 반환합니다. 이 함수는 appRoleAssignments 개체를 단일 역할 이름 문자열로 변환하는 데 필요합니다. 모범 사례는 한 번에 하나의 appRoleAssignment만 하나의 사용자에게 할당되도록 하는 것이고 여러 역할이 할당된 경우에는 반환된 역할 문자열을 예측할 수 없습니다. 

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |필수 |String |**[appRoleAssignments]** 개체. |

---
### <a name="split"></a>분할
**함수:** Split(source, delimiter)

**설명:** 지정된 구분 기호 문자를 사용하여 문자열을 다중 값 배열로 분할합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |업데이트할 **source** 값입니다. |
| **delimiter** |필수 |String |문자열을 분할하는 데 사용할 문자(예: ",")를 지정 |

#### <a name="split-a-string-into-a-multi-valued-array"></a>문자열을 다중 값 배열로 분할
예: 문자열의 쉼표로 구분된 목록을 가져와 Salesforce의 PermissionSets 특성과 같은 다중 값 특성에 연결할 수 있는 배열로 분할해야 합니다. 이 예의 경우 Azure AD에서 사용 권한 세트의 목록을 extensionAttribute5에 채웠습니다.

**식:** Split([extensionAttribute5], ",")

**샘플 입/출력:** 

* **입력** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **OUTPUT**:  ["PermissionSetOne", "PermissionSetTwo"]


---
### <a name="stripspaces"></a>StripSpaces
**함수:** StripSpaces(source)

**설명:** 원본 문자열에서 모든 공백(" ")을 제거합니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |업데이트할 **source** 값입니다. |

---
### <a name="switch"></a>스위치
**함수:** Switch(source, defaultValue, key1, value1, key2, value2, …)

**설명:** : **원본** 값이 **key** 와 일치하면, 해당 **key** 의 **value** 를 반환합니다. **source** 값이 아무 키와도 일치하지 않는 경우 **defaultValue** 를 반환합니다.  **Key** 및 **value** 매개 변수는 항상 쌍으로 제공되어야 합니다. 함수는 항상 짝수 개수의 매개 변수를 예상합니다. 함수는 manager와 같은 참조 특성에 사용하면 안 됩니다. 

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |**Source** 값입니다. |
| **defaultValue** |선택 |문자열 |원본이 모든 키와 일치하지 않는 경우 사용할 기본값입니다. 빈 문자열("")일 수 있습니다. |
| **key** |필수 |String |**원본** 값과 비교할 **Key** 입니다. |
| **value** |필수 |String |키와 일치하는 **원본** 의 대체 값입니다. |

#### <a name="replace-a-value-based-on-predefined-set-of-options"></a>미리 정의된 옵션 집합을 기반으로 값 바꾸기
예제: Azure AD에 저장된 상태 코드를 기반으로 사용자의 시간대를 정의합니다.  상태 코드가 미리 정의된 옵션 중 하나와 일치하지 않으면 기본값인 "오스트레일리아/시드니"를 사용합니다.

**식:**  
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**샘플 입/출력:**

* **입력** (상태): "QLD"
* **출력**: "오스트레일리아/브리즈번"


---
### <a name="tolower"></a>ToLower
**함수:** ToLower(source, culture)

**설명:** ‘원본’ 문자열 값을 가져와서 지정된 문화권 규칙을 사용하여 소문자로 변환합니다. 지정된 *문화권* 정보가 없는 경우 고정 문화권을 사용합니다.

대상 시스템의 기존 값을 소문자로 설정하려면 [대상 애플리케이션의 스키마를 업데이트](./customize-application-attributes.md#editing-the-list-of-supported-attributes)하고 관심 있는 특성에 대해 caseExact 속성을 'true'로 설정합니다. 

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |대개는 원본 개체의 특성 이름 |
| **culture** |선택 |문자열 |RFC 4646 기반의 문화권 이름 형식은 *languagecode2-country/regioncode2* 이며, 여기서 *languagecode2* 는 2자 언어 코드이고 *country/regioncode2* 는 2자 하위 문화권 코드입니다. 일본어(일본)의 ja-JP와 영어(미국)의 en-US를 예로 들 수 있습니다. 2자 언어 코드를 사용할 수 없는 경우 ISO 639-2에서 파생된 3자 코드가 사용됩니다.|

#### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>생성된 userPrincipalName(UPN) 값을 소문자로 변환
예: PreferredFirstName 및 PreferredLastName 원본 필드를 결합하고 모든 문자를 소문자로 변환하여 UPN 값을 생성하려고 합니다. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**샘플 입/출력:**

* (PreferredFirstName) **입력**: “John”
* (PreferredLastName) **입력**: “Smith”
* **출력**: "john.smith@contoso.com"


---
### <a name="toupper"></a>ToUpper
**함수:** ToUpper(source, culture)

**설명:** ‘원본’ 문자열 값을 가져와서 지정된 문화권 규칙을 사용하여 대문자로 변환합니다. 지정된 *문화권* 정보가 없는 경우 고정 문화권을 사용합니다.

대상 시스템의 기존 값을 대문자로 설정하려면 [대상 애플리케이션의 스키마를 업데이트](./customize-application-attributes.md#editing-the-list-of-supported-attributes)하고 관심 있는 특성에 대해 caseExact 속성을 'true'로 설정합니다. 

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 참고 |
| --- | --- | --- | --- |
| **source** |필수 |String |대개는 원본 개체의 특성 이름입니다. |
| **culture** |선택 |문자열 |RFC 4646 기반의 문화권 이름 형식은 *languagecode2-country/regioncode2* 이며, 여기서 *languagecode2* 는 2자 언어 코드이고 *country/regioncode2* 는 2자 하위 문화권 코드입니다. 일본어(일본)의 ja-JP와 영어(미국)의 en-US를 예로 들 수 있습니다. 2자 언어 코드를 사용할 수 없는 경우 ISO 639-2에서 파생된 3자 코드가 사용됩니다.|

---
### <a name="word"></a>Word
**함수:** Word(String,WordNumber,Delimiters)

**설명:** Word 함수는 사용할 구분 기호를 설명하는 매개 변수에 따라 문자열 내에 포함된 단어와 반환할 단어 수를 반환합니다. 구분 기호 내의 문자 중 하나로 구분되는 전체 문자열의 각 문자열은 단어로 식별됩니다.

숫자가 < 1인경우 , 빈 문자열을 반환합니다.
문자열이 null이면, 빈 문자열을 반환합니다.
문자열이 단어 수보다 적거나, 구분 기호로 식별되는 단어를 포함할 경우, 빈 문자열이 반환됩니다.

**매개 변수:** 

| 이름 | 필수/ 반복 | Type | 메모 |
| --- | --- | --- | --- |
| **String** |필수 |다중값 특성 |단어를 반환할 문자열.|
| **WordNumber** |필수 | 정수 | 반환해야 하는 단어 수를 식별하는 번호입니다.|
| **delimiters** |필수 |String| 단어를 식별하는 데 사용해야 하는 구분 기호를 나타내는 문자열|

**예:** 
`Word("The quick brown fox",3," ")`

“brown”을 반환합니다.

`Word("This,string!has&many separators",3,",!&#")`

“has”를 반환합니다.

---

## <a name="examples"></a>예
이 섹션에서는 더 많은 식 함수 사용 예제를 제공합니다. 

### <a name="strip-known-domain-name"></a>알려진 도메인 이름 제거
사용자 이름을 가져오려면 사용자의 이메일에서 알려진 도메인 이름을 제거합니다.  예를 들어, 도메인이 "contoso.com"인 경우 다음 식을 사용할 수 있습니다.

**식:**  
`Replace([mail], "@contoso.com", , ,"", ,)`

**샘플 입출력:** 

* **입력**(메일): “john.doe@contoso.com”
* **출력**: "john.doe"


### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>이름과 성의 부분을 연결하여 사용자 별칭을 생성합니다.
사용자의 이름 중 처음 3개 문자 및 사용자 성의 처음 5개 문자를 사용하여 사용자 별칭을 생성합니다.

**식:**  
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**샘플 입/출력:** 

* **입력** (givenName): "John"
* **입력** (surname): "Doe"
* **출력**: "JohDoe"


## <a name="related-articles"></a>관련 문서
* [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](../app-provisioning/user-provisioning.md)
* [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](../app-provisioning/customize-application-attributes.md)
* [사용자 프로 비전에 대 한 필터 범위 지정](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 애플리케이션에 사용자 및 그룹의 자동 프로비전 사용](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [계정 프로비전 알림](../app-provisioning/user-provisioning.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
