---
title: Verizon Premium의 Azure CDN 규칙 엔진 일치 조건
description: Verizon Premium의 Azure Content Delivery Network 규칙 엔진 일치 조건에 대한 참조 설명서
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: duau
ms.openlocfilehash: 61576e21bbf8fa260a5003bee62ac40b63d342c7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446736"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Verizon Premium의 Azure CDN 규칙 엔진 일치 조건

이 문서에서는 Verizon Premium의 Azure CDN(Content Delivery Network) [규칙 엔진](cdn-verizon-premium-rules-engine.md)에 사용할 수 있는 일치 조건에 대해 자세히 설명합니다.

규칙의 두 번째 부분이 일치 조건입니다. 일치 조건은 기능 집합에 대해 수행할 특정 요청 유형을 식별합니다.

예를 들어 다음을 수행하는 데 일치 조건을 사용할 수 있습니다.

- 특정 위치에서 콘텐츠에 대한 필터 요청
- 특정 IP 주소 또는 국가/지역에서 생성된 요청 필터링
- 헤더 정보에 의한 필터 요청

## <a name="match-conditions"></a><a name="top"></a>

* [Always](#always)
* [디바이스](#device)
* [위치](#location)
* [원본](#origin)
* [요청](#request)
* [URL](#url)

### <a name="always"></a><a name="always"></a>항상

[항상 일치 조건](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm)은 모든 요청에 기본 기능 세트를 적용하도록 설계되었습니다.

### <a name="device"></a><a name="device"></a>디바이스

이러한 일치 조건은 클라이언트의 사용자 에이전트에 따라 요청을 식별하도록 설계되었습니다.

| Name       | 목적                                                           |
|------------|-------------------------------------------------------------------|
| 브랜드 이름 | 디바이스의 브랜드 이름이 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([브랜드 이름 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm)) <br> **-** 정규식([브랜드 이름 정규식](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm)) <br> **-** 특정 패턴([브랜드 이름 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm)) |
| 디바이스 OS | 디바이스의 OS가 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([디바이스 OS 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm)) <br> **-** 정규식([디바이스 OS 정규식](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm)) <br> **-** 특정 패턴([디바이스 OS 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm)) |
| 디바이스 OS 버전 | 디바이스의 OS 버전이 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([디바이스 OS 버전 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm)) <br> **-** 정규식([디바이스 OS 버전 정규식](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm)) <br> **-** 특정 패턴([ 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm)) |
| [이중 방향 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | 디바이스에서 이중 방향을 지원하는지 여부에 따라 요청을 식별합니다. |
| HTML 기본 DTD | 디바이스의 HTML 기본 설정 DTD가 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([HTML 기본 설정 DTD 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm)) <br> **-** 정규식([HTML 기본 설정 DTD 정규식](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm)) <br> **-** 특정 패턴([HTML 기본 설정 DTD 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm)) |
| [이미지 인라인 처리 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | 디바이스에서 Base64 인코딩 이미지를 지원하는지 여부에 따라 요청을 식별합니다. |
| [Android 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | 디바이스에서 Android OS를 사용하는지 여부에 따라 요청을 식별합니다. |
| [앱 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | 네이티브 애플리케이션에서 콘텐츠를 요청했는지 여부에 따라 요청을 식별합니다. |
| [전체 데스크톱 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | 디바이스에서 전체 데스크톱 환경을 제공하는지 여부에 따라 요청을 식별합니다. |
| [IOS 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | 디바이스에서 iOS를 사용하는지 여부에 따라 요청을 식별합니다. |
| [로봇 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | 디바이스가 자동화된 HTTP 클라이언트(예: 로봇 크롤러)로 간주되는지 여부에 따라 요청을 식별합니다. |
| [스마트 TV 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | 디바이스가 스마트 TV인지 여부에 따라 요청을 식별합니다. |
| [스마트폰 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | 디바이스가 스마트폰인지 여부에 따라 요청을 식별합니다.
| [태블릿 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | 디바이스가 태블릿인지 여부에 따라 요청을 식별합니다. |
| [터치 스크린 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | 디바이스의 기본 포인팅 디바이스가 터치 스크린인지 여부에 따라 요청을 식별합니다. |
| [Windows Phone 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | 디바이스가 Windows Mobile 6.5/Windows Phone 7 이상인지 여부에 따라 요청을 식별합니다. |
| [무선 디바이스 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | 디바이스가 무선인지 여부에 따라 요청을 식별합니다. 
| 마케팅 이름 | 디바이스의 마케팅 이름이 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([마케팅 이름 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm)) <br> **-** 정규식([마케팅 이름 정규식](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm)) <br> **-** 특정 패턴([마케팅 이름 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm)) |
| 모바일 브라우저 | 디바이스의 브라우저가 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([모바일 브라우저 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm)) <br> **-** 정규식([모바일 브라우저 정규식](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm)) <br> **-** 특정 패턴([모바일 브라우저 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm)) |
| 모바일 브라우저 버전 | 디바이스의 브라우저 버전이 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([모바일 브라우저 버전 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm)) <br> **-** 정규식([모바일 브라우저 버전 정규식](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm)) <br> **-** 특정 패턴([모바일 브라우저 버전 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm)) |
| 모델 이름 | 디바이스의 모델 이름이 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([모델 이름 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm)) <br> **-** 정규식([모델 이름 정규식](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm)) <br> **-** 특정 패턴([모델 이름 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm)) |
| [점진적 다운로드 여부](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | 디바이스가 점진적 다운로드를 지원하는지 여부에 따라 요청을 식별합니다. |
| 출시 날짜 | 디바이스의 릴리스 날짜가 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([릴리스 날짜 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm)) <br> **-** 정규식([릴리스 날짜 정규식](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm)) <br> **-** 특정 패턴([릴리스 날짜 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm)) |
| [해상도 높이](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | 디바이스의 높이로 요청을 식별합니다. |
| [해상도 너비](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | 디바이스의 너비로 요청을 식별합니다. |

**[맨 위로 이동](#top)**

### <a name="location"></a><a name="location"></a>Location

이 일치 조건은 요청자의 위치에 따라 요청을 식별하도록 설계되었습니다.

| Name       | 목적                                                           |
|------------|-------------------------------------------------------------------|
| [AS 숫자](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | 특정 네트워크에서 발생하는 요청을 식별합니다. |
| 구/군/시 이름 | 구/군/시 이름이 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([구/군/시 이름 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm)) <br> **-** 정규식([구/군/시 이름 정규식](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm)) |
| [Continent](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | 지정된 대륙에서 발생하는 요청을 식별합니다. |
| [국가](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | 지정된 국가에서 발생하는 요청을 식별합니다. |
| [DMA 코드](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | 지정된 대도시권(지정된 시장 지역)에서 시작되는 요청을 식별합니다. |
| [Latitude](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | 지정된 위도에서 발생하는 요청을 식별합니다. |
| [경도](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | 지정된 경도에서 발생하는 요청을 식별합니다. |
| [Metro 코드](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | 지정된 대도시권(지정된 시장 지역)에서 시작되는 요청을 식별합니다. |
| [우편 번호](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | 지정된 우편 번호에서 발생하는 요청을 식별합니다. |
| [지역 코드](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | 지정된 지역에서 발생하는 요청을 식별합니다. |

> [!NOTE]
> **Metro 코드 또는 DMA 코드를 사용해야 하나요?** <br>
해당 일치 조건 모두 기능이 동일합니다. 그러나 Metro 코드 일치 조건을 사용하여 DMA에 따라 요청을 식별하는 것이 좋습니다.

**[맨 위로 이동](#top)**

### <a name="origin"></a><a name="origin"></a>원본

이 일치 조건은 CDN 스토리지 또는 고객 원본 서버를 가리키는 요청을 식별하도록 설계되었습니다.

| Name       | 목적                                                           |
|------------|-------------------------------------------------------------------|
| [CDN 원본](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | CDN 스토리지에 저장된 콘텐츠에 대한 요청을 식별합니다. |
| [고객 원본](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | 특정 고객 원본 서버에 저장된 콘텐츠에 대한 요청을 식별합니다. |

**[맨 위로 이동](#top)**

### <a name="request"></a><a name="request"></a>요청

이 일치 조건은 해당 속성에 따라 요청을 식별하도록 설계되었습니다.

| Name              | 목적                                                                |
|-------------------|------------------------------------------------------------------------|
| [클라이언트 IP 주소](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | 특정 IP 주소에서 발생하는 요청을 식별합니다. |
| 쿠키 매개 변수  | 다음과 일치하는 쿠키를 포함하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([쿠키 매개 변수 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm)) <br> **-** 정규식([쿠키 매개 변수 정규식](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm) <br> **-** 특정 패턴([쿠키 매개 변수 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm)) |
| [Edge CNAME](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | 특정 에지 CNAME을 가리키는 요청을 식별합니다. |
| 참조 도메인 | 다음과 일치하는 호스트 이름이 참조되었는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([도메인 리터럴 참조](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm)) <br> **-** 특정 패턴([도메인 와일드카드 참조](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm)) |
| 요청 헤더 | 다음과 일치하는 헤더를 포함하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([요청 헤더 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm)) <br> **-** 정규식([요청 헤더 정규식](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm)) <br> **-** 특정 패턴([요청 헤더 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm)) |
| [요청 메서드](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | 해당 HTTP 메서드로 요청을 식별합니다. |
| [요청 스키마](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | 해당 HTTP 프로토콜로 요청을 식별합니다. |

**[맨 위로 이동](#top)**

### <a name="url"></a><a name="url"></a>URL

| Name              | 목적                                                                |
|-------------------|------------------------------------------------------------------------|
| 맵의 경로와 | 파일 이름을 포함하여 상대 경로가 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([URL 경로 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm)) <br> **-** 정규식([URL 경로 정규식](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm)) <br> **-** 특정 패턴([URL 경로 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm)) |
| URL 경로 디렉터리 | 상대 경로가 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([URL 경로 디렉터리 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm)) <br> **-** 특정 패턴([URL 경로 디렉터리 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm)) |
| URL 경로 확장 | 파일 확장명이 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([URL 경로 확장 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm)) <br> **-** 특정 패턴([URL 경로 확장 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm)) |
| URL 경로 파일 이름 | 파일 이름이 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([URL 경로 파일 이름 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm)) <br> **-** 특정 패턴([URL 경로 파일 이름 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm)) |
| URL 쿼리 | 쿼리 문자열이 다음과 일치하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([URL 쿼리 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm)) <br> **-** 정규식([URL 쿼리 정규식](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm)) <br> **-** 특정 패턴([URL 쿼리 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm)) |
| URL 쿼리 매개 변수 | 다음과 일치하는 값으로 지정된 쿼리 문자열 매개 변수를 포함하는지 여부에 따라 요청을 식별합니다. <br> **-** 특정 값([URL 쿼리 매개 변수 리터럴](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm)) <br> **-** 특정 패턴([URL 쿼리 매개 변수 와일드카드](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm)) |

**[맨 위로 이동](#top)**

가장 최근의 일치 조건은 [Verizon 규칙 엔진 설명서](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Content Delivery Network 개요](cdn-overview.md)
- [규칙 엔진 참조](cdn-verizon-premium-rules-engine-reference.md)
- [규칙 엔진 조건식](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [규칙 엔진 기능](cdn-verizon-premium-rules-engine-reference-features.md)
- [규칙 엔진을 사용하여 기본 HTTP 동작 재정의](cdn-verizon-premium-rules-engine.md)
