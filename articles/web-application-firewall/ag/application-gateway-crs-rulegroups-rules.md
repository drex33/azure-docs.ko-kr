---
title: CRS 규칙 그룹 및 규칙
titleSuffix: Azure Web Application Firewall
description: 이 페이지는 웹 애플리케이션 방화벽 CRS 규칙 그룹 및 규칙에 대한 정보를 제공합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 07/06/2021
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 9aa4277c3d13419a4083193c07d807decea21c59
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566461"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>웹 애플리케이션 방화벽 CRS 규칙 그룹 및 규칙

Application Gateway WAF(웹 애플리케이션 방화벽)는 일반적인 취약점 및 악용 사례로부터 웹 애플리케이션을 보호합니다. 이러한 보호는 OWASP 핵심 규칙 집합 3.2 3.1, 3.0 또는 2.2.9를 기반으로 정의된 규칙을 통해 수행됩니다. 이러한 규칙은 규칙별로 해제할 수 있습니다. 이 문서에는 현재 제공되는 규칙 및 규칙 집합이 포함되어 있습니다.

## <a name="core-rule-sets"></a>핵심 규칙 집합

Application Gateway WAF는 기본적으로 CRS 3.0으로 미리 구성되어 있습니다. 그러나 대신 CRS 3.2, 3.1 또는 2.2.9를 사용하도록 선택할 수 있습니다.
 

CRS 3.2(공개 미리 보기)는 Java 감염, 초기 파일 업로드 검사 세트,, 고정 가양성 등을 방어하는 새로운 엔진과 새로운 규칙 세트를 제공합니다. 

CRS 3.1은 CRS 3.0 및 2.2.9에 비해 가양성을 줄입니다. [필요에 맞게 규칙을 사용자 지정](application-gateway-customize-waf-rules-portal.md)할 수도 있습니다.

> [!div class="mx-imgBorder"]
> ![규칙 관리](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

WAF는 다음 웹 취약성으로부터 보호합니다.

- SQL 삽입 공격
- 사이트 간 스크립팅 공격
- 명령 삽입, HTTP 요청 스머글링, HTTP 응답 분할, 원격 파일 포함 등의 기타 일반적인 공격
- HTTP 프로토콜 위반
- 호스트 사용자-에이전트 및 Accept 헤더 누락 같은 HTTP 프로토콜 이상
- 봇, 크롤러 및 스캐너
- 일반적인 애플리케이션 구성 오류(예: Apache 및 IIS)

### <a name="owasp-crs-32-public-preview"></a>OWASP CRS 3.2(공개 미리 보기)

CRS 3.2에는 다음 표와 같이 13개의 규칙 그룹이 포함되어 있습니다. 각 그룹은 비활성화할 수 있는 여러 개의 규칙을 포함합니다.

> [!NOTE]
> CRS 3.2는 WAF_v2 SKU에서만 사용할 수 있습니다.

|규칙 그룹|설명|
|---|---|
|**[일반](#general-32)**|일반 그룹|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-32)**|잠금 메서드(PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-32)**|포트 및 환경 스캐너로부터 보호|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](#crs920-32)**|프로토콜 및 인코딩 문제로부터 보호|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-32)**|헤더 삽입, 요청 스머글링 및 응답 분할로부터 보호|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-32)**|파일 및 경로 공격으로부터 보호|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-32)**|RFI(원격 파일 포함) 공격으로부터 보호|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-32)**|원격 코드 실행 공격으로부터 보호|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-32)**|PHP 삽입 공격으로부터 보호|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-32)**|사이트 간 스크립팅 공격으로부터 보호|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-32)**|SQL 삽입 공격으로부터 보호|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-32)**|세션 고정 공격으로부터 보호|
|**[REQUEST-944-APPLICATION-ATTACK-SESSION-JAVA](#crs944-32)**|JAVA 공격으로부터 보호|


### <a name="owasp-crs-31"></a>OWASP CRS 3.1

CRS 3.1에는 다음 표와 같이 13개의 규칙 그룹이 포함되어 있습니다. 각 그룹은 비활성화할 수 있는 여러 개의 규칙을 포함합니다.

> [!NOTE]
> CRS 3.1은 WAF_v2 SKU에서만 사용할 수 있습니다.

|규칙 그룹|설명|
|---|---|
|**[일반](#general-31)**|일반 그룹|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|잠금 메서드(PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|포트 및 환경 스캐너로부터 보호|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](#crs920-31)**|프로토콜 및 인코딩 문제로부터 보호|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|헤더 삽입, 요청 스머글링 및 응답 분할로부터 보호|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|파일 및 경로 공격으로부터 보호|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|RFI(원격 파일 포함) 공격으로부터 보호|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|원격 코드 실행 공격으로부터 보호|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|PHP 삽입 공격으로부터 보호|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|사이트 간 스크립팅 공격으로부터 보호|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|SQL 삽입 공격으로부터 보호|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|세션 고정 공격으로부터 보호|
|**[REQUEST-944-APPLICATION-ATTACK-SESSION-JAVA](#crs944-31)**|JAVA 공격으로부터 보호|

### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0에는 다음 표와 같이 12개의 규칙 그룹이 포함되어 있습니다. 각 그룹은 비활성화할 수 있는 여러 개의 규칙을 포함합니다.

|규칙 그룹|설명|
|---|---|
|**[일반](#general-30)**|일반 그룹|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|잠금 메서드(PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|포트 및 환경 스캐너로부터 보호|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](#crs920-30)**|프로토콜 및 인코딩 문제로부터 보호|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|헤더 삽입, 요청 스머글링 및 응답 분할로부터 보호|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|파일 및 경로 공격으로부터 보호|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|RFI(원격 파일 포함) 공격으로부터 보호|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|원격 코드 실행 공격으로부터 보호|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|PHP 삽입 공격으로부터 보호|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|사이트 간 스크립팅 공격으로부터 보호|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|SQL 삽입 공격으로부터 보호|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|세션 고정 공격으로부터 보호|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9에는 다음 표와 같이 10개의 규칙 그룹이 포함되어 있습니다. 각 그룹은 비활성화할 수 있는 여러 개의 규칙을 포함합니다.

|규칙 그룹|설명|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|프로토콜 위반(요청 본문에 잘못된 문자 또는 GET)으로부터 보호|
|**[crs_21_protocol_anomalies](#crs21)**|잘못된 헤더 정보로부터 보호|
|**[crs_23_request_limits](#crs23)**|한계를 초과하는 인수 또는 파일로부터 보호|
|**[crs_30_http_policy](#crs30)**|제한된 메서드, 헤더 및 파일 유형으로부터 보호|
|**[crs_35_bad_robots](#crs35)**|웹 크롤러가 및 스캐너로부터 보호|
|**[crs_40_generic_attacks](#crs40)**|일반 공격(세션 고정, 원격 파일 포함, PHP 삽입 등)으로부터 보호|
|**[crs_41_sql_injection_attacks](#crs41sql)**|SQL 삽입 공격으로부터 보호|
|**[crs_41_xss_attacks](#crs41xss)**|사이트 간 스크립팅 공격으로부터 보호|
|**[crs_42_tight_security](#crs42)**|경로 순회 공격으로부터 보호|
|**[crs_45_trojans](#crs45)**|백도어 트로이 목마로부터 보호|

다음 규칙 그룹 및 규칙은 Application Gateway에서 웹 애플리케이션 방화벽을 사용할 때 사용할 수 있습니다.

# <a name="owasp-32-public-preview"></a>[OWASP 3.2(공개 미리 보기)](#tab/owasp32)

## <a name="32-rule-sets"></a><a name="owasp32"></a> 3.2 규칙 집합

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-32"></a> <p x-ms-format-detection="none">일반</p>
|RuleId|설명|
|---|---|
|200004|가능한 다중 파트 일치하지 않는 경계.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-32"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>
|RuleId|설명|
|---|---|
|911100|정책에서 허용하지 않는 메서드|

### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-32"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>
|RuleId|설명|
|---|---|
|913100|보안 스캐너와 연결된 사용자-에이전트 발견|
|913101|스크립팅/일반 HTTP 클라이언트와 연결된 사용자-에이전트 발견|
|913102|웹 크롤러/봇과 연결된 사용자-에이전트 발견|
|913110|보안 스캐너와 연결된 요청 헤더 발견|
|913120|보안 스캐너와 연결된 요청 파일 이름/인수 발견|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-32"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>
|RuleId|설명|
|---|---|
|920100|잘못된 HTTP 요청 줄|
|920120|다중 파트/양식 데이터 바이패스 시도|
|920121|다중 파트/양식 데이터 바이패스 시도|
|920160|콘텐츠-길이 HTTP 헤더가 숫자가 아닙니다.|
|920170|본문 콘텐츠를 통한 GET 또는 HEAD 요청.|
|920171|분할 전송 인코딩을 통한 GET 또는 HEAD 요청.|
|920180|POST 요청에 콘텐츠-길이 헤더가 없습니다.|
|920190|범위: 마지막 바이트 값이 잘못되었습니다.|
|920200|범위: 필드가 너무 많음(6개 이상)|
|920201|범위: pdf 요청에 대한 필드가 너무 많음(35개 이상)|
|920202|범위: pdf 요청에 대한 필드가 너무 많음(6개 이상)|
|920210|여러 개의 충돌하는 연결 헤더 데이터가 발견되었습니다.|
|920220|URL Encoding 남용 공격 시도|
|920230|여러 URL Encoding 감지됨|
|920240|URL Encoding 남용 공격 시도|
|920250|UTF8 Encoding 남용 공격 시도|
|920260|유니코드 전자/반자 남용 공격 시도|
|920270|요청에 잘못된 문자(null 문자)|
|920271|요청에 잘못된 문자(인쇄할 수 없는 문자)|
|920272|요청에 잘못된 문자(ascii 127 미만의 인쇄 가능한 문자 이외의)|
|920273|요청에 잘못된 문자(매우 엄격한 집합 이외의)|
|920274|요청 헤더에 잘못된 문자(매우 엄격한 집합 이외의)|
|920280|호스트 헤더가 누락된 요청|
|920290|빈 호스트 헤더|
|920310|요청에 빈 Accept 헤더가 있음|
|920311|요청에 빈 Accept 헤더가 있음|
|920320|사용자 에이전트 헤더 누락|
|920330|빈 사용자 에이전트 헤더|
|920340|콘텐츠가 있지만 Content-Type 헤더가 누락된 요청|
|920341|콘텐츠가 있는 요청은 Content-Type 헤더 필요|
|920350|호스트 헤더가 숫자 IP 주소|
|920420|요청 콘텐츠 유형이 정책에서 허용되지 않음|
|920430|HTTP 프로토콜 버전이 정책에서 허용되지 않음|
|920440|URL 파일 확장명이 정책에서 허용되지 않음|
|920450|HTTP 헤더가 정책에 의해 제한됨(%{MATCHED_VAR})|
|920460|비정상적인 이스케이프 문자|
|920470|잘못된 Content-Type 헤더|
|920480|Content-Type 헤더 내에서 charset 매개 변수 제한|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-32"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|설명|
|---|---|
|921110|HTTP 요청 밀반입 공격|
|921120|HTTP 응답 분할 공격|
|921130|HTTP 응답 분할 공격|
|921140|헤더를 통한 HTTP 헤더 삽입 공격|
|921150|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 발견)|
|921151|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 발견)|
|921160|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 및 헤더-이름 발견)|
|921170|HTTP 매개 변수 오염|
|921180|HTTP 매개 변수 오염(%{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-32"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>
|RuleId|설명|
|---|---|
|930100|경로 탐색 공격(/../)|
|930110|경로 탐색 공격(/../)|
|930120|OS 파일 액세스 시도|
|930130|제한된 파일 액세스 시도|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-32"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>
|RuleId|설명|
|---|---|
|931100|가능한 RFI(원격 파일 포함) 공격: IP 주소를 사용하는 URL 매개 변수|
|931110|가능한 RFI(원격 파일 포함) 공격: 일반 RFI 취약 매개 변수 이름이 URL 페이로드에 사용됨|
|931120|가능한 RFI(원격 파일 포함) 공격: URL 페이로드가 후행 물음표 문자(?)에 사용됨|
|931130|가능한 RFI(원격 파일 포함) 공격 = 오프-도메인 참조/링크|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-32"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>
|RuleId|설명|
|---|---|
|932100|원격 명령 실행: Unix 명령 삽입|
|932105|원격 명령 실행: Unix 명령 삽입|
|932106|원격 명령 실행: Unix 명령 삽입|
|932110|원격 명령 실행: Windows 명령 삽입|
|932115|원격 명령 실행: Windows 명령 삽입|
|932120|원격 명령 실행: Windows PowerShell 명령 발견|
|932130|원격 명령 실행: Unix Shell 식 발견|
|932140|원격 명령 실행: Windows FOR/IF 명령 발견|
|932150|원격 명령 실행: 직접 Unix 명령 실행|
|932160|원격 명령 실행: Unix Shell 코드 발견|
|932170|원격 명령 실행: Shellshock(CVE-2014-6271)|
|932171|원격 명령 실행: Shellshock(CVE-2014-6271)|
|932180|제한된 파일 업로드 시도|
|932190|원격 명령 실행: 와일드 카드 바이패스 기술 시도|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-32"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>
|RuleId|설명|
|---|---|
|933100|PHP 삽입 공격: 여는/닫는 태그 발견|
|933110|PHP 삽입 공격: PHP 스크립트 파일 업로드 발견|
|933111|PHP 삽입 공격: PHP 스크립트 파일 업로드 발견|
|933120|PHP 삽입 공격: 구성 지시문 발견|
|933130|PHP 삽입 공격: 변수 발견|
|933131|PHP 삽입 공격: 변수 발견|
|933140|PHP 삽입 공격: I/O 스트림 발견|
|933150|PHP 삽입 공격: 고위험 PHP 함수 이름 발견|
|933151|PHP 삽입 공격: 보통 위험 PHP 함수 이름 발견|
|933160|PHP 삽입 공격: 고위험 PHP 함수 호출 발견|
|933161|PHP 삽입 공격: 저가치 PHP 함수 호출 발견|
|933170|PHP 삽입 공격: 직렬화된 개체 삽입|
|933180|PHP 삽입 공격: 변수 함수 호출 발견|
|933190|PHP 삽입 공격: PHP 닫는 태그 발견|
|933200|PHP 삽입 공격: 래퍼 체계가 감지됨|
|933210|PHP 삽입 공격: 변수 함수 호출 발견|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-32"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>
|RuleId|설명|
|---|---|
|941100|libinjection을 통한 XSS 공격 감지됨|
|941101|libinjection을 통해 XSS 공격이 감지되었습니다.|
|941110|XSS 필터 - 범주 1: 스크립트 태그 벡터|
|941120|XSS 필터 - 범주 2: 이벤트 처리기 벡터|
|941130|XSS 필터 - 범주 3: 특성 벡터|
|941140|XSS 필터 - 범주 4: JavaScript URI 벡터|
|941150|XSS 필터 - 범주 5: 허용되지 않는 HTML 특성|
|941160|NoScript XSS InjectionChecker: HTML 삽입|
|941170|NoScript XSS InjectionChecker: 특성 삽입|
|941180|노드 검사기 블랙리스트 키워드|
|941190|스타일시트를 사용하는 XSS|
|941200|VML 프레임을 사용하는 XSS|
|941210|난독 처리된 JavaScript를 사용하는 XSS|
|941220|난독 처리된 VB 스크립트를 사용하는 XSS|
|941230|'embed' 태그를 사용하는 XSS|
|941240|'import' 또는 'implementation' 특성을 사용하는 XSS|
|941250|IE XSS 필터 - 공격 감지됨.|
|941260|'meta' 태그를 사용하는 XSS|
|941270|'link' href를 사용하는 XSS|
|941280|'base' 태그를 사용하는 XSS|
|941290|'applet' 태그를 사용하는 XSS|
|941300|'object' 태그를 사용하는 XSS|
|941310|US-ASCII 잘못된 형식의 Encoding XSS 필터 - 공격 감지됨.|
|941320|가능한 XSS 공격 감지됨 - HTML 태그 처리기|
|941330|IE XSS 필터 - 공격 감지됨.|
|941340|IE XSS 필터 - 공격 감지됨.|
|941350|UTF-7 Encoding IE XSS - 공격 감지됨.|
|941360|JavaScript 난독 처리가 검색되었습니다.|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-32"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>
|RuleId|설명|
|---|---|
|942100|libinjection을 통한 SQL 삽입 공격 감지됨|
|942110|SQL 삽입 공격: 일반적인 삽입 테스트 감지됨|
|942120|SQL 삽입 공격: SQL 연산자 감지됨|
|942130|SQL 삽입 공격: SQL Tautology 감지됨|
|942140|SQL 삽입 공격: 일반 DB 이름이 감지됨|
|942150|SQL 삽입 공격|
|942160|sleep() 또는 benchmark()를 사용하는 블라인드 sqli 테스트 감지.|
|942170|조건부 쿼리를 포함하여 SQL benchmark 및 sleep 삽입 공격 감지|
|942180|기본 SQL 인증 바이패스 시도 감지 1/3|
|942190|MSSQL 코드 실행 및 정보 수집 시도 감지|
|942200|MySQL 주석-/공백-난독 처리된 삽입 및 억음 악센트 기호 종료 감지|
|942210|연결된 SQL 삽입 시도 감지 1/2|
|942220|정수 오버플로 공격 검색, 3.0.00738585072007e-308이 “매직 넘버” 크래시인 경우를 제외하고 skipfish에서 가져옴|
|942230|조건부 SQL 삽입 시도 감지|
|942240|MySQL 문자 집합 스위치 및 MSSQL DoS 시도 감지|
|942250|MATCH AGAINST, MERGE 및 EXECUTE IMMEDIATE 삽입 감지|
|942251|HAVING 삽입 감지|
|942260|기본 SQL 인증 바이패스 시도 감지 2/3|
|942270|기본적인 sql 삽입 검색. mysql, oracle 및 기타에 대한 일반적인 공격 문자열.|
|942280|Postgres pg_sleep 삽입, waitfor delay 공격 및 데이터베이스 종료 시도 감지|
|942290|기본적인 MongoDB SQL 삽입 시도 찾기|
|942300|MySQL 주석, 조건 및 ch(a)r 삽입 감지|
|942310|연결된 SQL 삽입 시도 감지 2/2|
|942320|MySQL 및 PostgreSQL에 저장된 저장 프로시저/함수 삽입 감지|
|942330|클래식 SQL 삽입 프로빙 감지 1/2|
|942340|기본 SQL 인증 바이패스 시도 감지 3/3|
|942350|MySQL UDF 삽입 및 기타 데이터/구조 조작 시도 감지|
|942360|연결된 기본 SQL 삽입 및 SQLLFI 시도 감지|
|942361|키워드 alter 또는 union을 기준으로 기본 SQL 삽입 감지|
|942370|클래식 SQL 삽입 프로빙 감지 2/2|
|942380|SQL 삽입 공격|
|942390|SQL 삽입 공격|
|942400|SQL 삽입 공격|
|942410|SQL 삽입 공격|
|942420|제한된 SQL 문자 이상 감지(쿠키): 특수 문자 # 초과(8)|
|942421|제한된 SQL 문자 이상 감지(쿠키): 특수 문자 # 초과(3)|
|942430|제한된 SQL 문자 이상 감지(인수): 특수 문자 # 초과(12)|
|942431|제한된 SQL 문자 이상 감지(인수): 특수 문자 # 초과(6)|
|942432|제한된 SQL 문자 이상 감지(인수): 특수 문자 # 초과(2)|
|942440|SQL 주석 시퀀스가 감지됨.|
|942450|SQL 16진수 Encoding이 식별됨|
|942460|메타 문자 이상 감지 경고 - 반복적인 단어가 아닌 문자|
|942470|SQL 삽입 공격|
|942480|SQL 삽입 공격|
|942490|클래식 SQL 삽입 프로빙 감지 3/3|
|942500|MySQL 인라인 주석이 검색되었습니다.|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-32"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>
|RuleId|설명|
|---|---|
|943100|가능한 세션 고정 공격: HTML의 쿠키 값 설정|
|943110|가능한 세션 고정 공격: 오프-도메인 참조 페이지가 있는 SessionID 매개 변수 이름|
|943120|가능한 세션 고정 공격: 참조 페이지가 없는 SessionID 매개 변수 이름|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-javap"></a><a name="crs944-32"></a> <p x-ms-format-detection="none">REQUEST-944-APPLICATION-ATTACK-JAVA</p>
|RuleId|설명|
|---|---|
|944100|원격 명령 실행: Apache Struts, Oracle WebLogic|
|944110|잠재적 페이로드 실행 감지|
|944120|가능한 페이로드 실행 및 원격 명령 실행|
|944130|의심스러운 Java 클래스|
|944200|Java 역직렬화 Apache Commons 악용|
|944210|Java serialization을 사용할 수 있음|
|944240|원격 명령 실행: Java serialization|
|944250|원격 명령 실행: 의심스러운 Java 메서드가 감지됨|
|944300|Base64로 인코딩된 문자열이 의심스러운 키워드와 일치함|

# <a name="owasp-31"></a>[OWASP 3.1](#tab/owasp31)

## <a name="31-rule-sets"></a><a name="owasp31"></a> 3.1 규칙 집합

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">일반</p>

|RuleId|설명|
|---|---|
|200004|가능한 다중 파트 일치하지 않는 경계.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|설명|
|---|---|
|911100|정책에서 허용하지 않는 메서드|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|설명|
|---|---|
|913100|보안 스캐너와 연결된 사용자-에이전트 발견|
|913101|스크립팅/일반 HTTP 클라이언트와 연결된 사용자-에이전트 발견|
|913102|웹 크롤러/봇과 연결된 사용자-에이전트 발견|
|913110|보안 스캐너와 연결된 요청 헤더 발견|
|913120|보안 스캐너와 연결된 요청 파일 이름/인수 발견|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|설명|
|---|---|
|920100|잘못된 HTTP 요청 줄|
|920120|다중 파트/양식 데이터 바이패스 시도|
|920121|다중 파트/양식 데이터 바이패스 시도|
|920130|요청 본문을 구문 분석하지 못했습니다.|
|920140|엄격한 유효성 검사에 실패한 다중 파트 요청 본문|
|920160|콘텐츠-길이 HTTP 헤더가 숫자가 아닙니다.|
|920170|본문 콘텐츠를 통한 GET 또는 HEAD 요청.|
|920171|분할 전송 인코딩을 통한 GET 또는 HEAD 요청.|
|920180|POST 요청에 콘텐츠-길이 헤더가 없습니다.|
|920190|범위 = 마지막 바이트 값이 잘못되었습니다.|
|920200|범위 = 필드가 너무 많음(6개 이상)|
|920201|범위 = pdf 요청에 대한 필드가 너무 많음(35개 이상)|
|920202|범위 = pdf 요청에 대한 필드가 너무 많음(6개 이상)|
|920210|여러 개의 충돌하는 연결 헤더 데이터가 발견되었습니다.|
|920220|URL Encoding 남용 공격 시도|
|920230|여러 URL Encoding 감지됨|
|920240|URL Encoding 남용 공격 시도|
|920250|UTF8 Encoding 남용 공격 시도|
|920260|유니코드 전자/반자 남용 공격 시도|
|920270|요청에 잘못된 문자(null 문자)|
|920271|요청에 잘못된 문자(인쇄할 수 없는 문자)|
|920272|요청에 잘못된 문자(ascii 127 미만의 인쇄 가능한 문자 이외의)|
|920273|요청에 잘못된 문자(매우 엄격한 집합 이외의)|
|920274|요청 헤더에 잘못된 문자(매우 엄격한 집합 이외의)|
|920280|호스트 헤더가 누락된 요청|
|920290|빈 호스트 헤더|
|920300|Accept 헤더가 누락된 요청|
|920310|요청에 빈 Accept 헤더가 있음|
|920311|요청에 빈 Accept 헤더가 있음|
|920320|사용자 에이전트 헤더 누락|
|920330|빈 사용자 에이전트 헤더|
|920340|요청에 콘텐츠는 있지만 Content-Type 헤더 누락|
|920341|콘텐츠가 있는 요청은 Content-Type 헤더 필요|
|920350|호스트 헤더가 숫자 IP 주소|
|920360|인수 이름이 너무 김|
|920370|인수 값이 너무 김|
|920380|요청에 인수가 너무 많음|
|920390|총 인수 크기 초과|
|920400|업로드된 파일 크기가 너무 큼|
|920410|업로드된 총 파일 크기가 너무 큼|
|920420|요청 콘텐츠 유형이 정책에서 허용되지 않음|
|920430|HTTP 프로토콜 버전이 정책에서 허용되지 않음|
|920440|URL 파일 확장명이 정책에서 허용되지 않음|
|920450|HTTP 헤더가 정책에서 제한됨(%@{MATCHED_VAR})|
|920460|비정상적인 이스케이프 문자|
|920470|잘못된 Content-Type 헤더|
|920480|Content-Type 헤더 내에서 charset 매개 변수 제한|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|설명|
|---|---|
|921110|HTTP 요청 밀반입 공격|
|921120|HTTP 응답 분할 공격|
|921130|HTTP 응답 분할 공격|
|921140|헤더를 통한 HTTP 헤더 삽입 공격|
|921150|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 발견)|
|921151|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 발견)|
|921160|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 및 헤더-이름 발견)|
|921170|HTTP 매개 변수 오염|
|921180|HTTP 매개 변수 오염(%{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|설명|
|---|---|
|930100|경로 탐색 공격(/../)|
|930110|경로 탐색 공격(/../)|
|930120|OS 파일 액세스 시도|
|930130|제한된 파일 액세스 시도|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|설명|
|---|---|
|931100|가능한 원격 파일 포함(RFI) 공격 = IP 주소를 사용하는 URL 매개 변수|
|931110|가능한 원격 파일 포함(RFI) 공격 = 일반 RFI 취약한 매개 변수 이름이 URL 페이로드에 사용됨|
|931120|가능한 원격 파일 포함(RFI) 공격 = URL 페이로드가 후행 물음표 문자(?)에 사용됨|
|931130|가능한 원격 파일 포함(RFI) 공격 = 오프-도메인 참조/링크|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|설명|
|---|---|
|932100|원격 명령 실행: Unix 명령 삽입|
|932105|원격 명령 실행: Unix 명령 삽입|
|932106|원격 명령 실행: Unix 명령 삽입|
|932110|원격 명령 실행: Windows 명령 삽입|
|932115|원격 명령 실행: Windows 명령 삽입|
|932120|원격 명령 실행 = Windows PowerShell 명령 발견|
|932130|원격 명령 실행 = Unix 셸 식 발견|
|932140|원격 명령 실행 = Windows FOR/IF 명령 발견|
|932150|원격 명령 실행: 직접 Unix 명령 실행|
|932160|원격 명령 실행 = Unix 셸 코드 발견|
|932170|원격 명령 실행 = Shellshock(CVE-2014-6271)|
|932171|원격 명령 실행 = Shellshock(CVE-2014-6271)|
|932180|제한된 파일 업로드 시도|
|932190|원격 명령 실행: 와일드 카드 바이패스 기술 시도|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|설명|
|---|---|
|933100|PHP 삽입 공격 = 여는/닫는 태그 발견|
|933110|PHP 삽입 공격 = PHP 스크립트 파일 업로드 발견|
|933111|PHP 삽입 공격: PHP 스크립트 파일 업로드 발견|
|933120|PHP 삽입 공격 = 구성 지시문 발견|
|933130|PHP 삽입 공격 = 변수 발견|
|933131|PHP 삽입 공격: 변수 발견|
|933140|PHP 삽입 공격: I/O 스트림 발견|
|933150|PHP 삽입 공격 = 고위험 PHP 함수 이름 발견|
|933151|PHP 삽입 공격: 보통 위험 PHP 함수 이름 발견|
|933160|PHP 삽입 공격 = 고위험 PHP 함수 호출 발견|
|933161|PHP 삽입 공격: 저가치 PHP 함수 호출 발견|
|933170|PHP 삽입 공격: 직렬화된 개체 삽입|
|933180|PHP 삽입 공격 = 변수 함수 호출 발견|
|933190|PHP 삽입 공격: PHP 닫는 태그 발견|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|설명|
|---|---|
|941100|libinjection을 통한 XSS 공격 감지됨|
|941101|libinjection을 통한 XSS 공격 감지됨|
|941110|XSS 필터 - 범주 1 = 스크립트 태그 벡터|
|941130|XSS 필터 - 범주 3 = 특성 벡터|
|941140|XSS 필터 - 범주 4 = JavaScript URI 벡터|
|941150|XSS 필터 - 범주 5 = 허용되지 않는 HTML 특성|
|941160|NoScript XSS InjectionChecker: HTML 삽입|
|941170|NoScript XSS InjectionChecker: 특성 삽입|
|941180|노드 유효성 검사기 블랙리스트 키워드|
|941190|스타일시트를 사용하는 XSS|
|941200|VML 프레임을 사용하는 XSS|
|941210|난독 처리된 JavaScript를 사용하는 XSS|
|941220|난독 처리된 VB 스크립트를 사용하는 XSS|
|941230|'embed' 태그를 사용하는 XSS|
|941240|'import' 또는 'implementation' 특성을 사용하는 XSS|
|941250|IE XSS 필터 - 공격 감지됨|
|941260|'meta' 태그를 사용하는 XSS|
|941270|'link' href를 사용하는 XSS|
|941280|'base' 태그를 사용하는 XSS|
|941290|'applet' 태그를 사용하는 XSS|
|941300|'object' 태그를 사용하는 XSS|
|941310|US-ASCII 잘못된 형식의 Encoding XSS 필터 - 공격 감지됨.|
|941320|가능한 XSS 공격 감지됨 - HTML 태그 처리기|
|941330|IE XSS 필터 - 공격 감지됨.|
|941340|IE XSS 필터 - 공격 감지됨.|
|941350|UTF-7 Encoding IE XSS - 공격 감지됨.|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|설명|
|---|---|
|942100|libinjection을 통한 SQL 삽입 공격 감지됨|
|942110|SQL 삽입 공격: 일반적인 삽입 테스트 감지됨|
|942120|SQL 삽입 공격: SQL 연산자 감지됨|
|942130|SQL 삽입 공격: SQL Tautology 감지됨|
|942140|SQL 삽입 공격 = 일반 DB 이름 감지됨|
|942150|SQL 삽입 공격|
|942160|sleep() 또는 benchmark()를 사용하는 블라인드 sqli 테스트 감지.|
|942170|조건부 쿼리를 포함하여 SQL benchmark 및 sleep 삽입 공격 감지|
|942180|기본 SQL 인증 바이패스 시도 감지 1/3|
|942190|MSSQL 코드 실행 및 정보 수집 시도 감지|
|942200|MySQL 주석-/공백-난독 처리된 삽입 및 억음 악센트 기호 종료 감지|
|942210|연결된 SQL 삽입 시도 감지 1/2|
|942220|정수 오버플로 공격 검색. 3.0.00738585072를 제외하고 skipfish에서 가져옴|
|942230|조건부 SQL 삽입 시도 감지|
|942240|MySQL 문자 집합 스위치 및 MSSQL DoS 시도 감지|
|942250|MATCH AGAINST, MERGE 및 EXECUTE IMMEDIATE 삽입 감지|
|942251|HAVING 삽입 감지|
|942260|기본 SQL 인증 바이패스 시도 감지 2/3|
|942270|기본적인 sql 삽입 검색. mysql oracle 및 기타에 대한 일반적인 공격 문자열|
|942280|Postgres pg_sleep 삽입, waitfor delay 공격 및 데이터베이스 종료 시도 감지|
|942290|기본적인 MongoDB SQL 삽입 시도 찾기|
|942300|MySQL 주석, 조건 및 ch(a)r 삽입 감지|
|942310|연결된 SQL 삽입 시도 감지 2/2|
|942320|MySQL 및 PostgreSQL에 저장된 저장 프로시저/함수 삽입 감지|
|942330|클래식 SQL 삽입 프로빙 감지 1/2|
|942340|기본 SQL 인증 바이패스 시도 감지 3/3|
|942350|MySQL UDF 삽입 및 기타 데이터/구조 조작 시도 감지|
|942360|연결된 기본 SQL 삽입 및 SQLLFI 시도 감지|
|942361|키워드 alter 또는 union을 기준으로 기본 SQL 삽입 감지|
|942370|클래식 SQL 삽입 프로빙 감지 2/2|
|942380|SQL 삽입 공격|
|942390|SQL 삽입 공격|
|942400|SQL 삽입 공격|
|942410|SQL 삽입 공격|
|942420|제한된 SQL 문자 이상 감지(쿠키): 특수 문자 # 초과(8)|
|942421|제한된 SQL 문자 이상 감지(쿠키): 특수 문자 # 초과(3)|
|942430|제한된 SQL 문자 이상 감지(인수): 특수 문자 # 초과(12)|
|942431|제한된 SQL 문자 이상 감지(인수): 특수 문자 # 초과(6)|
|942432|제한된 SQL 문자 이상 감지(인수): 특수 문자 # 초과(2)|
|942440|SQL 주석 시퀀스가 감지됨.|
|942450|SQL 16진수 Encoding이 식별됨|
|942460|메타 문자 이상 감지 경고 - 반복적인 단어가 아닌 문자|
|942470|SQL 삽입 공격|
|942480|SQL 삽입 공격|
|942490|클래식 SQL 삽입 프로빙 감지 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|설명|
|---|---|
|943100|가능한 세션 고정 공격 = HTML의 쿠키 값 설정|
|943110|가능한 세션 고정 공격 = 오프-도메인 참조 페이지가 있는 SessionID 매개 변수 이름|
|943120|가능한 세션 고정 공격 = 참조 페이지가 없는 SessionID 매개 변수 이름|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">REQUEST-944-APPLICATION-ATTACK-SESSION-JAVA</p>

|RuleId|설명|
|---|---|
|944120|가능한 페이로드 실행 및 원격 명령 실행|
|944130|의심스러운 Java 클래스|
|944200|Java 역직렬화 Apache Commons 악용|

# <a name="owasp-30"></a>[OWASP 3.0](#tab/owasp30)

## <a name="30-rule-sets"></a><a name="owasp30"></a> 3.0 규칙 집합

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">일반</p>

|RuleId|설명|
|---|---|
|200004|가능한 다중 파트 일치하지 않는 경계.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|설명|
|---|---|
|911100|정책에서 허용하지 않는 메서드|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|설명|
|---|---|
|913100|보안 스캐너와 연결된 사용자-에이전트 발견|
|913110|보안 스캐너와 연결된 요청 헤더 발견|
|913120|보안 스캐너와 연결된 요청 파일 이름/인수 발견|
|913101|스크립팅/일반 HTTP 클라이언트와 연결된 사용자-에이전트 발견|
|913102|웹 크롤러/봇과 연결된 사용자-에이전트 발견|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|설명|
|---|---|
|920100|잘못된 HTTP 요청 줄|
|920130|요청 본문을 구문 분석하지 못했습니다.|
|920140|엄격한 유효성 검사에 실패한 다중 파트 요청 본문|
|920160|콘텐츠-길이 HTTP 헤더가 숫자가 아닙니다.|
|920170|본문 콘텐츠를 통한 GET 또는 HEAD 요청.|
|920180|POST 요청에 콘텐츠-길이 헤더가 없습니다.|
|920190|범위 = 마지막 바이트 값이 잘못되었습니다.|
|920210|여러 개의 충돌하는 연결 헤더 데이터가 발견되었습니다.|
|920220|URL Encoding 남용 공격 시도|
|920240|URL Encoding 남용 공격 시도|
|920250|UTF8 Encoding 남용 공격 시도|
|920260|유니코드 전자/반자 남용 공격 시도|
|920270|요청에 잘못된 문자(null 문자)|
|920280|호스트 헤더가 누락된 요청|
|920290|빈 호스트 헤더|
|920310|요청에 빈 Accept 헤더가 있음|
|920311|요청에 빈 Accept 헤더가 있음|
|920330|빈 사용자 에이전트 헤더|
|920340|요청에 콘텐츠는 있지만 Content-Type 헤더 누락|
|920350|호스트 헤더가 숫자 IP 주소|
|920380|요청에 인수가 너무 많음|
|920360|인수 이름이 너무 김|
|920370|인수 값이 너무 김|
|920390|총 인수 크기 초과|
|920400|업로드된 파일 크기가 너무 큼|
|920410|업로드된 총 파일 크기가 너무 큼|
|920420|요청 콘텐츠 유형이 정책에서 허용되지 않음|
|920430|HTTP 프로토콜 버전이 정책에서 허용되지 않음|
|920440|URL 파일 확장명이 정책에서 허용되지 않음|
|920450|HTTP 헤더가 정책에서 제한됨(%@{MATCHED_VAR})|
|920200|범위 = 필드가 너무 많음(6개 이상)|
|920201|범위 = pdf 요청에 대한 필드가 너무 많음(35개 이상)|
|920230|여러 URL Encoding 감지됨|
|920300|Accept 헤더가 누락된 요청|
|920271|요청에 잘못된 문자(인쇄할 수 없는 문자)|
|920320|사용자 에이전트 헤더 누락|
|920272|요청에 잘못된 문자(ascii 127 미만의 인쇄 가능한 문자 이외의)|
|920202|범위 = pdf 요청에 대한 필드가 너무 많음(6개 이상)|
|920273|요청에 잘못된 문자(매우 엄격한 집합 이외의)|
|920274|요청 헤더에 잘못된 문자(매우 엄격한 집합 이외의)|
|920460|비정상적인 이스케이프 문자|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|설명|
|---|---|
|921100|HTTP 요청 스머글링 공격.|
|921110|HTTP 요청 밀반입 공격|
|921120|HTTP 응답 분할 공격|
|921130|HTTP 응답 분할 공격|
|921140|헤더를 통한 HTTP 헤더 삽입 공격|
|921150|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 발견)|
|921160|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 및 헤더-이름 발견)|
|921151|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 발견)|
|921170|HTTP 매개 변수 오염|
|921180|HTTP 매개 변수 오염(%@{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|설명|
|---|---|
|930100|경로 탐색 공격(/../)|
|930110|경로 탐색 공격(/../)|
|930120|OS 파일 액세스 시도|
|930130|제한된 파일 액세스 시도|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|설명|
|---|---|
|931100|가능한 원격 파일 포함(RFI) 공격 = IP 주소를 사용하는 URL 매개 변수|
|931110|가능한 원격 파일 포함(RFI) 공격 = 일반 RFI 취약한 매개 변수 이름이 URL 페이로드에 사용됨|
|931120|가능한 원격 파일 포함(RFI) 공격 = URL 페이로드가 후행 물음표 문자(?)에 사용됨|
|931130|가능한 원격 파일 포함(RFI) 공격 = 오프-도메인 참조/링크|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|설명|
|---|---|
|932120|원격 명령 실행 = Windows PowerShell 명령 발견|
|932130|원격 명령 실행 = Unix 셸 식 발견|
|932140|원격 명령 실행 = Windows FOR/IF 명령 발견|
|932160|원격 명령 실행 = Unix 셸 코드 발견|
|932170|원격 명령 실행 = Shellshock(CVE-2014-6271)|
|932171|원격 명령 실행 = Shellshock(CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|설명|
|---|---|
|933100|PHP 삽입 공격 = 여는/닫는 태그 발견|
|933110|PHP 삽입 공격 = PHP 스크립트 파일 업로드 발견|
|933120|PHP 삽입 공격 = 구성 지시문 발견|
|933130|PHP 삽입 공격 = 변수 발견|
|933150|PHP 삽입 공격 = 고위험 PHP 함수 이름 발견|
|933160|PHP 삽입 공격 = 고위험 PHP 함수 호출 발견|
|933180|PHP 삽입 공격 = 변수 함수 호출 발견|
|933151|PHP 삽입 공격 = 보통 위험 PHP 함수 이름 발견|
|933131|PHP 삽입 공격 = 변수 발견|
|933161|PHP 삽입 공격 = 저가치 PHP 함수 호출 발견|
|933111|PHP 삽입 공격 = PHP 스크립트 파일 업로드 발견|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|설명|
|---|---|
|941100|libinjection을 통한 XSS 공격 감지됨|
|941110|XSS 필터 - 범주 1 = 스크립트 태그 벡터|
|941130|XSS 필터 - 범주 3 = 특성 벡터|
|941140|XSS 필터 - 범주 4 = JavaScript URI 벡터|
|941150|XSS 필터 - 범주 5 = 허용되지 않는 HTML 특성|
|941180|노드 유효성 검사기 블랙리스트 키워드|
|941190|스타일시트를 사용하는 XSS|
|941200|VML 프레임을 사용하는 XSS|
|941210|난독 처리된 JavaScript를 사용하는 XSS|
|941220|난독 처리된 VB 스크립트를 사용하는 XSS|
|941230|'embed' 태그를 사용하는 XSS|
|941240|'import' 또는 'implementation' 특성을 사용하는 XSS|
|941260|'meta' 태그를 사용하는 XSS|
|941270|'link' href를 사용하는 XSS|
|941280|'base' 태그를 사용하는 XSS|
|941290|'applet' 태그를 사용하는 XSS|
|941300|'object' 태그를 사용하는 XSS|
|941310|US-ASCII 잘못된 형식의 Encoding XSS 필터 - 공격 감지됨.|
|941330|IE XSS 필터 - 공격 감지됨.|
|941340|IE XSS 필터 - 공격 감지됨.|
|941350|UTF-7 Encoding IE XSS - 공격 감지됨.|
|941320|가능한 XSS 공격 감지됨 - HTML 태그 처리기|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|설명|
|---|---|
|942100|libinjection을 통한 SQL 삽입 공격 감지됨|
|942110|SQL 삽입 공격: 일반적인 삽입 테스트 감지됨|
|942130|SQL 삽입 공격: SQL Tautology 감지됨|
|942140|SQL 삽입 공격 = 일반 DB 이름 감지됨|
|942160|sleep() 또는 benchmark()를 사용하는 블라인드 sqli 테스트 감지.|
|942170|조건부 쿼리를 포함하여 SQL benchmark 및 sleep 삽입 공격 감지|
|942190|MSSQL 코드 실행 및 정보 수집 시도 감지|
|942200|MySQL 주석-/공백-난독 처리된 삽입 및 억음 악센트 기호 종료 감지|
|942230|조건부 SQL 삽입 시도 감지|
|942260|기본 SQL 인증 바이패스 시도 감지 2/3|
|942270|기본적인 sql 삽입 검색. mysql oracle 및 기타에 대한 일반적인 공격 문자열.|
|942290|기본적인 MongoDB SQL 삽입 시도 찾기|
|942300|MySQL 주석, 조건 및 ch(a)r 삽입 감지|
|942310|연결된 SQL 삽입 시도 감지 2/2|
|942320|MySQL 및 PostgreSQL에 저장된 저장 프로시저/함수 삽입 감지|
|942330|클래식 SQL 삽입 프로빙 감지 1/2|
|942340|기본 SQL 인증 바이패스 시도 감지 3/3|
|942350|MySQL UDF 삽입 및 기타 데이터/구조 조작 시도 감지|
|942360|연결된 기본 SQL 삽입 및 SQLLFI 시도 감지|
|942370|클래식 SQL 삽입 프로빙 감지 2/2|
|942150|SQL 삽입 공격|
|942410|SQL 삽입 공격|
|942430|제한된 SQL 문자 이상 감지(인수): 특수 문자 # 초과(12)|
|942440|SQL 주석 시퀀스가 감지됨.|
|942450|SQL 16진수 Encoding이 식별됨|
|942251|HAVING 삽입 감지|
|942460|메타 문자 이상 감지 경고 - 반복적인 단어가 아닌 문자|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|설명|
|---|---|
|943100|가능한 세션 고정 공격 = HTML의 쿠키 값 설정|
|943110|가능한 세션 고정 공격 = 오프-도메인 참조 페이지가 있는 SessionID 매개 변수 이름|
|943120|가능한 세션 고정 공격 = 참조 페이지가 없는 SessionID 매개 변수 이름|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="29-rule-sets"></a><a name="owasp229"></a> 2.9 규칙 집합

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a> crs_20_protocol_violations

|RuleId|설명|
|---|---|
|960911|잘못된 HTTP 요청 줄|
|981227|Apache 오류 = 요청에 잘못된 URI가 있습니다.|
|960912|요청 본문을 구문 분석하지 못했습니다.|
|960914|엄격한 유효성 검사에 실패한 다중 파트 요청 본문|
|960915|다중 파트 파서가 일치하지 않을 수도 있는 경계를 감지했습니다.|
|960016|콘텐츠-길이 HTTP 헤더가 숫자가 아닙니다.|
|960011|본문 콘텐츠를 통한 GET 또는 HEAD 요청.|
|960012|POST 요청에 콘텐츠-길이 헤더가 없습니다.|
|960902|ID Encoding 사용이 잘못되었습니다.|
|960022|HTTP 1.0에는 예상 헤더가 허용되지 않습니다.|
|960020|Pragma 헤더에는 HTTP/1.1 요청에 대한 캐시-제어 헤더가 필요합니다.|
|958291|범위 = 필드가 존재하며 0부터 시작합니다.|
|958230|범위 = 마지막 바이트 값이 잘못되었습니다.|
|958295|여러 개의 충돌하는 연결 헤더 데이터가 발견되었습니다.|
|950107|URL Encoding 남용 공격 시도|
|950109|여러 URL Encoding 감지됨|
|950108|URL Encoding 남용 공격 시도|
|950801|UTF8 Encoding 남용 공격 시도|
|950116|유니코드 전자/반자 남용 공격 시도|
|960901|요청에 잘못된 문자|
|960018|요청에 잘못된 문자|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|설명|
|---|---|
|960008|호스트 헤더가 누락된 요청|
|960007|빈 호스트 헤더|
|960015|Accept 헤더가 누락된 요청|
|960021|요청에 빈 Accept 헤더가 있음|
|960009|요청에 사용자 에이전트 헤더 누락|
|960006|빈 사용자 에이전트 헤더|
|960904|요청에 콘텐츠는 있지만 Content-Type 헤더 누락|
|960017|호스트 헤더가 숫자 IP 주소|

### <a name="crs_23_request_limits"></a><a name="crs23"></a> crs_23_request_limits

|RuleId|설명|
|---|---|
|960209|인수 이름이 너무 김|
|960208|인수 값이 너무 김|
|960335|요청에 인수가 너무 많음|
|960341|총 인수 크기 초과|
|960342|업로드된 파일 크기가 너무 큼|
|960343|업로드된 총 파일 크기가 너무 큼|

### <a name="crs_30_http_policy"></a><a name="crs30"></a> crs_30_http_policy

|RuleId|설명|
|---|---|
|960032|정책에서 허용하지 않는 메서드|
|960010|요청 콘텐츠 유형이 정책에서 허용되지 않음|
|960034|HTTP 프로토콜 버전이 정책에서 허용되지 않음|
|960035|URL 파일 확장명이 정책에서 허용되지 않음|
|960038|HTTP 헤더가 정책에서 제한됨|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a> crs_35_bad_robots

|RuleId|설명|
|---|---|
|990002|요청에 보안 스캐너가 사이트를 스캔한 것으로 표시됨|
|990901|요청에 보안 스캐너가 사이트를 스캔한 것으로 표시됨|
|990902|요청에 보안 스캐너가 사이트를 스캔한 것으로 표시됨|
|990012|악의적인 웹 사이트 크롤러|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a> crs_40_generic_attacks

|RuleId|설명|
|---|---|
|960024|메타 문자 이상 감지 경고 - 반복적인 단어가 아닌 문자|
|950008|문서화되지 않은 ColdFusion 태그의 삽입|
|950010|LDAP 삽입 공격|
|950011|SSI 삽입 공격|
|950018|범용 PDF XSS URL을 감지했습니다.|
|950019|전자 메일 삽입 공격|
|950012|HTTP 요청 스머글링 공격.|
|950910|HTTP 응답 분할 공격|
|950911|HTTP 응답 분할 공격|
|950117|원격 파일 포함 공격|
|950118|원격 파일 포함 공격|
|950119|원격 파일 포함 공격|
|950120|가능한 원격 파일 포함(RFI) 공격 = 오프-도메인 참조/링크|
|981133|규칙 981133|
|981134|규칙 981134|
|950009|세션 고정 공격|
|950003|세션 고정|
|950000|세션 고정|
|950005|원격 파일 액세스 시도|
|950002|시스템 명령 액세스|
|950006|시스템 명령 삽입|
|959151|PHP 삽입 공격|
|958976|PHP 삽입 공격|
|958977|PHP 삽입 공격|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|설명|
|---|---|
|981231|SQL 주석 시퀀스가 감지됨.|
|981260|SQL 16진수 Encoding이 식별됨|
|981320|SQL 삽입 공격 = 일반 DB 이름 감지됨|
|981300|규칙 981300|
|981301|규칙 981301|
|981302|규칙 981302|
|981303|규칙 981303|
|981304|규칙 981304|
|981305|규칙 981305|
|981306|규칙 981306|
|981307|규칙 981307|
|981308|규칙 981308|
|981309|규칙 981309|
|981310|규칙 981310|
|981311|규칙 981311|
|981312|규칙 981312|
|981313|규칙 981313|
|981314|규칙 981314|
|981315|규칙 981315|
|981316|규칙 981316|
|981317|SQL SELECT 문 이상 감지 경고|
|950007|블라인드 SQL 삽입 공격|
|950001|SQL 삽입 공격|
|950908|SQL 삽입 공격.|
|959073|SQL 삽입 공격|
|981272|sleep() 또는 benchmark()를 사용하는 블라인드 sqli 테스트 감지.|
|981250|조건부 쿼리를 포함하여 SQL benchmark 및 sleep 삽입 공격 감지|
|981241|조건부 SQL 삽입 시도 감지|
|981276|기본적인 sql 삽입 검색. mysql oracle 및 기타에 대한 일반적인 공격 문자열.|
|981270|기본적인 MongoDB SQL 삽입 시도 찾기|
|981253|MySQL 및 PostgreSQL에 저장된 저장 프로시저/함수 삽입 감지|
|981251|MySQL UDF 삽입 및 기타 데이터/구조 조작 시도 감지|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|설명|
|---|---|
|973336|XSS 필터 - 범주 1 = 스크립트 태그 벡터|
|973338|XSS 필터 - 범주 3 = JavaScript URI 벡터|
|981136|규칙 981136|
|981018|규칙 981018|
|958016|사이트 간 스크립팅(XSS) 공격|
|958414|사이트 간 스크립팅(XSS) 공격|
|958032|사이트 간 스크립팅(XSS) 공격|
|958026|사이트 간 스크립팅(XSS) 공격|
|958027|사이트 간 스크립팅(XSS) 공격|
|958054|사이트 간 스크립팅(XSS) 공격|
|958418|사이트 간 스크립팅(XSS) 공격|
|958034|사이트 간 스크립팅(XSS) 공격|
|958019|사이트 간 스크립팅(XSS) 공격|
|958013|사이트 간 스크립팅(XSS) 공격|
|958408|사이트 간 스크립팅(XSS) 공격|
|958012|사이트 간 스크립팅(XSS) 공격|
|958423|사이트 간 스크립팅(XSS) 공격|
|958002|사이트 간 스크립팅(XSS) 공격|
|958017|사이트 간 스크립팅(XSS) 공격|
|958007|사이트 간 스크립팅(XSS) 공격|
|958047|사이트 간 스크립팅(XSS) 공격|
|958410|사이트 간 스크립팅(XSS) 공격|
|958415|사이트 간 스크립팅(XSS) 공격|
|958022|사이트 간 스크립팅(XSS) 공격|
|958405|사이트 간 스크립팅(XSS) 공격|
|958419|사이트 간 스크립팅(XSS) 공격|
|958028|사이트 간 스크립팅(XSS) 공격|
|958057|사이트 간 스크립팅(XSS) 공격|
|958031|사이트 간 스크립팅(XSS) 공격|
|958006|사이트 간 스크립팅(XSS) 공격|
|958033|사이트 간 스크립팅(XSS) 공격|
|958038|사이트 간 스크립팅(XSS) 공격|
|958409|사이트 간 스크립팅(XSS) 공격|
|958001|사이트 간 스크립팅(XSS) 공격|
|958005|사이트 간 스크립팅(XSS) 공격|
|958404|사이트 간 스크립팅(XSS) 공격|
|958023|사이트 간 스크립팅(XSS) 공격|
|958010|사이트 간 스크립팅(XSS) 공격|
|958411|사이트 간 스크립팅(XSS) 공격|
|958422|사이트 간 스크립팅(XSS) 공격|
|958036|사이트 간 스크립팅(XSS) 공격|
|958000|사이트 간 스크립팅(XSS) 공격|
|958018|사이트 간 스크립팅(XSS) 공격|
|958406|사이트 간 스크립팅(XSS) 공격|
|958040|사이트 간 스크립팅(XSS) 공격|
|958052|사이트 간 스크립팅(XSS) 공격|
|958037|사이트 간 스크립팅(XSS) 공격|
|958049|사이트 간 스크립팅(XSS) 공격|
|958030|사이트 간 스크립팅(XSS) 공격|
|958041|사이트 간 스크립팅(XSS) 공격|
|958416|사이트 간 스크립팅(XSS) 공격|
|958024|사이트 간 스크립팅(XSS) 공격|
|958059|사이트 간 스크립팅(XSS) 공격|
|958417|사이트 간 스크립팅(XSS) 공격|
|958020|사이트 간 스크립팅(XSS) 공격|
|958045|사이트 간 스크립팅(XSS) 공격|
|958004|사이트 간 스크립팅(XSS) 공격|
|958421|사이트 간 스크립팅(XSS) 공격|
|958009|사이트 간 스크립팅(XSS) 공격|
|958025|사이트 간 스크립팅(XSS) 공격|
|958413|사이트 간 스크립팅(XSS) 공격|
|958051|사이트 간 스크립팅(XSS) 공격|
|958420|사이트 간 스크립팅(XSS) 공격|
|958407|사이트 간 스크립팅(XSS) 공격|
|958056|사이트 간 스크립팅(XSS) 공격|
|958011|사이트 간 스크립팅(XSS) 공격|
|958412|사이트 간 스크립팅(XSS) 공격|
|958008|사이트 간 스크립팅(XSS) 공격|
|958046|사이트 간 스크립팅(XSS) 공격|
|958039|사이트 간 스크립팅(XSS) 공격|
|958003|사이트 간 스크립팅(XSS) 공격|
|973300|가능한 XSS 공격 감지됨 - HTML 태그 처리기|
|973301|XSS 공격 감지|
|973302|XSS 공격 감지|
|973303|XSS 공격 감지|
|973304|XSS 공격 감지|
|973305|XSS 공격 감지|
|973306|XSS 공격 감지|
|973307|XSS 공격 감지|
|973308|XSS 공격 감지|
|973309|XSS 공격 감지|
|973311|XSS 공격 감지|
|973313|XSS 공격 감지|
|973314|XSS 공격 감지|
|973331|IE XSS 필터 - 공격 감지됨.|
|973315|IE XSS 필터 - 공격 감지됨.|
|973330|IE XSS 필터 - 공격 감지됨.|
|973327|IE XSS 필터 - 공격 감지됨.|
|973326|IE XSS 필터 - 공격 감지됨.|
|973346|IE XSS 필터 - 공격 감지됨.|
|973345|IE XSS 필터 - 공격 감지됨.|
|973324|IE XSS 필터 - 공격 감지됨.|
|973323|IE XSS 필터 - 공격 감지됨.|
|973348|IE XSS 필터 - 공격 감지됨.|
|973321|IE XSS 필터 - 공격 감지됨.|
|973320|IE XSS 필터 - 공격 감지됨.|
|973318|IE XSS 필터 - 공격 감지됨.|
|973317|IE XSS 필터 - 공격 감지됨.|
|973329|IE XSS 필터 - 공격 감지됨.|
|973328|IE XSS 필터 - 공격 감지됨.|

### <a name="crs_42_tight_security"></a><a name="crs42"></a> crs_42_tight_security

|RuleId|설명|
|---|---|
|950103|경로 조작 공격|

### <a name="crs_45_trojans"></a><a name="crs45"></a> crs_45_trojans

|RuleId|설명|
|---|---|
|950110|백도어 액세스|
|950921|백도어 액세스|
|950922|백도어 액세스|

---

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 웹 애플리케이션 방화벽 규칙 사용자 지정](application-gateway-customize-waf-rules-portal.md)
