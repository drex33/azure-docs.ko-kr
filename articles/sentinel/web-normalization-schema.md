---
title: Microsoft Sentinel 웹 세션 정규화 스키마 참조(공개 미리 보기) | Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel 웹 세션 정규화 스키마를 표시합니다.
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
ms.topic: reference
ms.date: 11/17/2021
ms.author: ofshezaf
ms.openlocfilehash: 2a80d0b6e9be6d2eced6c59dced19d09aa4b35d3
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386825"
---
# <a name="microsoft-sentinel-web-session-normalization-schema-reference-public-preview"></a>Microsoft Sentinel 웹 세션 정규화 스키마 참조(공개 미리 보기)

웹 세션 정규화 스키마는 IP 네트워크 활동을 설명하는 데 사용됩니다. 예를 들어 IP 네트워크 활동은 웹 서버, 웹 프록시 및 웹 보안 게이트웨이에서 보고됩니다.

Microsoft Sentinel의 정규화에 대한 자세한 내용은 [정규화 및 ASIM(고급 SIEM 정보 모델)을](normalization.md)참조하세요.

> [!IMPORTANT]
> 네트워크 정규화 스키마는 현재 미리 보기로 제공됩니다. 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
>
> [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="schema-overview"></a>스키마 개요

웹 세션 정규화 스키마는 HTTP 네트워크 세션을 나타내며, 다음을 비롯한 일반적인 소스 형식에 대한 지원을 제공하는 데 특히 적합합니다.

- 웹 서버
- 웹 프록시
- 웹 보안 게이트웨이

ASIM 웹 세션 스키마는 HTTP 및 HTTPS 프로토콜 활동을 나타냅니다. 스키마는 프로토콜 활동을 나타내므로 RFC 및 공식적으로 할당된 매개 변수 목록에 의해 제어되며, 해당하는 경우 이 문서에서 참조됩니다. 

웹 세션 스키마는 원본 디바이스의 감사 이벤트를 나타내지 않습니다. 예를 들어 웹 보안 게이트웨이 정책을 수정하는 이벤트는 웹 세션 스키마로 나타낼 수 없습니다.

HTTP 세션은 TCP/IP를 기본 네트워크 계층 세션으로 활용하는 애플리케이션 계층 세션이므로 웹 세션 스키마는 [ASIM 네트워크 세션 스키마](network-normalization-schema.md)의 슈퍼 집합입니다.

웹 세션 스키마에서 가장 중요한 필드는 다음과 같습니다.

- [Url](#url)- 클라이언트가 서버에서 요청한 URL을 보고합니다.
- 요청이 생성된 IP 주소를 나타내는 [SrcIpAddr(IpAddr에](network-normalization-schema.md#ipaddr)별칭 지정)입니다. [](network-normalization-schema.md#srcipaddr) 
- [EventResultDetails -](#eventresultdetails)HTTP 상태 코드를 보고합니다.

웹 세션 [이벤트에는](network-normalization-schema.md#user) 사용자 및 요청을 시작하는 [프로세스에](process-events-normalization-schema.md) 대한 사용자 및 프로세스 정보가 포함될 수도 있습니다. 

## <a name="parsers"></a>파서

### <a name="source-agnostic-parsers"></a>원본 중립적 파서

모든 첫 번째 파서가 통합되는 소스에 구애받지 않는 파서 를 사용하고 구성된 모든 원본에서 분석이 실행되도록 하려면 다음 KQL 함수를 쿼리의 테이블 이름으로 사용합니다.

| 이름 | 설명 | 사용 지침 |
| ---- | --- | --- |
| <a name="imwebsession"></name>**imWebSession** | *union을* 사용하여 모든 *웹 세션* 원본의 정규화된 이벤트를 포함하는 집계 파서입니다. <br><br>예제: HTTP 세션 필드를 지원하는 네트워크 [세션 필드](#http-session-fields) |- 원본 독립적 분석에서 원본을 추가하거나 제거하려면 이 파서를 업데이트합니다.<br><br>- 원본 독립적 쿼리에서 이 함수를 사용합니다.|
| **ASimWebSession** | [imWebSession](#imwebsession) 함수와 비슷하지만 매개 변수가 지원되지 않으므로 **로그** 페이지 시간 선택기가 값을 사용하도록 강제하지 `custom` 않습니다. |- 원본과 관계없는 분석에서 원본을 추가하거나 제거하려는 경우 이러한 파서 업데이트<br><br>- 매개 변수를 사용할 계획이 없는 경우 소스에 구애받지 않는 쿼리에서 이 함수를 사용합니다.|
| **vimWebSession\<vendor\>\<product\>** | 원본별 파서가 특정 원본에 대한 정규화를 구현합니다. |- 기본으로 사용할 수 있는 정규화 파서가 없는 경우 원본에 대한 원본별 파서 추가 새 `im` 파서에 대한 참조를 포함하도록 집계 파서를 업데이트합니다. <br><br>- 구문 분석 및 정규화 문제를 해결하기 위해 원본 관련 파서를 업데이트합니다.<br><br>- 원본 관련 분석에 원본 관련 파서를 사용합니다.|
| **ASimWebSession\<vendor\>\<product\>** | 원본별 파서가 특정 원본에 대한 정규화를 구현합니다. <br><br>함수와 달리 `vim*` `ASim*` 함수는 매개 변수를 지원하지 않습니다. |- 기본으로 사용할 수 있는 정규화 파서가 없는 경우 원본에 대한 원본별 파서 추가 `ASim`새 파서에 대한 참조를 포함하도록 집계 파서를 업데이트합니다.<br><br>- 구문 분석 및 정규화 문제를 해결하기 위해 원본 관련 파서를 업데이트합니다.<br><br>- 매개 변수를 사용하지 `ASim` 않는 경우 대화형 쿼리에 원본별 파서 사용|
| | | |


[Microsoft Sentinel GitHub 리포지토리에서](https://aka.ms/DeployASIM)ASIM 파서를 배포합니다.

### <a name="add-your-own-normalized-parsers"></a>사용자 고유의 정규화된 파서 추가

웹 세션 정보 모델에 대한 사용자 지정 파서 구현 시 다음 구문을 사용하여 KQL 함수의 이름을 지정합니다.

- `vimWebSession<vendor><Product>` 매개변수 파서의 경우
- `ASimWebSession<vendor><Product>` 일반 파서의 경우

그런 다음, 또는 각각에 새 파서 를 `imWebSession` `ASimWebSession` 추가합니다.

### <a name="filtering-parser-parameters"></a>파서 매개 변수 필터링

`im`및 `vim*` 파서가 [매개 변수 필터링을 지원합니다.](normalization-about-parsers.md#optimized-parsers) 이러한 파서가 선택 사항이지만 쿼리 성능을 향상시킬 수 있습니다.

사용할 수 있는 필터링 매개 변수는 다음과 같습니다.

| 이름     | 유형      | Description |
|----------|-----------|-------------|
| **Starttime** | Datetime | 이 시간 이후에 **시작된** 웹 세션만 필터링합니다. |
| **Endtime** | Datetime | 이 시간 또는 그 이전에 **실행되기 시작한** 웹 세션만 필터링합니다. |
| **srcipaddr_has_any_ipv4_prefix** | 동적 | [원본 IP 주소 필드](network-normalization-schema.md#srcipaddr) 접두사를 나열된 값 중 하나에 있는 웹 세션만 필터링합니다. |
| **url_has_any** | 동적 | [URL 필드에](#url) 나열된 값이 있는 웹 세션만 필터링합니다. 지정되고 세션이 웹 세션이 아닌 경우 결과가 반환되지 않습니다.|  
| **httpuseragent_has_any** | 동적 | [사용자 에이전트 필드에](#httpuseragent) 나열된 값이 있는 웹 세션만 필터링합니다. 지정되고 세션이 웹 세션이 아닌 경우 결과가 반환되지 않습니다. |  
| | | |

예를 들어 지정된 도메인 이름 목록에 대한 웹 세션만 필터링하려면 다음을 사용합니다.

```kql
let torProxies=dynamic(["tor2web.org", "tor2web.com", "torlink.co",...]);
imWebSession (hurl_has_any = torProxies)
```

## <a name="schema-details"></a>스키마 세부 정보

웹 세션 정보 모델은 [OSSEM 네트워크 엔터티 스키마](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/network.md) 및 [OSSEM HTTP 엔터티 스키마](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/http.md)입니다.

업계 모범 사례를 준수하기 위해 웹 세션 스키마는 **Src** 및 **Dst** 설명자를 사용하여 필드 이름에 토큰 **Dvc를** 포함하지 않고 세션 원본 및 대상 디바이스를 식별합니다.

따라서 예를 들어 원본 디바이스 호스트 이름 및 IP 주소의 이름은 **SrcHostname** 및 **SrcIpAddr로** 각각 지정되며 **Src *Dvc* Hostname 및 Src** ***Dvc* IpAddr이** 아닙니다. 접두사 **Dvc는** 해당하는 경우 보고 또는 중간 디바이스에만 사용됩니다.

원본 및 대상 디바이스와 연결된 사용자 및 애플리케이션을 설명하는 필드는 **Src** 및 **Dst** 설명자도 사용합니다.

다른 ASIM 스키마는 일반적으로 **Dst** 대신 **Target을** 사용합니다.


### <a name="common-fields"></a>공용 필드

모든 스키마에 공통적인 필드는 [ASIM 스키마 개요](normalization-about-schemas.md#common)에 설명되어 있습니다. 다음 필드에는 웹 세션 이벤트에 대한 특정 지침이 있습니다.

| 필드               | 클래스       | 형식       |  Description        |
|---------------------|-------------|------------|--------------------|
| **EventType** | 필수 | Enumerated | 레코드에서 보고하는 작업을 설명하고 로 설정해야 `HTTPsession` 합니다. |
| **EventResult** | 필수 | Enumerated | 다음 값 중 하나로 정규화된 이벤트 결과를 설명합니다. <br> - `Success` <br> - `Partial` <br> - `Failure` <br> - `NA`(해당 사항 없음) <br><br>HTTP 세션의 경우 `Success` 는 보다 낮은 상태 코드로 정의되며 보다 높은 상태 `400` `Failure` 코드로 `400` 정의됩니다. HTTP 상태 코드 목록은 W3 Org 를 [참조하세요.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)<br><br>원본은 [EventResultDetails](#eventresultdetails)  필드에 대한 값만 제공할 수 있으며  **EventResult**  값을 얻으려면 분석해야 합니다. |
| <a name="eventresultdetails"></a>**EventResultDetails** | 선택 | 문자열 | HTTP 세션의 경우 값은 HTTP 상태 코드여야 합니다. <br><br>**참고**: 값은 이러한 값으로 정규화되어야 하는 다른 조건을 사용하여 원본 레코드에 제공될 수 있습니다. 원래 값은 **EventOriginalResultDetails** 필드에 저장되어야 합니다.|
| **EventSchema** | 필수 | String | 여기에 설명된 스키마의 이름은 `WebSession` 입니다. |
| **EventSchemaVersion**  | 필수   | String     | 스키마의 버전입니다. 여기에 설명된 스키마의 버전은 `0.1`입니다.         |
| **Dvc** 필드|        |      | 웹 세션 이벤트의 경우 디바이스 필드는 웹 세션 이벤트를 보고하는 시스템을 참조합니다.  |
| | | | |

### <a name="network-session-fields"></a>네트워크 세션 필드

HTTP 세션은 TCP/IP를 기본 네트워크 계층 세션으로 활용하는 애플리케이션 계층 세션입니다. 웹 세션 스키마는 [ASIM 네트워크 세션 스키마의](network-normalization-schema.md) 슈퍼 집합이며 모든 [네트워크 세션 필드도](network-normalization-schema.md#network-session-fields) 웹 세션 스키마에 포함됩니다.

### <a name="intermediary-device-fields"></a><a name="Intermediary"></a>중간 디바이스 필드

웹 세션 이벤트는 일반적으로 클라이언트에서 HTTP 연결을 종료하고 서버와 프록시 역할을 하는 새 연결을 시작하는 중간 디바이스에서 보고됩니다. 중간 디바이스를 나타내려면 [ASIM 네트워크 세션 스키마](network-normalization-schema.md) [중간 디바이스 필드를](network-normalization-schema.md#Intermediary) 사용합니다.


### <a name="http-session-fields"></a><a name="http-session-fields"></a>HTTP 세션 필드

다음은 웹 세션과 관련한 추가 필드입니다.

| 필드 | 클래스 | 형식 | Description |
| --- | --- | --- | --- |
| <a name="url"></a>**Url** | 필수 | String | 매개 변수를 포함한 전체 HTTP 요청 URL입니다.<br><br>예: `https://contoso.com/fo/?k=v&amp;q=u#f` |
| **UrlCategory** | 선택 | 문자열 | URL 또는 URL의 도메인 부분에 대해 정의된 그룹화입니다. 범주는 일반적으로 웹 보안 게이트웨이에서 제공되며 URL이 가리키는 사이트의 콘텐츠를 기반으로 합니다.<br><br>예: 검색 엔진, 성인, 뉴스, 광고 및 파킹된 도메인 |
| **UrlOriginal** | 선택 | 문자열 | URL이 보고 디바이스에 의해 수정되고 두 값이 모두 제공된 경우 URL의 원래 값입니다. |
| **HttpVersion** | 선택 | 문자열 | HTTP 요청 버전입니다.<br><br>예: `2.0` |
| **HttpRequestMethod** | 권장 | Enumerated | HTTP 메서드입니다. 값은 [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-4) 및 [RFC 5789](https://datatracker.ietf.org/doc/html/rfc5789#section-2)에 정의된 대로 이며 `GET` , , , 및 `HEAD` 를 `POST` `PUT` `DELETE` `CONNECT` `OPTIONS` `TRACE` `PATCH` 포함합니다.<br><br>예: `GET` |
| **HttpStatusCode** | Alias | | HTTP 상태 코드입니다. [EventResultDetails 에 대한 별칭입니다.](#eventresultdetails) |
| <a name="httpcontenttype"></a>**HttpContentType** | 선택 | 문자열 | HTTP 응답 콘텐츠 형식 헤더입니다. <br><br>**참고:** **HttpContentType** 필드에는 콘텐츠 형식과 실제 형식을 얻는 데 사용되는 인코딩과 같은 추가 매개 변수가 모두 포함될 수 있습니다.<br><br> 예: `text/html; charset=ISO-8859-4` |
| **HttpContentFormat** | 선택 | 문자열 | [HttpContentType의](#httpcontenttype) 콘텐츠 형식 부분입니다. <br><br> 예: `text/html` |
| **HttpReferrer** | 선택 | 문자열 | HTTP 참조 헤더입니다.<br><br>**참고:** ASIM은 OSSEM과 동기화되어 원래 HTTP 헤더 맞춤법이 아닌 *참조 관리자* 에 올바른 철자를 사용합니다.<br><br>예: `https://developer.mozilla.org/docs` |
| <a name="httpuseragent"></a>**HttpUserAgent** | 선택 | 문자열 | HTTP 사용자 에이전트 헤더입니다.<br><br>예:<br> `Mozilla/5.0`(Windows NT 10.0; WOW64)<br>`AppleWebKit/537.36` (Gecko와 같은 KHTML)<br>`Chrome/83.0.4103.97 Safari/537.36` |
| **UserAgent** | Alias | | 별칭을 [HttpUserAgent로](#httpuseragent) |
| **HttpRequestXff** | 선택 사항 | IP 주소 | HTTP X-Forwarded-For 헤더입니다.<br><br>예: `120.12.41.1` |
| **HttpRequestTime** | 선택 | 정수 | 해당하는 경우 서버에 요청을 보내는 데 걸린 시간(밀리초)입니다.<br><br>예: `700` |
| **HttpResponseTime** | 선택 | 정수 | 해당하는 경우 서버에서 응답을 받는 데 걸린 시간(밀리초)입니다.<br><br>예: `800` |
| **FileName** | 선택 | 문자열 | HTTP 업로드의 경우 업로드된 파일의 이름입니다. |
| **FileMD5** | 선택 사항 | MD5 | HTTP 업로드의 경우 업로드된 파일의 MD5 해시입니다.<br><br>예: `75a599802f1fa166cdadb360960b1dd0` |
| **FileSHA1** | 선택 사항 | SHA1 | HTTP 업로드의 경우 업로드된 파일의 SHA1 해시입니다.<br><br>예:<br>`d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0` |
| **FileSHA256** | 선택 사항 | SHA256 | HTTP 업로드의 경우 업로드된 파일의 SHA256 해시입니다.<br><br>예:<br>`e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **FileSHA512** | 선택 사항 | SHA512 | HTTP 업로드의 경우 업로드된 파일의 SHA512 해시입니다. |
| **FileSize** | 선택 | 정수 | HTTP 업로드의 경우 업로드된 파일의 크기(바이트)입니다. |
| **FileContentType** | 선택 | 문자열 | HTTP 업로드의 경우 업로드된 파일의 콘텐츠 형식입니다. |
| **RuleName** | 선택 | 문자열 | [DvcAction이](normalization-about-schemas.md#dvcaction) 결정된 규칙의 이름 또는 ID입니다.<br><br> 예: `AnyAnyDrop` |
| **RuleNumber** | 선택 | 정수 | [DvcAction이](normalization-about-schemas.md#dvcaction) 결정된 규칙의 수입니다.<br><br>예: `23` |
| **규칙** | 필수 | String | `NetworkRuleName` 또는 `NetworkRuleNumber` |
| **ThreatId** | 선택 | 문자열 | 웹 세션에서 식별된 위협 또는 맬웨어의 ID입니다.<br><br>예: `Tr.124` |
| **ThreatName** | 선택 | 문자열 | 웹 세션에서 식별된 위협 또는 맬웨어의 이름입니다.<br><br>예: `EICAR Test File` |
| **ThreatCategory** | 선택 | 문자열 | 웹 세션에서 식별된 위협 또는 맬웨어의 범주입니다.<br><br>예: `Trojan` |
| **ThreatRiskLevel** | 선택 | 정수 | 세션과 연결된 위험 수준입니다. 수준은 **0에서 100** 사이의 숫자여야 합니다.<br><br>**참고:** 값은 이 크기 조정으로 정규화되어야 하는 다른 배율로 원본 레코드에 제공될 수 있습니다. 원래 값은 [ThreatRiskLevelOriginal](#threatriskleveloriginal)에 저장되어야 합니다. |
| <a name="threatriskleveloriginal"></a>**ThreatRiskLevelOriginal** | 선택 | 문자열 | 보고 디바이스에서 보고한 위험 수준입니다. |
| | | | |

### <a name="other-fields"></a>기타 필드

이벤트가 웹 세션의 엔드포인트 중 하나에서 보고되는 경우 세션을 시작하거나 종료한 프로세스에 대한 정보가 포함될 수 있습니다. 이러한 경우 [ASIM 프로세스 이벤트 스키마를](process-events-normalization-schema.md) 사용하여 이 정보를 정규화합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Microsoft Sentinel의 정규화](normalization.md)
- [고급 SIEM 정보 모델 스키마](normalization-about-schemas.md)
- [고급 SIEM 정보 모델 파서](normalization-about-parsers.md)
- [고급 SIEM 정보 모델 콘텐츠](normalization-content.md)

