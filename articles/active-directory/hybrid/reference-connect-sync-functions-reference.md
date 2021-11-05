---
title: 'Azure AD 동기화 연결: 함수 참조 | Microsoft Docs'
description: Azure AD Connect 동기화의 선언적 프로비전 식을 참조하세요.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d297e7254728debc33b65fe226028fe108426fc9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057479"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD 동기화 연결: 함수 참조
Azure AD Connect에서 동기화 중에 특성 값을 조작하려면 함수를 사용합니다.  
함수의 구문은 다음 형식을 사용하여 표현됩니다.  
`<output type> FunctionName(<input type> <position name>, ..)`

함수가 과부하되거나 여러 구문을 허용한 경우, 모든 사용한 구문의 목록이 나타납니다.  
함수는 강력한 형식이며, 일치하는 문서 형식 안에 들어가는 형식을 확인합니다.  
형식이 일치하지 않는 경우 오류가 throw됩니다.

형식은 다음 구문을 사용하여 표현합니다.

* **bin** - 이진
* **bool** - 부울
* **dt** – UTC 날짜/시간
* **enum** – 알려진 상수의 열거형
* **exp** – 부울로 계산되어야 하는 식
* **mvbin** – 다중값의 이진
* **mvstr** – 다중 값 문자열
* **mvref** – 다중 값 참조
* **num** – 숫자
* **ref** – 참조
* **str** - 문자열
* **var** – 거의 모든 Variant 데이터 형식
* **void** – 값을 반환하지 않음

**mvbin**, **mvstr** 및 **mvref** 형식을 포함하는 함수는 다중값 특성에서만 작동할 수 있습니다. **bin**, **str** 및 **ref** 를 포함한 함수는 단일값 및 다중값 특성에서 작동합니다.

## <a name="functions-reference"></a>함수 참조

* **MSSQLSERVER에 대한 프로토콜 속성**
  * [CertExtensionOids](#certextensionoids)
  * [CertFormat](#certformat)
  * [CertFriendlyName](#certfriendlyname)
  * [CertHashString](#certhashstring)
  * [CertIssuer](#certissuer)
  * [CertIssuerDN](#certissuerdn)
  * [CertIssuerOid](#certissueroid)
  * [CertKeyAlgorithm](#certkeyalgorithm)
  * [CertKeyAlgorithmParams](#certkeyalgorithmparams)
  * [CertNameInfo](#certnameinfo)
  * [CertNotAfter](#certnotafter)
  * [CertNotBefore](#certnotbefore)
  * [CertPublicKeyOid](#certpublickeyoid)
  * [CertPublicKeyParametersOid](#certpublickeyparametersoid)
  * [CertSerialNumber](#certserialnumber)
  * [CertSignatureAlgorithmOid](#certsignaturealgorithmoid)
  * [CertSubject](#certsubject)
  * [CertSubjectNameDN](#certsubjectnamedn)
  * [CertSubjectNameOid](#certsubjectnameoid)
  * [CertThumbprint](#certthumbprint)
  * [CertVersion](#certversion)
  * [IsCert](#iscert)
* **변환**
  * [CBool](#cbool)
  * [CDate](#cdate)
  * [CGuid](#cguid)
  * [ConvertFromBase64](#convertfrombase64)
  * [ConvertToBase64](#converttobase64)
  * [ConvertFromUTF8Hex](#convertfromutf8hex)
  * [ConvertToUTF8Hex](#converttoutf8hex)
  * [CNum](#cnum)
  * [CRef](#cref)
  * [CStr](#cstr)
  * [StringFromGuid](#stringfromguid)
  * [StringFromSid](#stringfromsid)
* **날짜/시간**
  * [DateAdd](#dateadd)
  * [DateFromNum](#datefromnum)
  * [FormatDateTime](#formatdatetime)
  * [Now](#now)
  * [NumFromDate](#numfromdate)
* **디렉터리**
  * [DNComponent](#dncomponent)
  * [DNComponentRev](#dncomponentrev)
  * [EscapeDNComponent](#escapedncomponent)
* **Evaluation**
  * [IsBitSet](#isbitset)
  * [IsDate](#isdate)
  * [IsEmpty](#isempty)
  * [IsGuid](#isguid)
  * [IsNull](#isnull)
  * [IsNullOrEmpty](#isnullorempty)
  * [IsNumeric](#isnumeric)
  * [IsPresent](#ispresent)
  * [IsString](#isstring)
* **Math**
  * [BitAnd](#bitand)
  * [BitOr](#bitor)
  * [RandomNum](#randomnum)
* **다중값**
  * [포함](#contains)
  * [개수](#count)
  * [Item](#item)
  * [ItemOrNull](#itemornull)
  * [Join](#join)
  * [RemoveDuplicates](#removeduplicates)
  * [Split](#split)
* **Program Flow**
  * [오류](#error)
  * [IIF](#iif)
  * [Select](#select)
  * [스위치](#switch)
  * [Where](#where)
  * [With](#with)
* **Text**
  * [GUID](#guid)
  * [InStr](#instr)
  * [InStrRev](#instrrev)
  * [LCase](#lcase)
  * [왼쪽](#left)
  * [Len](#len)
  * [LTrim.](#ltrim)
  * [Mid](#mid)
  * [PadLeft](#padleft)
  * [PadRight](#padright)
  * [PCase](#pcase)
  * [바꾸기](#replace)
  * [ReplaceChars](#replacechars)
  * [오른쪽](#right)
  * [RTrim](#rtrim)
  * [자르기](#trim)
  * [UCase](#ucase)
  * [Word](#word)

---
### <a name="bitand"></a>BitAnd
**설명:**  
BitAnd 함수는 값에 지정된 비트를 설정합니다.

**구문:**  
`num BitAnd(num value1, num value2)`

* value1, value2: 숫자 값은 AND와 함께 사용해야 합니다.

**설명:**  
이 함수는 두 매개 변수를 전부 이진 표현으로 변환시키고 비트를 다음과 같이 설정합니다.

* 0 - *value1* 및 *value2* 의 해당 비트 중 하나 또는 둘 모두가 0인 경우
* 1 - 2개 모두 해당 비트일 경우 1입니다.

즉, 두 매개 변수의 해당 비트가 1일 경우를 제외하는 모든 경우에는 0을 반환합니다.

**예:**  
`BitAnd(&HF, &HF7)`  
16진법 "F" AND "F7"로 이 값을 계산했기 때문에 7을 반환합니다.

---
### <a name="bitor"></a>BitOr
**설명:**  
BitOr 함수는 값에 지정된 비트를 설정합니다.

**구문:**  
`num BitOr(num value1, num value2)`

* value1, value2: 숫자 값은 OR과 함께 사용해야 합니다.

**설명:**  
이 함수는 두 개의 모든 매개 변수를 이진 표현으로 변환하고 마스크 및 플래그 내의 해당 비트가 둘 중에 하나 혹은 둘 다 1일 경우 1로, 해당 비트 모두 0일 경우 0으로 설정합니다. 즉, 두 매개 변수의 해당 비트가 0일 경우를 제외하는 모든 경우에 1을 반환합니다.

---
### <a name="cbool"></a>CBool
**설명:**  
CBool 함수는 계산된 식에 따라 부울을 반환합니다.

**구문:**  
`bool CBool(exp Expression)`

**설명:**  
식에서 0이 아닌 값으로 계산하는 경우 CBool은 True를 반환하고, 그렇지 않으면 False를 반환합니다.

**예:**  
`CBool([attrib1] = [attrib2])`  

두개의 속성이 같은 동일한 값을 가지면 True로 반환합니다.

---
### <a name="cdate"></a>CDate
**설명:**  
CDate 함수는 문자열에서 UTC 날짜/시간을 반환합니다. 날짜/시간은 동기화 내의 네이티브 특성 형식이 아니지만 일부 함수에서 사용됩니다.

**구문:**  
`dt CDate(str value)`

* 값: 날짜, 시간 및 임의적 시간대 문자열

**설명:**  
반환되는 문자열은 항상 UTC로 나타납니다.

**예:**  
`CDate([employeeStartTime])`  
직원의 시작 시간을 기반으로 날짜/시간을 반환합니다.

`CDate("2013-01-10 4:00 PM -8")`  
"2013-01-11 12:00 AM"을 나타내는 날짜/시간을 반환합니다.


---
### <a name="certextensionoids"></a>CertExtensionOids
**설명:**  
인증서 개체의 중요한 모든 확장의 Oid 값을 반환합니다.

**구문:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certformat"></a>CertFormat
**설명:**  
이 X.509v3 인증서의 형식 이름을 반환합니다.

**구문:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certfriendlyname"></a>CertFriendlyName
**설명:**  
인증서와 관련된 별칭을 반환합니다.

**구문:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certhashstring"></a>CertHashString
**설명:**  
X.509v3 인증서의 SHA1 해시 값을 16진수 문자열로 반환합니다.

**구문:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certissuer"></a>CertIssuer
**설명:**  
X.509v3 인증서를 발급한 인증 기관의 이름을 반환합니다.

**구문:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certissuerdn"></a>CertIssuerDN
**설명:**  
인증서 발급자의 고유 이름을 반환합니다.

**구문:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certissueroid"></a>CertIssuerOid
**설명:**  
인증서 발급자의 Oid를 반환합니다.

**구문:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**설명:**  
이 X.509v3 인증서의 키 알고리즘 정보를 문자열로 반환합니다.

**구문:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**설명:**  
X.509v3 인증서의 키 알고리즘 매개 변수를 16진수 문자열로 반환합니다.

**구문:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certnameinfo"></a>CertNameInfo
**설명:**  
인증서의 주체 및 발급자 이름을 반환합니다.

**구문:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.
*   X509NameType: 주체에 대한 X509NameType 값입니다.
*   includesIssuerName: 발급자 이름을 포함하면 true이고, 그렇지 않으면 false입니다.

---
### <a name="certnotafter"></a>CertNotAfter
**설명:**  
인증서가 더 이상 유효하지 않은 이후의 현지 시간으로 날짜를 반환합니다.

**구문:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certnotbefore"></a>CertNotBefore
**설명:**  
인증서가 유효한 현지 시간으로 날짜를 반환합니다.

**구문:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**설명:**  
X.509v3 인증서에 대한 공개 키의 Oid를 반환합니다.

**구문:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**설명:**  
X.509v3 인증서에 대한 공개 키 매개 변수의 Oid를 반환합니다.

**구문:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certserialnumber"></a>CertSerialNumber
**설명:**  
X.509v3 인증서의 일련 번호를 반환합니다.

**구문:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**설명:**  
인증서의 서명을 만드는 데 사용된 알고리즘의 Oid를 반환합니다.

**구문:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certsubject"></a>CertSubject
**설명:**  
인증서에서 주체 고유 이름을 가져옵니다.

**구문:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**설명:**  
인증서의 고유한 주체 이름을 반환합니다.

**구문:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**설명:**  
인증서의 주체 이름에 대한 Oid를 반환합니다.

**구문:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certthumbprint"></a>CertThumbprint
**설명:**  
인증서의 지문을 반환합니다.

**구문:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="certversion"></a>CertVersion
**설명:**  
인증서의 X.509 형식 버전을 반환합니다.

**구문:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.

---
### <a name="cguid"></a>CGuid
**설명:**  
CGuid 함수는 GUID의 문자열 표현을 이진 표현으로 변환합니다.

**구문:**  
`bin CGuid(str GUID)`

* 이 패턴에서 문자열 서식: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 또는 {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

---
### <a name="contains"></a>포함
**설명:**  
Contains 함수는 다중 값 특성에 포함된 문자열을 찾습니다.

**구문:**  
`num Contains (mvstring attribute, str search)` - 대/소문자 구분  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` - 대/소문자 구분

* 특성: 다중 값 특성을 검색합니다.
* 검색: 특성에서 찾을 문자열입니다.
* 대소문자 유형: 대소문자를 구분하거나 구분하지 않습니다.

문자열이 발견된 다중값 특성에 인덱스를 반환합니다. 문자열을 찾을 수 없는 경우 0이 반환됩니다.

**설명:**  
다중값 문자열 특성에 대한 검색 값에서 부분 문자열을 찾습니다.  
참조 특성에 대해 검색된 문자열은 일치하는 것으로 간주될 값에 정확히 일치해야 합니다.

**예:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
proxyAddresses 특성이 기본 전자 메일 주소(대문자로 표시 “SMTP:”)를 가질 경우 proxyAddress 특성이 반환되며, 그 외에는 오류가 반환됩니다.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**설명:**  
ConvertFromBase64 함수는 지정된 base64 인코딩 값을 일반 문자열로 변환합니다.

**구문:**  
`str ConvertFromBase64(str source)` - 인코딩에 유니코드 가정  
`str ConvertFromBase64(str source, enum Encoding)`

* 원본: Base64 인코딩된 문자열  
* Encoding: 유니코드, ASCII, UTF8

**예제**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

두 예제 모두 "*Hello world!*"를 반환합니다.

---
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**설명:**  
ConvertFromUTF8Hex 함수는 지정된 UTF8 16진수 인코딩 값을 문자열로 변환합니다.

**구문:**  
`str ConvertFromUTF8Hex(str source)`

* 원본: UTF8 2-바이트 인코딩된 문자열

**설명:**  
이 함수와 ConvertFromBase64([],UTF8) 결과의 차이점은 DN 특성을 사용하기 쉽다는 것입니다.  
이 형식은 DN으로 Azure Active Directory에서 사용됩니다.

**예:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
"*Hello world!*"를 반환합니다.

---
### <a name="converttobase64"></a>ConvertToBase64
**설명:**  
ConvertToBase64 함수는 문자열을 유니코드 base64 문자열로 변환합니다.  
정수 배열 값을 base 64 자릿수로 인코딩된 동등한 문자열 표현으로 변환합니다.

**구문:**  
`str ConvertToBase64(str source)`

**예:**  
`ConvertToBase64("Hello world!")`  
"SABlAGwAbABvACAAdwBvAHIAbABkACEA"를 반환합니다.

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**설명:**  
ConvertToUTF8Hex 함수는 문자열을 UTF8 16진수 인코딩 값으로 변환합니다.

**구문:**  
`str ConvertToUTF8Hex(str source)`

**설명:**  
이 함수의 출력 형식은 DN 특성 형식으로 Azure Active Directory에서 사용됩니다.

**예:**  
`ConvertToUTF8Hex("Hello world!")`  
48656C6C6F20776F726C6421을 반환합니다.

---
### <a name="count"></a>개수
**설명:**  
Count 함수는 다중값 특성의 요소 수를 반환합니다.

**구문:**  
`num Count(mvstr attribute)`

---
### <a name="cnum"></a>CNum
**설명:**  
CNum 함수는 문자열을 숫자 데이터 형식으로 반환합니다.

**구문:**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**설명:**  
문자열을 참조 특성으로 변환합니다.

**구문:**  
`ref CRef(str value)`

**예:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

---
### <a name="cstr"></a>CStr
**설명:**  
CStr 함수는 문자열 데이터 형식으로 변환합니다.

**구문:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* 값: 숫자 값, 참조 특성 또는 부울입니다.

**예:**  
`CStr([dn])`  
"cn=Joe,dc=contoso,dc=com"을 반환할 수 있습니다.

---
### <a name="dateadd"></a>DateAdd
**설명:**  
지정된 시간 간격이 추가된 날짜를 포함하는 날짜를 반환합니다.

**구문:**  
`dt DateAdd(str interval, num value, dt date)`

* 간격: 추가하고자 하는 시간 간격에 해당하는 문자열 식입니다. 문자열은 다음 값 중 하나가 있어야 합니다.
  * yyyy 년
  * q 분기
  * m 월
  * y 연간 일자
  * d 일
  * w 요일
  * ww 주
  * h 시간
  * n 분
  * s 초
* 값: 추가하고자 하는 단위 수 입니다. 양수(미래 날짜) 또는 음수(과거 날짜)가 될 수 있습니다.
* 날짜: 날짜/시간은 간격이 추가된 날짜로 나타납니다.

**예:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
3개월을 추가하고 "2001-04-01"을 나타내는 날짜/시간을 반환합니다.

---
### <a name="datefromnum"></a>DateFromNum
**설명:**  
DateFromNum 함수는 AD의 날짜 값 형식을 날짜/시간 형식으로 변환합니다.

**구문:**  
`dt DateFromNum(num value)`

**예:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
2012-01-01 23:00:00을 나타내는 날짜/시간을 반환합니다.

---
### <a name="dncomponent"></a>DNComponent
**설명:**  
DNComponent 함수는 왼쪽부터 지정된 DN 구성 요소의 값을 반환합니다.

**구문:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: 참조 특성 해석
* ComponentNumber: 반환할 DN 내의 구성 요소

**예:**  
`DNComponent(CRef([dn]),1)`  
dn이 "cn=Joe,ou=…"인 경우 Joe를 반환합니다.

---
### <a name="dncomponentrev"></a>DNComponentRev
**설명:**  
DNComponentRev 함수는 오른쪽(끝)부터 지정된 DN 구성 요소의 값을 반환합니다.

**구문:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* dn: 참조 특성 해석
* ComponentNumber-반환할 DN 내의 구성 요소
* 옵션: DC –"dc ="가 있는 모든 구성 요소를 무시합니다.

**예:**  
dn이 "cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com"인 경우  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
모두 US를 반환합니다.

---
### <a name="error"></a>Error
**설명:**  
Error 함수는 사용자 지정 오류를 반환하는 데 사용됩니다.

**구문:**  
`void Error(str ErrorMessage)`

**예:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
accountName 특성이 없는 경우 개체에서 오류가 throw됩니다.

---
### <a name="escapedncomponent"></a>EscapeDNComponent
**설명:**  
EscapeDNComponent 함수는 DN의 한 구성 요소를 받고 LDAP에 나타낼 수 있도록 이스케이프합니다.

**구문:**  
`str EscapeDNComponent(str value)`

**예:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
LDAP 디렉터리 내에서, displayName 특성에 LDAP에서 이스케이프된 문자가 포함된 경우에도 개체가 생성될 수 있음을 확인합니다.

---
### <a name="formatdatetime"></a>FormatDateTime
**설명:**  
FormatDateTime 함수는 날짜/시간을 지정된 형식의 문자열로 구성하는 데 사용됩니다.

**구문:**  
`str FormatDateTime(dt value, str format)`

* value: 날짜/시간 형식의 값
* 형식: 변환할 형식을 나타내는 문자열입니다.

**설명:**  
형식에 대한 가능한 값은 [FORMAT에 대한 사용자 지정 날짜 및 시간 형식](/dax/custom-date-and-time-formats-for-the-format-function)에서 확인할 수 있습니다.

**예:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-MM-dd")`  
"2007-12-25"를 반환합니다.

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
"20140905081453.0Z"를 반환할 수 있습니다.

---
### <a name="guid"></a>GUID
**설명:**  
함수 Guid는 임의의 GUID를 새로 생성합니다.

**구문:**  
`str Guid()`

---
### <a name="iif"></a>IIF
**설명:**  
IIF 함수는 지정된 조건에 따라 가능한 값 집합 중 하나를 반환합니다.

**구문:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* 조건: true 또는 false로 계산될 수 있는 임의의 값 또는 식입니다.
* valueIfTrue: 조건이 true로 평가되는 경우 반환된 값입니다.
* valueIfFalse: 조건이 false로 평가되는 경우 반환된 값입니다.

**예:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 사용자가 인턴일 경우 사용자 별칭 앞에 “t-”를 추가하여 반환하고, 그 외의 경우에는 본래의 별칭 그대로 반환합니다.

---
### <a name="instr"></a>InStr
**설명:**  
InStr 함수는 문자열에서 부분 문자열이 처음 나오는 경우를 찾습니다.

**구문:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: 검색할 문자열
* stringmatch: 찾을 문자열
* start: 부분 문자열을 찾을 시작 위치
* compare: vbTextCompare 또는 vbBinaryCompare

**설명:**  
부분 문자열을 찾으면 위치가 반환되고, 찾지 못할 경우 0이 반환됩니다.

**예:**  
`InStr("The quick brown fox","quick")`  
5로 계산합니다.

`InStr("repEated","e",3,vbBinaryCompare)`  
7로 계산합니다.

---
### <a name="instrrev"></a>InStrRev
**설명:**  
InStrRev 함수는 문자열에서 부분 문자열이 마지막으로 나오는 경우를 찾습니다.

**구문:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: 검색할 문자열
* stringmatch: 찾을 문자열
* start: 부분 문자열을 찾을 시작 위치
* compare: vbTextCompare 또는 vbBinaryCompare

**설명:**  
부분 문자열을 찾으면 위치가 반환되고, 찾지 못할 경우 0이 반환됩니다.

**예:**  
`InStrRev("abbcdbbbef","bb")`  
7을 반환합니다.

---
### <a name="isbitset"></a>IsBitSet
**설명:**  
IsBitSet 함수는 비트 설정 여부를 테스트합니다.

**구문:**  
`bool IsBitSet(num value, num flag)`

* 값: 숫자 값은 evaluated.flag: 숫자 값은 계산할 수 있는 숫자 값을 가집니다.

**예:**  
`IsBitSet(&HF,4)`  
비트 "4"는 16진수 값 "F" 안에 설정되어 있으므로 True를 반환합니다.

---
### <a name="isdate"></a>IsDate
**설명:**  
식이 날짜/시간 형식으로 계산될 경우 IsDate 함수는 True로 계산됩니다.

**구문:**  
`bool IsDate(var Expression)`

**설명:**  
CDate()가 정상적으로 수행될 수 있는지 결정하는 데 사용됩니다.

---
### <a name="iscert"></a>IsCert
**설명:**  
원시 데이터를 .NET X509Certificate2 인증서 개체로 직렬화할 수 있으면 true를 반환합니다.

**구문:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: X.509 인증서의 바이트 배열 표현입니다. 바이트 배열은 DER(이진) 또는 Base64로 인코딩된 X.509 데이터일 수 있습니다.
---
### <a name="isempty"></a>IsEmpty
**설명:**  
특성이 CS 또는 MV에서 나타나지만 빈 문자열로 계산될 경우 IsEmpty 함수는 True로 계산됩니다.

**구문:**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid
**설명:**  
문자열을 GUID로 변환할 수 있는 경우 IsGuid 함수는 True로 계산합니다.

**구문:**  
`bool IsGuid(str GUID)`

**설명:**  
GUID는 다음 패턴 중 하나로 정의됩니다. xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 또는 {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

CGuid()가 성공적으로 수행될 수 있는지 여부를 결정하는데 사용됩니다.

**예:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
StrAttribute가 GUID 형식을 가질 경우, 이진 표현으로 반환되며, 아닐 경우 Null을 반환합니다.

---
### <a name="isnull"></a>IsNull
**설명:**  
식이 Null로 계산되면 IsNull 함수는 true를 반환합니다.

**구문:**  
`bool IsNull(var Expression)`

**설명:**  
특성이 없는 경우 Null로 표현됩니다.

**예:**  
`IsNull([displayName])`  
CS 또는 MV에 특성이 없을 경우 True를 반환합니다.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**설명:**  
식이 null 또는 빈 문자열일 경우 IsNullOrEmpty 함수는 true를 반환합니다.

**구문:**  
`bool IsNullOrEmpty(var Expression)`

**설명:**  
특성이 없거나, 빈 문자열로 존재하는 경우 True로 계산합니다.  
이 함수의 역원을 IsPresnt라고 합니다.

**예:**  
`IsNullOrEmpty([displayName])`  
특성이 없거나 CS 또는 MV에서 빈 문자열인 경우 True를 반환합니다.

---
### <a name="isnumeric"></a>IsNumeric
**설명:**  
IsNumeric 함수에는 숫자 형식으로 식이 계산될 수 있는지 여부를 나타내는 부울 값을 반환합니다.

**구문:**  
`bool IsNumeric(var Expression)`

**설명:**  
CNum()이 식을 구문 분석하는 데 성공할 수 있는지 여부를 결정할 때 사용됩니다.

---
### <a name="isstring"></a>IsString
**설명:**  
식이 문자열 형식으로 계산될 수 있는 경우 IsString 함수는 True로 계산됩니다.

**구문:**  
`bool IsString(var expression)`

**설명:**  
CStr()이 식을 구문 분석하는 데 성공할 수 있는지 여부를 결정할 때 사용됩니다.

---
### <a name="ispresent"></a>IsPresent
**설명:**  
식이 Null이 아니고 비어 있지 않은 문자열로 계산되는 경우 IsPresent 함수는 true를 반환합니다.

**구문:**  
`bool IsPresent(var expression)`

**설명:**  
이 함수의 역함수는 IsNullOrEmpty으로 지칭됩니다.

**예:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>항목
**설명:**  
Item 함수는 다중값 문자열/특성에서 하나의 항목을 반환합니다.

**구문:**  
`var Item(mvstr attribute, num index)`

* attribute: 다중값 특성
* 인덱스: 다중값 문자열에 있는 항목에 대한 인덱스입니다.

**설명:**  
Item 함수는 다중값 특성의 항목에 대한 인덱스를 반환하는 Contains 함수와 함께 사용할 수 있습니다.

인덱스가 범위를 초과하는 경우 오류가 나타납니다.

**예:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
기본 전자 메일 주소를 반환합니다.

---
### <a name="itemornull"></a>ItemOrNull
**설명:**  
ItemOrNull 함수는 다중값 문자열/특성에서 하나의 항목을 반환합니다.

**구문:**  
`var ItemOrNull(mvstr attribute, num index)`

* attribute: 다중값 특성
* 인덱스: 다중값 문자열에 있는 항목에 대한 인덱스입니다.

**설명:**  
ItemOrNull 함수는 다중값 특성의 항목에 대한 인덱스를 반환하는 Contains 함수와 함께 사용할 수 있습니다.

인덱스가 범위를 초과하는 경우 Null 값을 반환합니다.

---
### <a name="join"></a>Join
**설명:**  
Join 함수는 다중값 문자열을 사용하여 각 항목 사이에 지정된 구분 기호를 삽입하여 단일 값 문자열을 반환합니다.

**구문:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* attribute: 연결할 문자열을 포함하는 다중값 특성
* 구분 기호: 반환된 문자열의 부분 문자열을 구분하는데 사용되는 모든 문자열입니다. 이 인수를 생략하면 공백 문자(" ")가 사용됩니다. 구분 기호가 길이가 0인 문자열(“”)또는 없을 경우 ,목록에서 모든 항목이 구분 기호 없이 연결됩니다.

**주의**  
Join 및 Split 함수 사이에 패리티가 있습니다. Join 함수는 단일 문자열을 반환하기 위해 문자열의 배열을 채택하고  구분 기호 문자열을 사용하여 배열을 연결합니다. Split 함수는 문자열의 배열을 반환하기 위해 문자열을 채택하고 구분 기호로 구분합니다. 그러나 Join 함수는 모든 구분 기호 문자열을 사용하여 문자열을 연결할 수 있지만, Split 함수는 단일 문자 구분 기호를 사용하여 오직 문자열을 나눌 수만 있다는 것이 가장 중요한 차이점입니다.

**예:**  
`Join([proxyAddresses],",")`  
"SMTP:john.doe@contoso.com,smtp:jd@contoso.com"을 반환할 수 있습니다.

---
### <a name="lcase"></a>LCase
**설명:**  
LCase 함수는 문자열의 모든 문자를 소문자로 변환합니다.

**구문:**  
`str LCase(str value)`

**예:**  
`LCase("TeSt")`  
"test"를 반환합니다.

---
### <a name="left"></a>왼쪽
**설명:**  
Left 함수는 문자열 왼쪽부터 지정된 수의 문자를 반환합니다.

**구문:**  
`str Left(str string, num NumChars)`

* string: 문자로 반환될 문자열입니다.
* NumChars: 문자열의 시작(왼쪽)에서 반환될 문자의 개수를 식별하는 번호입니다.

**설명:**  
문자열의 첫 번째 numChars 문자를 포함하는 문자열입니다.

* numChars = 0 인 경우, 빈 문자열을 반환합니다.
* numCahrs < 0,인 경우, 입력된 문자열을 반환합니다.
* 문자열이 null이면 빈 문자열을 반환합니다.

문자열이 numChars 내에서 숫자가 지정한 문자보다 적은 문자를 포함하는 경우, 문자열과 동일한 문자열(즉, 매개 변수 1의 모든 문자가 포함)이 반환됩니다.

**예:**  
`Left("John Doe", 3)`  
"Joh"를 반환합니다.

---
### <a name="len"></a>Len
**설명:**  
Len 함수는 문자열의 문자 수를 반환합니다.

**구문:**  
`num Len(str value)`

**예:**  
`Len("John Doe")`  
8을 반환합니다.

---
### <a name="ltrim"></a>LTrim
**설명:**  
LTrim 함수는 문자열에서 선행 공백을 제거합니다.

**구문:**  
`str LTrim(str value)`

**예:**  
`LTrim(" Test ")`  
"Test"를 반환합니다.

---
### <a name="mid"></a>Mid
**설명:**  
Mid 함수는 문자열의 지정된 위치부터 지정된 수의 문자를 반환합니다.

**구문:**  
`str Mid(str string, num start, num NumChars)`

* string: 문자로 반환될 문자열입니다.
* start : 문자열 내의 시작지점에서 반환할 문자를 식별하는 번호입니다.
* NumChars: 문자열의 위치에서 반환될 문자의 수를 식별하는 번호입니다.

**설명:**  
문자열의 시작 위치에서 시작되는 numChars 문자를 반환합니다.  
문자열의 start 위치에서 numChars 문자를 포함하는 문자열:

* numChars = 0 인 경우, 빈 문자열을 반환합니다.
* numCahrs < 0,인 경우, 입력된 문자열을 반환합니다.
* start > 문자열의 길이인 경우, 입력된 문자열을 반환 합니다.
* start < = 0 인 경우, 입력된 문자열을 반환 합니다.
* 문자열이 null이면 빈 문자열을 반환합니다.

시작 위치에서 문자열의 numChar 문자가 남아있지 않는 경우, 가능한 많은 문자가 반환됩니다.

**예:**  
`Mid("John Doe", 3, 5)`  
"hn Do"를 반환합니다.

`Mid("John Doe", 6, 999)`  
"Doe"를 반환합니다.

---
### <a name="now"></a>지금
**설명:**  
Now 함수는 컴퓨터의 시스템 날짜 및 시간에 따라 현재 날짜 및 시간을 지정하는 날짜/시간을 반환합니다.

**구문:**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**설명:**  
NumFromDate 함수는 AD 날짜 형식으로 날짜를 반환합니다.

**구문:**  
`num NumFromDate(dt value)`

**예:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
129699324000000000을 반환합니다.

---
### <a name="padleft"></a>PadLeft
**설명:**  
PadLeft 함수는 제공된 채움 문자를 사용하여 문자열을 지정된 길이로 왼쪽 채움합니다.

**구문:**  
`str PadLeft(str string, num length, str padCharacter)`

* 문자열: 문자열을 채웁니다.
* 길이: 원하는 문자열의 길이를 나타내는 정수입니다.
* padCharacter: 채움 문자를 사용하여 단일 문자로 문자열을 구성합니다.

**설명:**

* 문자열의 길이가 본래의 길이보다 짧을 경우, 길이가 같아질때가지 문자열의 시작 부분(왼쪽)에 padCharacter를 반복적으로 추가합니다.
* PadCharacter는 공백 문자가 될 수 있지만, null 값은 될 수 없습니다.
* 문자열이 길이가 본래의 길이보다 같거나 클 경우, 문자열은 변함이 없습니다.
* 문자열 길이가 크거나 같을 경우, 문자열과 동일한 문자열이 반환됩니다.
* 문자열의 길이가 본래의 길이보다 작은 경우, 채움 문자로 채워진 문자열이 포함된 원하는 길이의 새 문자열이 반환됩니다.
* 문자열이 null이면, 함수는 빈 문자열을 반환합니다.

**예:**  
`PadLeft("User", 10, "0")`  
"000000User"를 반환합니다.

---
### <a name="padright"></a>PadRight
**설명:**  
PadRight 함수는 제공된 채움 문자를 사용하여 지정된 길이로 문자열을 오른쪽 채움합니다.

**구문:**  
`str PadRight(str string, num length, str padCharacter)`

* 문자열: 문자열을 채웁니다.
* 길이: 원하는 문자열의 길이를 나타내는 정수입니다.
* padCharacter: 채움 문자를 사용하여 단일 문자로 문자열을 구성합니다.

**설명:**

* 문자열의 길이가 본래의 길이보다 짧을 경우, 길이가 같아질때까지 문자열의 끝(오른쪽)에 padCharacter를 반복적으로 추가합니다.
* PadCharacter는 공백 문자가 될 수 있지만, null 값은 될 수 없습니다.
* 문자열이 길이가 본래의 길이보다 같거나 클 경우, 문자열은 변함이 없습니다.
* 문자열 길이가 크거나 같을 경우, 문자열과 동일한 문자열이 반환됩니다.
* 문자열의 길이가 본래의 길이보다 작은 경우, 채움 문자로 채워진 문자열이 포함된 원하는 길이의 새 문자열이 반환됩니다.
* 문자열이 null이면, 함수는 빈 문자열을 반환합니다.

**예:**  
`PadRight("User", 10, "0")`  
"User000000"을 반환합니다.

---
### <a name="pcase"></a>PCase
**설명:**  
PCase 함수는 문자열내의 각 공백으로 구분된 단어의 첫 문자를 대문자로 변환하고 다른 모든 문자를 소문자로 변환합니다.

**구문:**  
`String PCase(string)`

**설명:**

* 이 기능은 현재 머리글자어와 같이 전체적으로 대문자인 단어를 변경할 적절한 대/소문자 구분을 제공하지 않습니다.

**예:**  
`PCase("TEsT")`  
"test"를 반환합니다.

`PCase(LCase("TEST"))`  
"Test"를 반환합니다.

---
### <a name="randomnum"></a>RandomNum
**설명:**  
RandomNum 함수는 지정된 간격 사이의 난수를 반환합니다.

**구문:**  
`num RandomNum(num start, num end)`

* start: 생성할 난수 값의 하한을 식별하는 번호
* 끝: 난수 값의 상한값을 식별 하는 번호를 생성

**예:**  
`Random(100,999)`  
734를 반환할 수 있습니다.

---
### <a name="removeduplicates"></a>RemoveDuplicates
**설명:**  
RemoveDuplicates 함수는 다중값 문자열을 사용하여 개별 값을 고유하게 만듭니다.

**구문:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**예:**  
`RemoveDuplicates([proxyAddresses])`  
모든 중복 값을 제거한 삭제된 proxyAddress 특성을 반환합니다.

---
### <a name="replace"></a>바꾸기
**설명:**  
Replace 함수는 한 문자열이 나오는 모든 경우를 다른 문자열로 바꿉니다.

**구문:**  
`str Replace(str string, str OldValue, str NewValue)`

* string: 값을 바꿀 문자열
* OldValue: 검색한 후 바꿀 문자열
* NewValue: 문자열을 대체합니다.

**설명:**  
이 함수는 다음의 특별한 모니커를 인식합니다.

* \n – 새로운 줄
* \r – 캐리지 반환
* \t – 탭

**예:**  
`Replace([address],"\r\n",", ")`  
CRLF를 쉼표와 공백으로 바꾸고 "One Microsoft Way, Redmond, WA, USA"로 나타낼 수 있습니다.

---
### <a name="replacechars"></a>ReplaceChars
**설명:**  
ReplaceChars 함수는 ReplacePattern 문자열에 해당 문자가 나오는 모든 경우를 바꿉니다.

**구문:**  
`str ReplaceChars(str string, str ReplacePattern)`

* string: 문자를 대체하는 문자열입니다.
* ReplacePattern: 바꿀 문자를 사용하여 사전에 포함하는 문자열입니다.

형식은 {source1}: {target1} {source2}: {target2} {sourceN} {targetN} 원본은 찾을 문자열이고 대상은 대체할 문자열입니다.

**설명:**

* 함수는 정의된 원본의 각 항목을 사용하여 대상의 항목을 대체합니다.
* 원본은 하나의 문자만(유니코드) 있어야 합니다.
* 원본은 공백이거나 한글자 이상일 수 없습니다(구문 분석 오류).
* ö:oe, β:ss처럼 대상에 여러 문자가 있을 수 있습니다.
* 문자가 제거되어야 하는 경우 대상은 비어있을 수 있습니다.
* 원본은 대소문자를 구분하며 정확히 일치해야 합니다.
* ,(쉼표) 및: (콜론)은 지정된 문자이며 이 함수를 사용하여 바꿀 수 없습니다.
* ReplacePattern의 공백 및 white character는 무시됩니다.

**예:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Raksmorgas를 반환합니다.

`ReplaceChars("O’Neil",%ReplaceString%)`  
"ONeil"을 반환합니다. 단일 틱이 제거 대상으로 정의됩니다.

---
### <a name="right"></a>오른쪽
**설명:**  
Right 함수는 문자열의 오른쪽(끝)부터 지정된 수의 문자를 반환합니다.

**구문:**  
`str Right(str string, num NumChars)`

* string: 문자로 반환될 문자열입니다.
* NumChars: 숫자는 문자열의 끝(오른쪽)에서 반환될 문자열의 수를 식별합니다.

**설명:**  
문자열의 마지막 위치부터 NumChars 문자가 반환됩니다.

문자열의 마지막 numChars 문자를 포함 하는 문자열:

* numChars = 0 인 경우, 빈 문자열을 반환합니다.
* numCahrs < 0,인 경우, 입력된 문자열을 반환합니다.
* 문자열이 null이면 빈 문자열을 반환합니다.

문자열의 문자가 NumChars의 지정된 숫자보다 적은 경우, 문자열과 동일한 문자열이 반환됩니다.

**예:**  
`Right("John Doe", 3)`  
"Doe"를 반환합니다.

---
### <a name="rtrim"></a>RTrim
**설명:**  
RTrim 함수는 문자열에서 후행 공백을 제거합니다.

**구문:**  
`str RTrim(str value)`

**예:**  
`RTrim(" Test ")`  
"Test"를 반환합니다.

---
### <a name="select"></a>선택
**설명:**  
지정된 함수에 기반하여 다중값 특성(또는 식 출력)의 모든 값을 처리합니다.

**구문:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* item: 다중값 특성의 요소를 나타냅니다.
* attribute: 다중값 특성입니다.
* expression: 값의 컬렉션을 반환하는 식입니다.
* condition: 특성의 한 항목을 처리할 수 있는 모든 함수입니다.

**예:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
하이픈(-)을 제거한 후에 otherPhone 다중값 특성의 모든 값을 반환합니다.

---
### <a name="split"></a>분할
**설명:**  
Split 함수는 구분 기호로 구분된 문자열을 다중값 문자열로 만듭니다.

**구문:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* value: 구분할 구분 기호 문자를 포함하는 문자열입니다.
* delimiter: 구분 기호로 사용할 수 있는 단일 문자입니다.
* limit: 반환될 수 있는 값의 최대 갯수입니다.

**예:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
proxyAddress 특성에 유용한 2개 이상의 요소가 있는 다중값 문자열을 반환합니다.

---
### <a name="stringfromguid"></a>StringFromGuid
**설명:**  
StringFromGuid 함수는 이진 GUID를 문자열로 변환합니다.

**구문:**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**설명:**  
StringFromSid 함수는 보안 식별자를 포함한 바이트 배열을 문자열로 변환합니다.

**구문:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>스위치
**설명:**  
Switch 함수는 계산 조건에 따라 단일 값을 반환하는 데 사용됩니다.

**구문:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr: 계산하고자 하는 변형 식입니다.
* value: 해당 식이 True일 경우 반환될 값입니다.

**설명:**  
Switch 함수 인수 목록은 식과 값의 쌍으로 구성됩니다. 식은 왼쪽에서 오른쪽으로 계산되며, True로 계산되는 첫 번째 식과 연결된 값이 반환됩니다. 파트가 적절한 쌍으로 연결되지 않으면, 런타임 오류가 발생합니다.

예를들어, expr1이 True면, Switch는 value1을 반환합니다. expr-1이 False이고, expr-2가 True면 Switch는 Value-2로 반환합니다.

다음의 경우 Switch는 Nothing을 반환합니다.

* 어떤 식도 True가 아닌 경우
* 첫 번째 True 식의 해당 값이 Null인 경우

그 중 하나만 반환되더라도, Switch는 모든 식을 계산합니다. 그렇기 때문에 원하지 않는 결과가 나타나지 않도록 주의해야합니다. 예를 들어, 어떤 식의 계산이 division by zero 오류로 나올 경우, 오류가 발생한 것입니다.

값은 사용자 지정 문자열을 반환하는 오류 함수가 될 수도 있습니다.

**예:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
일부 주요 도시에서 사용하는 언어를 반환하지만 그 외의 경우에는 오류를 반환합니다.

---
### <a name="trim"></a>공백 제거
**설명:**  
Trim 함수는 선행 및 후행 공백을 문자열에서 제거합니다.

**구문:**  
`str Trim(str value)`  

**예:**  
`Trim(" Test ")`  
"test"를 반환합니다.

`Trim([proxyAddresses])`  
proxyAddress 특성의 각 값에 대한 선행 및 후행 공백을 제거합니다.

---
### <a name="ucase"></a>UCase
**설명:**  
UCase 함수는 문자열의 모든 문자를 대문자로 변환합니다.

**구문:**  
`str UCase(str string)`

**예:**  
`UCase("TeSt")`  
"test"를 반환합니다.

---
### <a name="where"></a>Where

**설명:**  
특정 조건에 기반하여 다중값 특성(또는 식 출력)의 값에 대한 하위 집합을 반환합니다.

**구문:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* item: 다중값 특성의 요소를 나타냅니다.
* attribute: 다중값 특성입니다.
* condition: true 또는 false로 평가될 수 있는 모든 식입니다.
* expression: 값의 컬렉션을 반환하는 식입니다.

**예:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
만료되지 않은 userCertificate 다중값 특성의 인증서 값을 반환합니다.

---
### <a name="with"></a>With
**설명:**  
With 함수는 복합 식에서 한 번 이상 나타나는 하위 식을 변수로 표현하여 복합 식을 단순화하는 방법을 제공합니다.

**구문:** 
`With(var variable, exp subExpression, exp complexExpression)`  
* variable: 하위 식을 나타냅니다.
* subExpression: 변수로 표현되는 하위 식입니다.
* complexExpression: 복합 식입니다.

**예:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
기능적으로 다음과 같습니다.  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
userCertificate 특성에서 만료되지 않은 인증서 값만 반환합니다.


---
### <a name="word"></a>Word
**설명:**  
Word 함수는 사용할 구분 기호를 설명하는 매개 변수에 따라 문자열 내에 포함된 단어와 반환할 단어 수를 반환합니다.

**구문:**  
`str Word(str string, num WordNumber, str delimiters)`

* string: 단어를 반환할 문자열입니다.
* WordNumber: 반환해야 하는 단어 수를 식별하는 번호입니다.
* delimiters : 단어를 식별하는데 사용될 구분 기호를 나타내는 문자열입니다.

**설명:**  
구분 기호 내의 문자 중 하나로 구분되는 전체 문자열의 각 문자열은 단어로 식별됩니다.

* 숫자가 < 1인경우 , 빈 문자열을 반환합니다.
* 문자열이 null이면, 빈 문자열을 반환합니다.

문자열이 단어 수보다 적거나, 구분 기호로 식별되는 단어를 포함할 경우, 빈 문자열이 반환됩니다.

**예:**  
`Word("The quick brown fox",3," ")`  
"brown"을 반환합니다.

`Word("This,string!has&many separators",3,",!&#")`  
"has"를 반환합니다.

## <a name="additional-resources"></a>추가 리소스
* [선언적 프로비전 식 이해](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect Sync: 사용자 지정 동기화 옵션](how-to-connect-sync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)
