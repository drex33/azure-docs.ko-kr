---
title: Azure Front Door DRS 규칙 그룹 및 규칙의 Azure Web Application Firewall
description: 이 문서에서는 Web Application Firewall DRS 규칙 그룹 및 규칙에 대한 정보를 제공합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 07/29/2021
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: d1e6349dd2c809c2a18551037c3bd3a8970f6d68
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566465"
---
# <a name="web-application-firewall-drs-rule-groups-and-rules"></a>Web Application Firewall DRS 규칙 그룹 및 규칙

Azure Front Door WAF(Web Application Firewall)는 일반적인 취약성과 익스플로잇으로부터 웹 애플리케이션을 보호합니다. Azure 관리형 규칙 세트는 일반적인 보안 위협에 대한 보호를 쉽게 배포할 수 있는 방법을 제공합니다. 그러한 규칙 세트는 Azure에서 관리되므로, 새로운 공격 서명으로부터 보호하기 위해 필요에 따라 규칙이 업데이트됩니다.


## <a name="default-rule-sets"></a>기본 규칙 집합

Azure 관리형 기본 규칙 세트에는 다음 위협 범주에 대한 규칙이 포함됩니다.

- 사이트 간 스크립팅
- Java 공격
- 로컬 파일 포함
- PHP 삽입 공격
- 원격 명령 실행
- 원격 파일 포함
- 세션 고정
- SQL 삽입 공격 보호
- 프로토콜 공격자

새로운 공격 서명이 규칙 세트에 추가되면 기본 규칙 세트의 버전 번호가 증가합니다.
기본 규칙 세트는 WAF 정책의 감지 모드에서 기본적으로 사용하도록 설정됩니다. 기본 규칙 세트 내에서 개별 규칙을 사용하지 않거나 사용하도록 설정하여 애플리케이션 요구 사항을 충족할 수 있습니다. 규칙별로 특정 작업(허용/차단/리디렉션/로그)을 설정할 수도 있습니다.

경우에 따라 WAF 평가에서 특정 요청 특성을 생략해야 할 수도 있습니다. 일반적인 예제로는 인증에 사용되는 Active Directory 삽입 토큰이 있습니다. 관리되는 규칙, 규칙 그룹 또는 전체 규칙 세트에 대한 제외 목록을 구성할 수 있습니다.  

기본 작업은 차단하는 것입니다. 또한 기본 규칙 세트에서 미리 구성된 규칙을 무시하려는 경우 동일한 WAF 정책에서 사용자 지정 규칙을 구성할 수 있습니다.

사용자 지정 규칙은 항상 기본 규칙 세트의 규칙을 평가하기 전에 적용됩니다. 요청이 사용자 지정 규칙과 일치하면 해당 규칙 동작이 적용됩니다. 요청이 차단되거나 백 엔드로 전달됩니다. 다른 사용자 지정 규칙이나 기본 규칙 세트의 규칙은 처리되지 않습니다. WAF 정책에서 기본 규칙 세트를 제거할 수도 있습니다.

### <a name="microsoft-threat-intelligence-collection-rules"></a>Microsoft 위협 인텔리전스 수집 규칙

Microsoft 위협 인텔리전스 수집 규칙은 향상된 적용 범위, 특정 취약성에 대한 패치, 가양성 감소를 제공하도록 Microsoft 인텔리전스 팀과 협력하여 작성되었습니다.

### <a name="anomaly-scoring-mode"></a>변칙 채점 모드

OWASP는 트래픽 차단 여부를 결정하는 기존 모드와 변칙 채점 모드 두 가지가 있습니다.

기존 모드에서는 특정 규칙과 일치하는 트래픽은 다른 규칙과 상관없이 일치 항목으로 간주됩니다. 이 모드는 이해하기 쉽습니다. 그러나 특정 요청과 일치하는 규칙이 몇 개인지 알 수 없다는 제한이 있습니다. 이러한 제한을 극복하기 위해 변칙 채점 모드가 도입되었습니다. 변칙 채점 모드는 OWASP 3.*x* 의 기본값입니다.

변칙 채점 모드에서는 방화벽이 방지 모드일 때 특정 규칙과 일치하는 트래픽이 즉시 차단되지 않습니다. 규칙에는 네 가지 심각도 *중요*, *오류*, *경고* 또는 *알림* 이 있습니다. 이 심각도는 요청의 숫자 값에 영향을 주며, 이것을 변칙 점수라고 합니다. 예를 들어 *경고* 규칙 일치 항목 하나당 3점입니다. *중요* 규칙 일치 항목 하나당 5점입니다.

|심각도  |값  |
|---------|---------|
|위험     |5|
|Error        |4|
|Warning      |3|
|알림       |2|

변칙 점수가 트래픽을 차단하는 임계값은 5입니다. 따라서 ‘위험’ 규칙 일치 항목이 하나만 있으면 WAF가 방지 모드에서도 요청을 차단하기에 충분합니다. 하지만 *경고* 규칙 일치 항목이 하나 있으면 변칙 점수가 3 증가하므로 트래픽을 차단하기에는 점수가 부족합니다.

### <a name="drs-20"></a>DRS 2.0

DRS 2.0에는 다음 표와 같이 17개의 규칙 그룹이 포함되어 있습니다. 각 그룹은 비활성화할 수 있는 여러 개의 규칙을 포함합니다.

> [!NOTE]
> DRS 2.0은 Azure Front Door 프리미엄에서만 사용할 수 있습니다.

|규칙 그룹|설명|
|---|---|
|**[일반](#general-20)**|일반 그룹|
|**[METHOD-ENFORCEMENT](#drs911-20)**|잠금 메서드(PUT, PATCH)|
|**[PROTOCOL-ENFORCEMENT](#drs920-20)**|프로토콜 및 인코딩 문제로부터 보호|
|**[PROTOCOL-ATTACK](#drs921-20)**|헤더 삽입, 요청 스머글링 및 응답 분할로부터 보호|
|**[APPLICATION-ATTACK-LFI](#drs930-20)**|파일 및 경로 공격으로부터 보호|
|**[APPLICATION-ATTACK-RFI](#drs931-20)**|RFI(원격 파일 포함) 공격으로부터 보호|
|**[APPLICATION-ATTACK-RCE](#drs932-20)**|원격 코드 실행 공격으로부터 보호|
|**[APPLICATION-ATTACK-PHP](#drs933-20)**|PHP 삽입 공격으로부터 보호|
|**[APPLICATION-ATTACK-NodeJS](#drs934-20)**|Node JS 공격으로부터 보호|
|**[APPLICATION-ATTACK-XSS](#drs941-20)**|사이트 간 스크립팅 공격으로부터 보호|
|**[APPLICATION-ATTACK-SQLI](#drs942-20)**|SQL 삽입 공격으로부터 보호|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-20)**|세션 고정 공격으로부터 보호|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-20)**|JAVA 공격으로부터 보호|
|**[MS-ThreatIntel-WebShells](#drs9905-20)**|웹 셸 공격으로부터 보호|
|**[MS-ThreatIntel-AppSec](#drs9903-20)**|AppSec 공격으로부터 보호|
|**[MS-ThreatIntel-SQLI](#drs99031-20)**|SQLI 공격으로부터 보호|
|**[MS-ThreatIntel-CVEs](#drs99001-20)**|CVE 공격으로부터 보호|

### <a name="drs-11"></a>DRS 1.1
|규칙 그룹|설명|
|---|---|
|**[PROTOCOL-ATTACK](#drs921-11)**|헤더 삽입, 요청 스머글링 및 응답 분할로부터 보호|
|**[APPLICATION-ATTACK-LFI](#drs930-11)**|파일 및 경로 공격으로부터 보호|
|**[APPLICATION-ATTACK-RFI](#drs931-11)**|원격 파일 포함 공격으로부터 보호|
|**[APPLICATION-ATTACK-RCE](#drs932-11)**|원격 명령 실행으로부터 보호|
|**[APPLICATION-ATTACK-PHP](#drs933-11)**|PHP 삽입 공격으로부터 보호|
|**[APPLICATION-ATTACK-XSS](#drs941-11)**|사이트 간 스크립팅 공격으로부터 보호|
|**[APPLICATION-ATTACK-SQLI](#drs942-11)**|SQL 삽입 공격으로부터 보호|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-11)**|세션 고정 공격으로부터 보호|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-11)**|JAVA 공격으로부터 보호|
|**[MS-ThreatIntel-WebShells](#drs9905-11)**|웹 셸 공격으로부터 보호|
|**[MS-ThreatIntel-AppSec](#drs9903-11)**|AppSec 공격으로부터 보호|
|**[MS-ThreatIntel-SQLI](#drs99031-11)**|SQLI 공격으로부터 보호|
|**[MS-ThreatIntel-CVEs](#drs99001-11)**|CVE 공격으로부터 보호|

### <a name="drs-10"></a>DRS 1.0

|규칙 그룹|설명|
|---|---|
|**[PROTOCOL-ATTACK](#drs921-10)**|헤더 삽입, 요청 스머글링 및 응답 분할로부터 보호|
|**[APPLICATION-ATTACK-LFI](#drs930-10)**|파일 및 경로 공격으로부터 보호|
|**[APPLICATION-ATTACK-RFI](#drs931-10)**|원격 파일 포함 공격으로부터 보호|
|**[APPLICATION-ATTACK-RCE](#drs932-10)**|원격 명령 실행으로부터 보호|
|**[APPLICATION-ATTACK-PHP](#drs933-10)**|PHP 삽입 공격으로부터 보호|
|**[APPLICATION-ATTACK-SQLI](#drs942-10)**|SQL 삽입 공격으로부터 보호|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-10)**|세션 고정 공격으로부터 보호|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-10)**|JAVA 공격으로부터 보호|



### <a name="bot-rules"></a>봇 규칙

|규칙 그룹|설명|
|---|---|
|**[BadBots](#bot100)**|잘못된 봇으로부터 보호|
|**[GoodBots](#bot200)**|양호한 봇 식별|
|**[UnknownBots](#bot300)**|알 수 없는 봇 식별|



다음 규칙 그룹 및 규칙은 Azure Front Door에서 웹 애플리케이션 방화벽을 사용할 때 사용할 수 있습니다.

# <a name="drs-20"></a>[DRS 2.0](#tab/drs20)

## <a name="20-rule-sets"></a><a name="drs20"></a> 2.0 규칙 집합

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-20"></a> <p x-ms-format-detection="none">일반</p>
|RuleId|설명|
|---|---|
|200002|요청 본문을 구문 분석하지 못했습니다.|
|200003|엄격한 유효성 검사에 실패한 다중 파트 요청 본문|


### <a name="p-x-ms-format-detectionnonemethod-enforcementp"></a><a name="drs911-20"></a> <p x-ms-format-detection="none">METHOD ENFORCEMENT</p>
|RuleId|설명|
|---|---|
|911100|정책에서 허용하지 않는 메서드|

### <a name="p-x-ms-format-detectionnoneprotocol-enforcementp"></a><a name="drs920-20"></a> <p x-ms-format-detection="none">PROTOCOL-ENFORCEMENT</p>
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
|920210|여러 개의 충돌하는 연결 헤더 데이터가 발견되었습니다.|
|920220|URL Encoding 남용 공격 시도|
|920230|여러 URL Encoding 감지됨|
|920240|URL Encoding 남용 공격 시도|
|920260|유니코드 전자/반자 남용 공격 시도|
|920270|요청에 잘못된 문자(null 문자)|
|920271|요청에 잘못된 문자(인쇄할 수 없는 문자)|
|920280|호스트 헤더가 누락된 요청|
|920290|빈 호스트 헤더|
|920300|Accept 헤더가 누락된 요청|
|920310|요청에 빈 Accept 헤더가 있음|
|920311|요청에 빈 Accept 헤더가 있음|
|920320|사용자 에이전트 헤더 누락|
|920330|빈 사용자 에이전트 헤더|
|920340|요청에 콘텐츠가 있지만 Content-Type 헤더가 누락됨|
|920341|콘텐츠가 있는 요청은 Content-Type 헤더 필요|
|920350|호스트 헤더가 숫자 IP 주소|
|920420|요청 콘텐츠 유형이 정책에서 허용되지 않음|
|920430|HTTP 프로토콜 버전이 정책에서 허용되지 않음|
|920440|URL 파일 확장명이 정책에서 허용되지 않음|
|920450|HTTP 헤더가 정책에서 제한됨|
|920470|잘못된 Content-Type 헤더|
|920480|요청 콘텐츠 형식 문자 집합이 정책에서 허용되지 않음|

### <a name="p-x-ms-format-detectionnoneprotocol-attackp"></a><a name="drs921-20"></a> <p x-ms-format-detection="none">PROTOCOL-ATTACK</p>

|RuleId|설명|
|---|---|
|921110|HTTP 요청 밀반입 공격|
|921120|HTTP 응답 분할 공격|
|921130|HTTP 응답 분할 공격|
|921140|헤더를 통한 HTTP 헤더 삽입 공격|
|921150|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 발견)|
|921151|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 발견)|
|921160|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 및 헤더-이름 발견)|

### <a name="p-x-ms-format-detectionnonelfi---local-file-inclusionp"></a><a name="drs930-20"></a> <p x-ms-format-detection="none">LFI - 로컬 파일 포함</p>
|RuleId|설명|
|---|---|
|930100|경로 탐색 공격(/../)|
|930110|경로 탐색 공격(/../)|
|930120|OS 파일 액세스 시도|
|930130|제한된 파일 액세스 시도|

### <a name="p-x-ms-format-detectionnonerfi---remote-file-inclusionp"></a><a name="drs931-20"></a> <p x-ms-format-detection="none">RFI - 원격 파일 포함</p>
|RuleId|설명|
|---|---|
|931100|가능한 RFI(원격 파일 포함) 공격: IP 주소를 사용하는 URL 매개 변수|
|931110|가능한 RFI(원격 파일 포함) 공격: 일반 RFI 취약 매개 변수 이름이 URL 페이로드에 사용됨|
|931120|가능한 RFI(원격 파일 포함) 공격: URL 페이로드가 후행 물음표 문자(?)에 사용됨|
|931130|가능한 RFI(원격 파일 포함) 공격: 오프-도메인 참조/링크|

### <a name="p-x-ms-format-detectionnonerce---remote-command-executionp"></a><a name="drs932-20"></a> <p x-ms-format-detection="none">RCE - 원격 명령 실행</p>
|RuleId|설명|
|---|---|
|932100|원격 명령 실행: Unix 명령 삽입|
|932105|원격 명령 실행: Unix 명령 삽입|
|932110|원격 명령 실행: Windows 명령 삽입|
|932115|원격 명령 실행: Windows 명령 삽입|
|932120|원격 명령 실행: Windows PowerShell 명령 발견|
|932130|원격 명령 실행: Unix 셸 식 발견|
|932140|원격 명령 실행: Windows FOR/IF 명령 발견|
|932150|원격 명령 실행: 직접 Unix 명령 실행|
|932160|원격 명령 실행: Unix 셸 코드 발견|
|932170|원격 명령 실행: Shellshock(CVE-2014-6271)|
|932171|원격 명령 실행: Shellshock(CVE-2014-6271)|
|932180|제한된 파일 업로드 시도|

### <a name="p-x-ms-format-detectionnonephp-attacksp"></a><a name="drs933-20"></a> <p x-ms-format-detection="none">PHP 공격</p>
|RuleId|설명|
|---|---|
|933100|PHP 삽입 공격: 여는/닫는 태그 발견|
|933110|PHP 삽입 공격: PHP 스크립트 파일 업로드 발견|
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
|933200|PHP 삽입 공격: 래퍼 체계가 검색됨|
|933210|PHP 삽입 공격: 변수 함수 호출 발견|

### <a name="p-x-ms-format-detectionnonenode-js-attacksp"></a><a name="drs934-20"></a> <p x-ms-format-detection="none">Node JS 공격</p>
|RuleId|설명|
|---|---|
|934100|Node.js 삽입 공격|

### <a name="p-x-ms-format-detectionnonexss---cross-site-scriptingp"></a><a name="drs941-20"></a> <p x-ms-format-detection="none">XSS - 교차 사이트 스크립팅</p>
|RuleId|설명|
|---|---|
|941100|libinjection을 통한 XSS 공격 감지됨|
|941101|libinjection을 통한 XSS 공격이 검색됨.|
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
|941360|JavaScript 난독 처리가 검색됨.|
|941370|JavaScript 전역 변수 발견|
|941380|AngularJS 클라이언트 쪽 템플릿 삽입이 검색됨|


### <a name="p-x-ms-format-detectionnonesqli---sql-injectionp"></a><a name="drs942-20"></a> <p x-ms-format-detection="none">SQLI - SQL 삽입</p>
|RuleId|설명|
|---|---|
|942100|libinjection을 통한 SQL 삽입 공격 감지됨|
|942110|SQL 삽입 공격: 일반적인 삽입 테스트 감지됨|
|942120|SQL 삽입 공격: SQL 연산자 감지됨|
|942130|SQL 삽입 공격: SQL Tautology 감지됨|
|942140|SQL 삽입 공격: 일반 DB 이름이 검색됨|
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
|942260|기본 SQL 인증 바이패스 시도 감지 2/3|
|942270|기본적인 sql 삽입 검색. mysql, oracle 등에 대한 일반적인 공격 문자열.|
|942280|Postgres pg_sleep 삽입, waitfor delay 공격 및 데이터베이스 종료 시도 감지|
|942290|기본적인 MongoDB SQL 삽입 시도 찾기|
|942300|MySQL 주석, 조건 및 ch(a)r 삽입이 검색됨|
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
|942430|제한된 SQL 문자 이상 감지(인수): 특수 문자 # 초과(12)|
|942440|SQL 주석 시퀀스가 감지됨.|
|942450|SQL 16진수 Encoding이 식별됨|
|942460|메타 문자 이상 감지 경고 - 반복적인 단어가 아닌 문자|
|942470|SQL 삽입 공격|
|942480|SQL 삽입 공격|
|942500|MySQL 인라인 주석이 검색되었습니다.|
|942510|틱 또는 백틱에 의한 SQLi 바이패스 시도가 검색되었습니다.|


### <a name="p-x-ms-format-detectionnonesession-fixationp"></a><a name="drs943-20"></a> <p x-ms-format-detection="none">SESSION-FIXATION</p>
|RuleId|설명|
|---|---|
|943100|가능한 세션 고정 공격: HTML의 쿠키 값 설정|
|943110|가능한 세션 고정 공격: 오프-도메인 참조 페이지가 있는 SessionID 매개 변수 이름|
|943120|가능한 세션 고정 공격: 참조 페이지가 없는 SessionID 매개 변수 이름|

### <a name="p-x-ms-format-detectionnonejava-attacksp"></a><a name="drs944-20"></a> <p x-ms-format-detection="none">JAVA 공격</p>
|RuleId|설명|
|---|---|
|944100|원격 명령 실행: Apache Struts, Oracle WebLogic|
|944110|잠재적 페이로드 실행 검색|
|944120|가능한 페이로드 실행 및 원격 명령 실행|
|944130|의심스러운 Java 클래스|
|944200|Java 역직렬화 Apache Commons 악용|
|944210|Java serialization을 사용할 수 있음|
|944240|원격 명령 실행: Java serialization|
|944250|원격 명령 실행: 의심스러운 Java 메서드가 검색됨|

### <a name="p-x-ms-format-detectionnonems-threatintel-webshellsp"></a><a name="drs9905-20"></a> <p x-ms-format-detection="none">MS-ThreatIntel-WebShells</p>
|RuleId|설명|
|---|---|
|99005002|웹 셸 상호 작용 시도(POST)|
|99005003|웹 셸 업로드 시도(POST) - CHOPPER PHP|
|99005004|웹 셸 업로드 시도(POST) - CHOPPER ASPX|

### <a name="p-x-ms-format-detectionnonems-threatintel-appsecp"></a><a name="drs9903-20"></a> <p x-ms-format-detection="none">MS-ThreatIntel-AppSec</p>
|RuleId|설명|
|---|---|
|99030001|헤더의 경로 통과 우회(/.././../)|
|99030002|요청 본문의 경로 통과 우회(/.././../)|

### <a name="p-x-ms-format-detectionnonems-threatintel-sqlip"></a><a name="drs99031-20"></a> <p x-ms-format-detection="none">MS-ThreatIntel-SQLI</p>
|RuleId|설명|
|---|---|
|99031001|SQL 삽입 공격: 일반적인 삽입 테스트 감지됨|
|99031002|SQL 주석 시퀀스가 감지됨.|

### <a name="p-x-ms-format-detectionnonems-threatintel-cvesp"></a><a name="drs99001-20"></a> <p x-ms-format-detection="none">MS-ThreatIntel-CVEs</p>
|RuleId|설명|
|---|---|
|99001001|알려진 자격 증명으로 F5 tmui(CVE-2020-5902) REST API 악용 시도|

# <a name="drs-11"></a>[DRS 1.1](#tab/drs11)

## <a name="11-rule-sets"></a><a name="drs11"></a> 1.1 규칙 집합

### <a name="p-x-ms-format-detectionnoneprotocol-attackp"></a><a name="drs921-11"></a> <p x-ms-format-detection="none">PROTOCOL-ATTACK</p>
|RuleId|설명|
|---|---|
|921110|HTTP 요청 밀반입 공격|
|921120|HTTP 응답 분할 공격|
|921130|HTTP 응답 분할 공격|
|921140|헤더를 통한 HTTP 헤더 삽입 공격|
|921150|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 발견)|
|921151|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 발견)|
|921160|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 및 헤더-이름 발견)|

### <a name="p-x-ms-format-detectionnonelfi---local-file-inclusionp"></a><a name="drs930-11"></a> <p x-ms-format-detection="none">LFI - 로컬 파일 포함</p>
|RuleId|설명|
|---|---|
|930100|경로 탐색 공격(/../)|
|930110|경로 탐색 공격(/../)|
|930120|OS 파일 액세스 시도|
|930130|제한된 파일 액세스 시도|

### <a name="p-x-ms-format-detectionnonerfi---remote-file-inclusionp"></a><a name="drs931-11"></a> <p x-ms-format-detection="none">RFI - 원격 파일 포함</p>
|RuleId|설명|
|---|---|
|931100|가능한 RFI(원격 파일 포함) 공격: IP 주소를 사용하는 URL 매개 변수|
|931110|가능한 RFI(원격 파일 포함) 공격: 일반 RFI 취약 매개 변수 이름이 URL 페이로드에 사용됨|
|931120|가능한 RFI(원격 파일 포함) 공격: URL 페이로드가 후행 물음표 문자(?)에 사용됨|
|931130|가능한 RFI(원격 파일 포함) 공격: 오프-도메인 참조/링크|

### <a name="p-x-ms-format-detectionnonerce---remote-command-executionp"></a><a name="drs932-11"></a> <p x-ms-format-detection="none">RCE - 원격 명령 실행</p>
|RuleId|설명|
|---|---|
|932100|원격 명령 실행: Unix 명령 삽입|
|932105|원격 명령 실행: Unix 명령 삽입|
|932110|원격 명령 실행: Windows 명령 삽입|
|932115|원격 명령 실행: Windows 명령 삽입|
|931120|원격 명령 실행: Windows PowerShell 명령 발견|
|932130|원격 명령 실행: Unix 셸 식 발견|
|932140|원격 명령 실행: Windows FOR/IF 명령 발견|
|932150|원격 명령 실행: 직접 Unix 명령 실행|
|932160|원격 명령 실행: Shellshock(CVE-2014-6271)|
|932170|원격 명령 실행: Shellshock(CVE-2014-6271)|
|932171|원격 명령 실행: Shellshock(CVE-2014-6271)|
|932180|제한된 파일 업로드 시도|

### <a name="p-x-ms-format-detectionnonephp-attacksp"></a><a name="drs933-11"></a> <p x-ms-format-detection="none">PHP 공격</p>
|RuleId|설명|
|---|---|
|933100|PHP 삽입 공격: PHP 여는 태그 발견|
|933110|PHP 삽입 공격: PHP 스크립트 파일 업로드 발견|
|933120|PHP 삽입 공격: 구성 지시문 발견|
|933130|PHP 삽입 공격: 변수 발견|
|933140|PHP 삽입 공격: I/O 스트림 발견|
|933150|PHP 삽입 공격: 고위험 PHP 함수 이름 발견|
|933151|PHP 삽입 공격: 보통 위험 PHP 함수 이름 발견|
|933160|PHP 삽입 공격: 고위험 PHP 함수 호출 발견|
|933170|PHP 삽입 공격: 직렬화된 개체 삽입|
|933180|PHP 삽입 공격: 변수 함수 호출 발견|

### <a name="p-x-ms-format-detectionnonexss---cross-site-scriptingp"></a><a name="drs941-11"></a> <p x-ms-format-detection="none">XSS - 교차 사이트 스크립팅</p>
|RuleId|설명|
|---|---|
|941100|libinjection을 통한 XSS 공격 감지됨|
|941101|libinjection을 통한 XSS 공격 감지됨|
|941110|XSS 필터 - 범주 1: 스크립트 태그 벡터|
|941120|XSS 필터 - 범주 2: 이벤트 처리기 벡터|
|941130|XSS 필터 - 범주 3: 특성 벡터|
|941140|XSS 필터 - 범주 4: JavaScript URI 벡터|
|941150|XSS 필터 - 범주 5: 허용되지 않는 HTML 특성|
|941160|NoScript XSS InjectionChecker: HTML 삽입|
|941170|NoScript XSS InjectionChecker: 특성 삽입|
|941180|노드 검사기 블랙리스트 키워드|
|941190|IE XSS 필터 - 공격 감지됨.|
|941200|IE XSS 필터 - 공격 감지됨.|
|941210|IE XSS 필터 - 공격 감지됨.|
|941220|IE XSS 필터 - 공격 감지됨.|
|941230|IE XSS 필터 - 공격 감지됨.|
|941240|IE XSS 필터 - 공격 감지됨.|
|941250|IE XSS 필터 - 공격 감지됨.|
|941260|IE XSS 필터 - 공격 감지됨.|
|941270|IE XSS 필터 - 공격 감지됨.|
|941280|IE XSS 필터 - 공격 감지됨.|
|941290|IE XSS 필터 - 공격 감지됨.|
|941300|IE XSS 필터 - 공격 감지됨.|
|941310|US-ASCII 잘못된 형식의 Encoding XSS 필터 - 공격 감지됨.|
|941320|가능한 XSS 공격 감지됨 - HTML 태그 처리기|
|941330|IE XSS 필터 - 공격 감지됨.|
|941340|IE XSS 필터 - 공격 감지됨.|
|941350|UTF-7 Encoding IE XSS - 공격 감지됨.|

### <a name="p-x-ms-format-detectionnonesqli---sql-injectionp"></a><a name="drs942-11"></a> <p x-ms-format-detection="none">SQLI - SQL 삽입</p>
|RuleId|설명|
|---|---|
|942100|libinjection을 통한 SQL 삽입 공격 감지됨|
|942110|SQL 삽입 공격: 일반적인 삽입 테스트 감지됨|
|942120|SQL 삽입 공격: SQL 연산자 감지됨|
|942140|SQL 삽입 공격: 일반 DB 이름이 검색됨|
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
|942260|기본 SQL 인증 바이패스 시도 감지 2/3|
|942270|기본적인 sql 삽입 검색. mysql, oracle 등에 대한 일반적인 공격 문자열.|
|942280|Postgres pg_sleep 삽입, waitfor delay 공격 및 데이터베이스 종료 시도 감지|
|942290|기본적인 MongoDB SQL 삽입 시도 찾기|
|942300|MySQL 주석, 조건 및 ch(a)r 삽입 감지|
|942310|연결된 SQL 삽입 시도 감지 2/2|
|942320|MySQL 및 PostgreSQL에 저장된 저장 프로시저/함수 삽입 감지|
|942330|클래식 SQL 삽입 프로빙 검색 1/3|
|942340|기본 SQL 인증 바이패스 시도 감지 3/3|
|942350|MySQL UDF 삽입 및 기타 데이터/구조 조작 시도 감지|
|942360|연결된 기본 SQL 삽입 및 SQLLFI 시도 감지|
|942361|키워드 alter 또는 union을 기준으로 기본 SQL 삽입 감지|
|942370|클래식 SQL 삽입 프로빙 검색 2/3|
|942380|SQL 삽입 공격|
|942390|SQL 삽입 공격|
|942400|SQL 삽입 공격|
|942410|SQL 삽입 공격|
|942430|제한된 SQL 문자 이상 감지(인수): 특수 문자 # 초과(12)|
|942440|SQL 주석 시퀀스가 감지됨.|
|942450|SQL 16진수 Encoding이 식별됨|
|942470|SQL 삽입 공격|
|942480|SQL 삽입 공격|

### <a name="p-x-ms-format-detectionnonesession-fixationp"></a><a name="drs943-11"></a> <p x-ms-format-detection="none">SESSION-FIXATION</p>
|RuleId|설명|
|---|---|
|943100|가능한 세션 고정 공격: HTML의 쿠키 값 설정|
|943110|가능한 세션 고정 공격: 오프-도메인 참조 페이지가 있는 SessionID 매개 변수 이름|
|943120|가능한 세션 고정 공격: 참조 페이지가 없는 SessionID 매개 변수 이름|

### <a name="p-x-ms-format-detectionnonejava-attacksp"></a><a name="drs944-11"></a> <p x-ms-format-detection="none">JAVA 공격</p>
|RuleId|설명|
|---|---|
|944100|원격 명령 실행: 의심스러운 Java 클래스가 검색됨|
|944110|가능한 세션 고정 공격: HTML의 쿠키 값 설정|
|944120|원격 명령 실행: Java serialization(CVE-2015-5842)|
|944130|의심스러운 Java 클래스가 검색됨|
|944200|매직 바이트가 검색됨, 가능한 java serialization 사용 중|
|944210|Base64로 인코딩된 매직 바이트가 검색됨, 가능한 java serialization 사용 중|
|944240|원격 명령 실행: Java serialization(CVE-2015-5842)|
|944250|원격 명령 실행: 의심스러운 Java 메서드가 검색됨|

### <a name="p-x-ms-format-detectionnonems-threatintel-webshellsp"></a><a name="drs9905-11"></a> <p x-ms-format-detection="none">MS-ThreatIntel-WebShells</p>
|RuleId|설명|
|---|---|
|99005002|웹 셸 상호 작용 시도(POST)|
|99005003|웹 셸 업로드 시도(POST) - CHOPPER PHP|
|99005004|웹 셸 업로드 시도(POST) - CHOPPER ASPX|

### <a name="p-x-ms-format-detectionnonems-threatintel-appsecp"></a><a name="drs9903-11"></a> <p x-ms-format-detection="none">MS-ThreatIntel-AppSec</p>
|RuleId|설명|
|---|---|
|99030001|헤더의 경로 통과 우회(/.././../)|
|99030002|요청 본문의 경로 통과 우회(/.././../)|

### <a name="p-x-ms-format-detectionnonems-threatintel-sqlip"></a><a name="drs99031-11"></a> <p x-ms-format-detection="none">MS-ThreatIntel-SQLI</p>
|RuleId|설명|
|---|---|
|99031001|SQL 삽입 공격: 일반적인 삽입 테스트 감지됨|
|99031002|SQL 주석 시퀀스가 감지됨.|

### <a name="p-x-ms-format-detectionnonems-threatintel-cvesp"></a><a name="drs99001-11"></a> <p x-ms-format-detection="none">MS-ThreatIntel-CVEs</p>
|RuleId|설명|
|---|---|
|99001001|알려진 자격 증명으로 F5 tmui(CVE-2020-5902) REST API 악용 시도|

# <a name="drs-10"></a>[DRS 1.0](#tab/drs10)

## <a name="10-rule-sets"></a><a name="drs10"></a> 1.0 규칙 집합

### <a name="p-x-ms-format-detectionnoneprotocol-attackp"></a><a name="drs921-10"></a> <p x-ms-format-detection="none">PROTOCOL-ATTACK</p>
|RuleId|설명|
|---|---|
|921110|HTTP 요청 밀반입 공격|
|921120|HTTP 응답 분할 공격|
|921130|HTTP 응답 분할 공격|
|921140|헤더를 통한 HTTP 헤더 삽입 공격|
|921150|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 발견)|
|921151|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 발견)|
|921160|페이로드를 통한 HTTP 헤더 삽입 공격(CR/LF 및 헤더-이름 발견)|

### <a name="p-x-ms-format-detectionnonelfi---local-file-inclusionp"></a><a name="drs930-10"></a> <p x-ms-format-detection="none">LFI - 로컬 파일 포함</p>
|RuleId|설명|
|---|---|
|930100|경로 탐색 공격(/../)|
|930110|경로 탐색 공격(/../)|
|930120|OS 파일 액세스 시도|
|930130|제한된 파일 액세스 시도|

### <a name="p-x-ms-format-detectionnonerfi---remote-file-inclusionp"></a><a name="drs931-10"></a> <p x-ms-format-detection="none">RFI - 원격 파일 포함</p>
|RuleId|설명|
|---|---|
|931100|가능한 RFI(원격 파일 포함) 공격: IP 주소를 사용하는 URL 매개 변수|
|931110|가능한 RFI(원격 파일 포함) 공격: 일반 RFI 취약 매개 변수 이름이 URL 페이로드에 사용됨|
|931120|가능한 RFI(원격 파일 포함) 공격: URL 페이로드가 후행 물음표 문자(?)에 사용됨|
|931130|가능한 RFI(원격 파일 포함) 공격: 오프-도메인 참조/링크|

### <a name="p-x-ms-format-detectionnonerce---remote-command-executionp"></a><a name="drs932-10"></a> <p x-ms-format-detection="none">RCE - 원격 명령 실행</p>
|RuleId|설명|
|---|---|
|932100|원격 명령 실행: Unix 명령 삽입|
|932105|원격 명령 실행: Unix 명령 삽입|
|932110|원격 명령 실행: Windows 명령 삽입|
|932115|원격 명령 실행: Windows 명령 삽입|
|932120|원격 명령 실행: Windows PowerShell 명령 발견|
|932130|원격 명령 실행: Unix 셸 식 발견|
|932140|원격 명령 실행: Windows FOR/IF 명령 발견|
|932150|원격 명령 실행: 직접 Unix 명령 실행|
|932160|원격 명령 실행: Unix 셸 코드 발견|
|932170|원격 명령 실행: Shellshock(CVE-2014-6271)|
|932171|원격 명령 실행: Shellshock(CVE-2014-6271)|
|932180|제한된 파일 업로드 시도|

### <a name="p-x-ms-format-detectionnonephp-attacksp"></a><a name="drs933-10"></a> <p x-ms-format-detection="none">PHP 공격</p>
|RuleId|설명|
|---|---|
|933100|PHP 삽입 공격: 여는/닫는 태그 발견|
|933110|PHP 삽입 공격: PHP 스크립트 파일 업로드 발견|
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

### <a name="p-x-ms-format-detectionnonexss---cross-site-scriptingp"></a><a name="drs941-10"></a> <p x-ms-format-detection="none">XSS - 교차 사이트 스크립팅</p>
|RuleId|설명|
|---|---|
|941100|libinjection을 통한 XSS 공격 감지됨|
|941101|libinjection을 통한 XSS 공격이 검색됨.|
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

### <a name="p-x-ms-format-detectionnonesqli---sql-injectionp"></a><a name="drs942-10"></a> <p x-ms-format-detection="none">SQLI - SQL 삽입</p>
|RuleId|설명|
|---|---|
|942100|libinjection을 통한 SQL 삽입 공격 감지됨|
|942110|SQL 삽입 공격: 일반적인 삽입 테스트 감지됨|
|942120|SQL 삽입 공격: SQL 연산자 감지됨|
|942140|SQL 삽입 공격: 일반 DB 이름이 검색됨|
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
|942260|기본 SQL 인증 바이패스 시도 감지 2/3|
|942270|기본적인 sql 삽입 검색. mysql, oracle 등에 대한 일반적인 공격 문자열.|
|942280|Postgres pg_sleep 삽입, waitfor delay 공격 및 데이터베이스 종료 시도 감지|
|942290|기본적인 MongoDB SQL 삽입 시도 찾기|
|942300|MySQL 주석, 조건 및 ch(a)r 삽입이 검색됨|
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
|942430|제한된 SQL 문자 이상 감지(인수): 특수 문자 # 초과(12)|
|942440|SQL 주석 시퀀스가 감지됨.|
|942450|SQL 16진수 Encoding이 식별됨|
|942470|SQL 삽입 공격|
|942480|SQL 삽입 공격|

### <a name="p-x-ms-format-detectionnonesession-fixationp"></a><a name="drs943-10"></a> <p x-ms-format-detection="none">SESSION-FIXATION</p>
|RuleId|설명|
|---|---|
|943100|가능한 세션 고정 공격: HTML의 쿠키 값 설정|
|943110|가능한 세션 고정 공격: 오프-도메인 참조 페이지가 있는 SessionID 매개 변수 이름|
|943120|가능한 세션 고정 공격: 참조 페이지가 없는 SessionID 매개 변수 이름|

### <a name="p-x-ms-format-detectionnonejava-attacksp"></a><a name="drs944-10"></a> <p x-ms-format-detection="none">JAVA 공격</p>
|RuleId|설명|
|---|---|
|944100|원격 명령 실행: Apache Struts, Oracle WebLogic|
|944110|잠재적 페이로드 실행 검색|
|944120|가능한 페이로드 실행 및 원격 명령 실행|
|944130|의심스러운 Java 클래스|
|944200|Java 역직렬화 Apache Commons 악용|
|944210|Java serialization을 사용할 수 있음|
|944240|원격 명령 실행: Java serialization|
|944250|원격 명령 실행: 의심스러운 Java 메서드가 검색됨|

# <a name="bot-rules"></a>[봇 규칙](#tab/bot)

## <a name="bot-manager-rule-sets"></a><a name="bot"></a> 봇 관리자 규칙 집합

### <a name="p-x-ms-format-detectionnonebad-botsp"></a><a name="bot100"></a> <p x-ms-format-detection="none">잘못된 봇</p>
|RuleId|설명|
|---|---|
|Bot100100|위협 인텔리전스에서 악의적인 봇이 검색됨|
|Bot100200|ID를 위조한 악의적인 봇|

### <a name="p-x-ms-format-detectionnonegood-botsp"></a><a name="bot200"></a> <p x-ms-format-detection="none">양호한 봇</p>
|RuleId|설명|
|---|---|
|Bot200100|검색 엔진 크롤러|
|Bot200200|확인되지 않은 검색 엔진 크롤러|

### <a name="p-x-ms-format-detectionnoneunknown-botsp"></a><a name="bot300"></a> <p x-ms-format-detection="none">알 수 없는 봇</p>
|RuleId|설명|
|---|---|
|Bot300100|지정되지 않은 ID|
|Bot300200|웹 크롤링 및 공격을 위한 도구 및 프레임워크|
|Bot300300|범용 HTTP 클라이언트 및 SDK|
|Bot300400|서비스 에이전트|
|Bot300500|사이트 상태 모니터링 서비스|
|Bot300600|위협 인텔리전스에서 알 수 없는 봇이 검색됨|
|Bot300700|기타 봇|

---


## <a name="next-steps"></a>다음 단계

- [Azure Front Door를 사용하는 Web Application Firewall의 사용자 지정 규칙](waf-front-door-custom-rules.md)
