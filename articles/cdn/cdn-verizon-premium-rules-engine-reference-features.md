---
title: Verizon Premium의 Azure CDN 규칙 엔진 기능
description: Verizon Premium의 Azure CDN 규칙 엔진 기능에 대한 참조 설명서입니다.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: duau
ms.openlocfilehash: c1f77903378cdced27497893638b03a03279adfb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423340"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Verizon Premium의 Azure CDN 규칙 엔진 기능

이 문서에서는 Azure CDN(Content Delivery Network) [규칙 엔진](cdn-verizon-premium-rules-engine.md)에 사용할 수 있는 기능에 대해 자세히 설명합니다.

규칙의 세 번째 부분은 기능을 다루고 있습니다. 기능은 일치 조건 집합으로 식별되는 요청 유형에 적용할 작업 유형을 정의합니다.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Verizon Premium의 Azure CDN 규칙 엔진 기능 참조

사용 가능한 기능 유형은 다음과 같습니다.

* [Access](#access)
* [캐싱](#caching)
* [설명](#comment)
* [헤더](#headers)
* [로그](#logs)
* [Optimize](#optimize)
* [원본](#origin)
* [전문](#specialty)
* [URL](#url)
* [웹 애플리케이션 방화벽](#waf)

### <a name="access"></a><a name="access"></a>Access

이러한 기능은 콘텐츠에 대한 액세스를 제어하도록 설계되었습니다.

| Name       | 목적                                                           |
|------------|-------------------------------------------------------------------|
| [액세스 거부(403)](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | 모든 요청이 403 사용 권한 없음 응답으로 거부되는지 여부를 결정합니다. |
| [토큰 인증](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | 요청에 토큰 기반 인증을 적용할지 여부를 결정합니다. |
| [토큰 인증 거부 코드](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | 토큰 기반 인증으로 인해 요청이 거부되는 경우 사용자에게 반환할 응답 유형을 결정합니다. |
| [토큰 인증 URL 대/소문자 무시](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | 토큰 기반 인증에서 URL 비교 시 대/소문자를 구분할지 결정합니다. |
| [토큰 인증 매개 변수](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | 토큰 기반 인증 쿼리 문자열 매개 변수 이름을 바꿔야 하는지 여부를 결정합니다. |

**[맨 위로 돌아가기](#top)**

### <a name="caching"></a><a name="caching"></a>캐싱

이러한 기능은 콘텐츠가 캐시되는 시기와 방식을 사용자 지정하기 위해 설계되었습니다.

| Name       | 목적                                                           |
|------------|-------------------------------------------------------------------|
| [대역폭 매개 변수](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | 대역폭 제한 매개 변수(예: ec_rate 및 ec_prebuf)를 활성화할지 여부를 결정합니다. |
| [대역폭 제한](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | 에지 서버에서 제공하는 응답에 대한 대역폭을 제한합니다. |
| [바이패스 캐시](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | 요청에서 캐싱 기술을 활용할 수 있는지 여부를 결정합니다. |
| [Cache-Control 헤더 처리](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  외부 Max-Age 기능이 활성 상태일 때 에지 서버에 의한 Cache-Control 헤더의 생성을 제어합니다. |
| [Cache-Key 쿼리 문자열](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | **cache-key** _에서 요청과 관련된 쿼리 문자열 매개 변수를 포함 또는 제외할지 여부를 결정합니다. <br> __캐싱을 위해 자산을 고유하게 식별하는 상대 경로입니다.  에지 서버는 캐시된 콘텐츠를 확인할 때 이 상대 경로를 사용합니다.  기본적으로 캐시 키는 쿼리 문자열 매개 변수를 포함하지 않습니다. |
| [Cache-Key 다시 쓰기](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | 요청과 관련된 cache-key를 다시 씁니다. |
| [전체 캐시 채우기](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | 요청 결과, 에지 서버에서 캐시가 부분적으로 누락된 경우 수행할 작업을 결정합니다. |
| [압축 파일 형식](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | 서버에서 압축할 파일 형식을 정의합니다. | 
| [기본 내부 Max-Age](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | 에지 서버에서 원본 서버 캐시 유효성 재검사를 위한 기본 max-age 간격을 결정합니다. |
| [만료 헤더 처리](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | 외부 Max-Age 기능이 활성 상태일 때 에지 서버에 의한 만료 헤더의 생성을 제어합니다. |
| [외부 Max-Age](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | 브라우저에서 에지 서버 캐시 유효성 재검사를 위한 max-age 간격을 결정합니다. |
| [강제 내부 Max-Age](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | 에지 서버에서 원본 서버 캐시 유효성 재검사를 위한 max-age 간격을 결정합니다. |
| [H.264 지원(HTTP 점진적 다운로드)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | 콘텐츠를 스트리밍하는 데 사용할 수 있는 H.264 파일 형식의 유형을 결정합니다. |
| [H.264 지원 비디오 찾기 매개 변수](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | HTTP 프로그레시브 다운로드를 사용할 때 H.264 미디어를 통한 검색을 제어하는 매개 변수에 할당 된 이름을 재정의합니다. |
| [No-Cache 요청 부여](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | HTTP 클라이언트의 no-cache 요청을 원본 서버에 전달할지 여부를 결정합니다. |
| [원본 No-Cache 무시](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | CDN이 원본 서버에서 제공되는 특정 지시문을 무시할지 여부를 결정합니다. |
| [적절하지 않은 범위 무시](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | 요청에서 416 요청한 범위가 적절하지 않음 상태 코드를 생성하는 경우 클라이언트로 반환할 응답을 결정합니다. |
| [내부 Max-Stale](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | 에지 서버가 원본 서버로 캐시된 자산의 유효성 재검사를 할 수 없는 경우 에지 서버에서 캐시된 자산이 정상 만료 시간을 지나 얼마나 오래 제공될 수 있는지를 제어합니다. |
| [부분 캐시 공유](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | 요청에서 부분적으로 캐시된 콘텐츠를 생성할 수 있는지 여부를 결정합니다. |
| [캐시된 콘텐츠 사전 유효성 검사](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | TTL이 만료되기 전에 캐시된 콘텐츠로 미리 유효성 재검사할 수 있는지 여부를 결정합니다. |
| [0바이트 캐시 파일 새로 고침](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | 0바이트 캐시 자산에 대한 HTTP 클라이언트 요청이 에지 서버에 의해 처리되는 방식을 결정합니다. |
| [캐시 가능한 상태 코드 집합](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | 캐시된 콘텐츠가 발생할 수 있는 상태 코드 집합을 정의합니다. |
| [오류 시 오래된 콘텐츠 배달](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | 캐시 유효성 재검사 중이나 고객 원본 서버에서 요청된 콘텐츠를 검색할 때 만료되고 캐시된 콘텐츠를 배달할지 여부를 결정합니다. | 
| [유효성 재검사 중 기한 경과](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | 유효성 재검사를 수행하는 동안 에지 서버가 오래된 클라이언트를 요청자에게 제공하도록 하여 성능을 개선합니다. |

**[맨 위로 돌아가기](#top)**

### <a name="comment"></a><a name="comment"></a>주석

주석 기능을 통해 규칙 내에 메모를 추가할 수 있습니다.

**[맨 위로 돌아가기](#top)**

### <a name="headers"></a><a name="headers"></a>헤더

이러한 기능은 요청자 또는 응답에서 헤더를 추가, 수정 또는 삭제하도록 설계되었습니다.

| Name       | 목적                                                           |
|------------|-------------------------------------------------------------------|
| [Age 응답 헤더](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | Age 응답 헤더를 요청자에게 전송되는 응답에 포함할지 결정합니다. |
| [디버그 캐시 응답 헤더](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | 응답에 요청된 자산에 대한 캐시 정책 정보를 제공하는 [X-EC-Debug 응답 헤더](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm)를 포함할 수 있는지 여부를 결정합니다. |
| [클라이언트 요청 헤더 수정](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | 요청에서 헤더를 덮어쓰기, 추가 또는 삭제합니다. |
| [클라이언트 응답 헤더 수정](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | 응답에서 헤더를 덮어쓰기, 추가 또는 삭제합니다. |
| [클라이언트 IP 사용자 지정 헤더 설정](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | 클라이언트를 요청하는 IP 주소가 사용자 지정 요청 헤더로 요청에 추가되도록 합니다. |

**[맨 위로 돌아가기](#top)**

### <a name="logs"></a><a name="logs"></a>로그

이러한 기능은 원시 로그 파일에 저장된 데이터를 사용자 지정하도록 설계되었습니다.

| Name       | 목적                                                           |
|------------|-------------------------------------------------------------------|
| [사용자 지정 로그 필드 1](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | 원시 로그 파일의 사용자 지정 로그 필드에 할당할 콘텐츠와 형식을 결정합니다. |
| [로그 쿼리 문자열](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | 액세스 로그에 쿼리 문자열을 URL과 함께 저장할지 여부를 결정합니다. |

**[맨 위로 돌아가기](#top)**

### <a name="optimize"></a><a name="optimize"></a>최적화

이러한 기능은 요청에서 에지 최적화 프로그램이 제공하는 최적화를 수행할지 여부를 결정합니다.

| Name       | 목적                                                           |
|------------|-------------------------------------------------------------------|
| [에지 최적화 프로그램](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | 에지 최적화 프로그램을 요청에 적용할 수 있는지 여부를 결정합니다. |
| [에지 최적화 프로그램 - 구성 인스턴스화](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | 사이트와 관련된 에지 최적화 프로그램 구성을 인스턴스화 또는 활성화합니다. |

**[맨 위로 돌아가기](#top)**

### <a name="origin"></a><a name="origin"></a>원본

이러한 기능은 CDN이 원본 서버와 통신하는 방법을 제어하도록 설계되었습니다.

| Name       | 목적                                                           |
|------------|-------------------------------------------------------------------|
| [최대 연결 유지 요청](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | 연결이 닫히기 전에 연결을 유지할 최대 요청 수를 정의합니다. |
| [프록시 특별 헤더](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | 에지 서버에서 원본 서버로 전달할 [CDN 전용 요청 헤더](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders)의 집합을 정의합니다. |

**[맨 위로 돌아가기](#top)**

### <a name="specialty"></a><a name="specialty"></a>전문

이러한 기능은 고급 사용자만 사용해야 하는 고급 기능을 제공합니다.

| Name       | 목적                                                           |
|------------|-------------------------------------------------------------------|
| [캐시 가능한 HTTP 메서드](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | 네트워크에서 캐시할 수 있는 추가 HTTP 메서드 집합을 결정합니다. |
| [캐시 가능한 요청 본문 크기](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | POST 응답을 캐시할 수 있는지 여부를 결정하는 임계값을 정의합니다. |
| [QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | 클라이언트가 CDN 서비스가 QUIC를 지원한다는 알림을 받을지 여부를 결정합니다. |
| [스트리밍 최적화](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | 캐싱 구성을 조정하여 라이브 스트림의 성능을 최적화하고 원본 서버의 부하를 줄입니다. |
| [User 변수](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | 맞춤식 트래픽 처리 솔루션에 전달되는 사용자 정의 변수에 값을 할당합니다. |

**[맨 위로 돌아가기](#top)**

### <a name="url"></a><a name="url"></a>URL

이러한 기능을 통해 요청을 다른 URL로 리디렉션하거나 다시 작성할 수 있습니다.

| Name       | 목적                                                           |
|------------|-------------------------------------------------------------------|
| [리디렉션 추적](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | 고객 원본 서버에서 반환된 Location 헤더에 정의된 호스트 이름으로 요청을 리디렉션할 수 있는지 여부를 결정합니다. |
| [URL 리디렉션](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | Location 헤더를 통해 요청을 리디렉션합니다. |
| [URL 재작성](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | 요청 URL을 다시 씁니다. |

**[맨 위로 돌아가기](#top)**

### <a name="web-application-firewall"></a><a name="waf">웹 애플리케이션 방화벽</a>

[웹 애플리케이션 방화벽](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm) 기능은 웹 애플리케이션 방화벽으로 요청을 보호할지 여부를 결정합니다.

**[맨 위로 돌아가기](#top)**

가장 최근 기능에 대해서는 [Verizon 규칙 엔진 설명서](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [규칙 엔진 참조](cdn-verizon-premium-rules-engine-reference.md)
- [규칙 엔진 조건식](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [규칙 엔진 일치 조건](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [규칙 엔진을 사용하여 HTTP 동작 재정의](cdn-verizon-premium-rules-engine.md)
- [Azure CDN 개요](cdn-overview.md)
