---
title: Azure Front Door용 Azure 웹 애플리케이션 방화벽이란?
description: Azure Front Door 서비스의 Azure 웹 애플리케이션 방화벽이 악의적인 공격으로부터 웹 애플리케이션을 보호하는 방법을 알아봅니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 06/09/2021
ms.author: victorh
ms.openlocfilehash: 09c184f9f3e62d8f26a2baaaa3fb31f06f4dbfca
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292911"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Azure Front Door의 Azure 웹 애플리케이션 방화벽

Azure Front Door의 Azure WAF(웹 애플리케이션 방화벽)는 웹 애플리케이션을 중앙 집중식으로 보호합니다. WAF는 일반적인 악용 및 취약성으로부터 웹 서비스를 보호합니다. 사용자의 서비스 가용성을 높게 유지하고 규정 준수 요구 사항을 충족하는 데 유용합니다.

Front Door의 WAF는 글로벌 중앙 집중식 솔루션입니다. 전 세계 Azure 네트워크 에지 위치에 배포됩니다. WAF를 사용하도록 설정된 웹 애플리케이션은 네트워크 에지에서 Front Door가 전달하는 수신 요청을 모두 검사합니다. 

WAF는 공격 소스에 가까운 악의적인 공격이 가상 네트워크에 침입하기 전에 차단합니다. 성능을 저하시키지 않으면서 대규모로 글로벌 보호를 받을 수 있습니다. WAF 정책은 구독의 Front Door 프로필에 쉽게 연결됩니다. 몇 분 내에 새로운 규칙을 배포할 수 있기 때문에 변화하는 위협 패턴에 신속하게 대응할 수 있습니다.

![Azure 웹 애플리케이션 방화벽](../media/overview/wafoverview.png)

Azure Front Door는 Front Door Standard 및 Front Door Premium SKU의 [두 가지 새로운 SKU를 미리 보기](../../frontdoor/standard-premium/overview.md)로 소개합니다. WAF는 기본적으로 모든 기능을 갖춘 Front Door Premium SKU와 통합됩니다. Front Door Standard SKU의 경우 [사용자 지정 규칙](#custom-authored-rules)만 지원됩니다.

## <a name="waf-policy-and-rules"></a>WAF 정책 및 규칙

[WAF 정책](waf-front-door-create-portal.md)을 구성하고 보호를 위해 하나 이상의 Front Door 프런트 엔드에 해당 정책을 연결할 수 있습니다. WAF 정책은 다음 두 가지 유형의 보안 규칙으로 구성됩니다.

- 고객이 작성한 사용자 지정 규칙

- Azure에서 관리하는 미리 구성된 규칙 세트의 컬렉션에 해당하는 관리형 규칙 세트

두 규칙이 모두 존재하는 경우 관리형 규칙 세트의 규칙을 처리하기 전에 사용자 지정 규칙이 처리됩니다. 규칙은 일치 조건, 우선 순위 및 작업으로 구성됩니다. 지원되는 작업 유형은 허용, 차단, 로그 및 리디렉션입니다. 관리형 규칙과 사용자 지정 규칙을 결합하여 특정 애플리케이션 보호 요구 사항을 충족하는 완전히 사용자 지정된 정책을 만들 수 있습니다.

정책 내 규칙은 우선 순위에 따라 처리됩니다. 우선 순위는 처리할 규칙의 순서를 정의하는 고유한 정수입니다. 정수 값이 작을수록 우선 순위가 높고 이러한 규칙은 정수 값이 높은 규칙보다 먼저 평가됩니다. 규칙이 일치하면 규칙에 정의된 해당 작업이 요청에 적용됩니다. 이러한 일치가 처리되면 우선 순위가 낮은 규칙은 더 이상 처리되지 않습니다.

Front Door가 제공하는 웹 애플리케이션에는 한 번에 하나의 WAF 정책만 연결될 수 있습니다. 그러나 WAF 정책이 연결되지 않은 Front Door 구성도 사용할 수 있습니다. WAF 정책이 있으면 전 세계적으로 일관된 보안 정책을 보장하기 위해 모든 에지 위치에 복제됩니다.

## <a name="waf-modes"></a>WAF 모드

WAF 정책은 다음 두 가지 모드에서 실행되도록 구성할 수 있습니다.

- **감지 모드:** 감지 모드로 실행하면 WAF는 모니터링 이외의 다른 작업을 수행하지 않고, 요청 및 일치하는 WAF 규칙을 WAF 로그에 기록합니다. Front Door에 대한 로깅 진단 기능을 켤 수 있습니다. 포털을 사용하는 경우 **진단** 섹션으로 이동하세요.

- **방지 모드:** 방지 모드에서는 요청이 규칙과 일치하면 WAF가 지정된 작업을 수행합니다. 일치하는 항목이 발견되면 우선 순위가 낮은 규칙이 더 이상 평가되지 않습니다. 일치하는 모든 요청은 WAF 로그에도 기록됩니다.

## <a name="waf-actions"></a>WAF 작업

WAF 고객은 요청이 규칙의 조건과 일치하는 경우 작업 중 하나를 실행하도록 선택할 수 있습니다.

- **허용:**  요청은 WAF를 통과해서 백 엔드에 전달됩니다. 더 낮은 우선 순위 규칙으로는 이 요청을 차단할 수 없습니다.
- **차단:** 요청이 차단되고 WAF는 요청을 백 엔드에 전달하지 않고 클라이언트에 응답을 보냅니다.
- **로그:**  요청은 WAF 로그에 기록되고 WAF는 우선 순위가 낮은 규칙을 계속 평가합니다.
- **리디렉션:** WAF는 요청을 지정된 URI로 리디렉션합니다. 지정된 URI가 정책 수준 설정입니다. 이 옵션이 구성되면 **리디렉션** 작업과 일치하는 모든 요청이 해당 URI로 전송됩니다.

## <a name="waf-rules"></a>WAF 규칙

WAF 정책은 두 가지 유형의 보안 규칙으로 구성될 수 있습니다. 하나는 고객이 작성하는 사용자 지정 규칙이고, 다른 하나는 Azure 관리형 미리 구성된 규칙세트를 나타내는 관리형 규칙 세트입니다.

### <a name="custom-authored-rules"></a>사용자 작성 규칙

다음과 같이 사용자 지정 규칙 WAF를 구성할 수 있습니다.

- **IP 허용 목록 및 차단 목록:** 클라이언트 IP 주소 또는 IP 주소 범위에 따라 웹 애플리케이션에 대한 액세스를 제어할 수 있습니다. IPv4 및 IPv6 주소 형식이 모두 지원됩니다. 이 목록은 원본 IP가 목록의 IP와 일치하는 요청을 차단하거나 허용하도록 구성할 수 있습니다.

- **지리 기반 액세스 제어:** 클라이언트의 IP 주소와 연결된 국가 코드를 기반으로 웹 애플리케이션에 대한 액세스를 제어할 수 있습니다.

- **HTTP 매개 변수 기반 액세스 제어:** HTTP/HTTPS 요청 매개 변수에서 문자열 일치를 기반으로 규칙을 만들 수 있습니다.  예:쿼리 문자열, POST 인수, 요청 URI, 요청 헤더 및 요청 본문.

- **요청 메서드 기반 액세스 제어**: 요청의 HTTP 요청 메서드에 대한 규칙을 기반으로 합니다. 예: GET, PUT, 또는 HEAD.

- **크기 제약 조건:** 쿼리 문자열, URI 또는 요청 본문과 같은 요청의 특정 부분 길이를 기반으로 규칙을 만들 수 있습니다.

- **속도 제한 규칙**: 속도 제어 규칙은 모든 클라이언트 IP 주소에서 오는 비정상적으로 높은 트래픽을 제한합니다. 1분 동안 클라이언트 IP에서 허용하는 웹 요청 수에 대한 임계값을 구성할 수 있습니다. 이 규칙은 클라이언트 IP의 모든 요청을 허용하거나 차단하는 IP 목록 기반 허용/차단 사용자 지정 규칙과는 다릅니다. 속도 제한은 세부적인 속도 제어를 위해 HTTP(S) 매개 변수 일치와 같은 추가 일치 조건과 결합할 수 있습니다.

### <a name="azure-managed-rule-sets"></a>Azure 관리형 규칙 세트

Azure 관리형 규칙 세트는 일반적인 보안 위협에 대한 보호를 쉽게 배포할 수 있는 방법을 제공합니다. 이러한 규칙 세트는 Azure에서 관리되므로 새로운 공격 서명으로부터 보호하기 위해 필요에 따라 규칙을 업데이트합니다. Azure 관리형 기본 규칙 세트에는 다음 위협 범주에 대한 규칙이 포함됩니다.

- 사이트 간 스크립팅
- Java 공격
- 로컬 파일 포함
- PHP 삽입 공격
- 원격 명령 실행
- 원격 파일 포함
- 세션 고정
- SQL 삽입 공격 보호
- 프로토콜 공격자

사용자 지정 규칙은 항상 기본 규칙 세트의 규칙을 평가하기 전에 적용됩니다. 요청이 사용자 지정 규칙과 일치하면 해당 규칙 동작이 적용됩니다. 요청이 차단되거나 백 엔드로 전달됩니다. 다른 사용자 지정 규칙이나 기본 규칙 세트의 규칙은 처리되지 않습니다. WAF 정책에서 기본 규칙 세트를 제거할 수도 있습니다.

자세한 내용은 [Web Application Firewall DRS 규칙 그룹 및 규칙](waf-front-door-drs.md)을 참조하세요.


### <a name="bot-protection-rule-set-preview"></a>봇 보호 규칙 세트(미리 보기)

알려진 봇 범주의 요청에 대해 사용자 지정 작업을 수행하도록 관리형 봇 보호 규칙 세트를 설정할 수 있습니다. 

지원되는 세 가지 봇 범주는 불량, 양호 및 알 수 없음입니다. Bot 서명은 WAF 플랫폼에 의해 관리되고 동적으로 업데이트됩니다.

불량 봇에는 악성 IP 주소의 봇과 신원을 위조한 봇이 포함됩니다. 악성 IP 주소는 Microsoft 위협 인텔리전스 피드에서 제공되며 1시간마다 업데이트됩니다. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) microsoft 위협 인텔리전스를 구동 하 고 클라우드 용 microsoft Defender를 비롯 한 여러 서비스에서 사용 됩니다.

올바른 봇에는 유효성 검사가 완료된 검색 엔진이 포함됩니다. 알 수 없는 범주에는 자신을 봇으로 식별한 추가 봇 그룹이 포함됩니다. 예: 시장 분석기, 피드 페처(fetcher) 및 데이터 수집 에이전트. 

알 수 없는 봇은 추가적인 유효성 검사 없이 게시된 사용자 에이전트를 통해 분류됩니다. 다양한 종류의 봇을 차단, 허용, 기록 또는 리디렉션하도록 사용자 지정 작업을 설정할 수 있습니다.

![봇 보호 규칙 세트](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> 봇 보호 규칙 세트는 현재 공개 미리 보기이며 미리 보기 서비스 수준 계약과 함께 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.  자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

봇 보호를 사용하면 봇 규칙과 일치하는 수신 요청이 FrontdoorWebApplicationFirewallLog 로그에 기록됩니다. 스토리지 계정, 이벤트 허브 또는 로그 분석에서 WAF 로그에 액세스할 수 있습니다.

## <a name="configuration"></a>구성

Azure Portal, REST API, Azure Resource Manager 템플릿 및 Azure PowerShell을 사용하여 모든 WAF 규칙 유형을 구성하고 배포할 수 있습니다.

## <a name="monitoring"></a>모니터링

Front Door의 WAF 모니터링은 경고를 추적하고 트래픽 추세를 쉽게 모니터링 하기 위해 Azure Monitor와 연결됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Application Gateway의 웹 애플리케이션 방화벽](../ag/ag-overview.md)에 대해 자세히 알아보기
