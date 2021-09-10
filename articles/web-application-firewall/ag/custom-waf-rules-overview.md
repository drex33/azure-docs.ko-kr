---
title: Application Gateway에 대한 Azure WAF(Web Application Firewall) v2 사용자 지정 규칙
description: 이 문서에서는 Azure Application Gateway의 WAF(Web Application Firewall) v2 사용자 지정 규칙에 대한 개요를 제공합니다.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 07/30/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ad73d319e5466bb541a99e3ef33b17cdc644dfe5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566459"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Azure Application Gateway의 Web Application Firewall v2에 대한 사용자 지정 규칙

Azure Application Gateway WAF(Web Application Firewall) v2는 다양한 유형의 공격으로부터 보호하는 미리 구성된 플랫폼 관리 규칙 집합을 제공합니다. 이러한 공격에는 사이트 간 스크립팅, SQL 삽입 및 기타 기능이 포함됩니다. WAF 관리자인 경우 사용자 고유의 규칙을 작성하여 CRS(핵심 규칙 집합) 규칙을 강화할 수 있습니다. 규칙은 일치 조건에 따라 요청된 트래픽을 차단하거나 허용할 수 있습니다.

사용자 지정 규칙을 사용하면 WAF를 통과하는 각 요청에 대해 평가된 고유한 규칙을 만들 수 있습니다. 이러한 규칙은 관리형 규칙 세트의 나머지 규칙보다 높은 우선 순위를 갖습니다. 사용자 지정 규칙에는 규칙 이름, 규칙 우선 순위 및 일치 조건의 배열이 포함되어 있습니다. 이러한 조건이 충족되면 작업이 허용 또는 차단됩니다.

예를 들어 192.168.5.4/24 범위의 IP 주소에서 모든 요청을 차단할 수 있습니다. 이 규칙에서 연산자는 *IPMatch* 이고 matchValues는 IP 주소 범위(192.168.5.4/24)이며, 작업은 트래픽을 차단하는 것입니다. 또한 규칙의 이름 및 우선 순위를 설정합니다.

사용자 지정 규칙은 복합 논리를 사용하여 보안 요구 사항을 해결하는 고급 규칙을 지원합니다. 예를 들면 ((Condition 1 **및** Condition 2) **또는** Condition 3)입니다. 즉, Condition 1 **및** Condition 2가 충족되는 경우 **또는** Condition 3이 충족되면 WAF는 사용자 지정 규칙에 지정된 작업을 수행해야 합니다.

동일한 규칙 내의 서로 다른 일치 조건은 항상 **및** 을 사용하여 복합됩니다. 예를 들어 특정 IP 주소에서 트래픽을 차단하고, 특정 브라우저를 사용하는 경우에만 트래픽을 차단합니다.

서로 다른 두 조건에서 **또는** 을 사용하려는 경우 두 조건이 다른 규칙에 있어야 합니다. 예를 들어 특정 IP 주소에서 트래픽을 차단하거나 특정 브라우저를 사용하는 경우 트래픽을 차단합니다.

> [!NOTE]
> WAF 사용자 지정 규칙의 최대 수는 100입니다. Application Gateway 제한 사항에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)을 참조하세요.

정규식은 CRS 규칙 집합에서와 마찬가지로 사용자 지정 규칙에서도 지원됩니다. 예를 들어 [사용자 지정 웹 애플리케이션 방화벽 규칙 만들기 및 사용](create-custom-waf-rules.md)의 예제 3 및 5를 참조하세요.

## <a name="allowing-vs-blocking"></a>허용 및 차단

사용자 지정 규칙을 사용하여 트래픽을 허용하고 차단하는 것은 간단합니다. 예를 들어 IP 주소 범위에서 들어오는 모든 트래픽을 차단할 수 있습니다. 특정 브라우저에서 요청을 가져오는 경우 트래픽을 허용하는 다른 규칙을 만들 수 있습니다.

항목을 허용하려면 `-Action` 매개 변수가 **허용** 으로 설정되어 있는지 확인합니다. 항목을 차단하려면 `-Action` 매개 변수가 **차단** 으로 설정되어 있는지 확인합니다.

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

이전 `$BlockRule`은 Azure Resource Manager의 다음 사용자 지정 규칙에 매핑됩니다.

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationCondition": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

이 사용자 지정 규칙에는 작업을 수행하기 위해 충족해야 하는 이름, 우선 순위, 동작 및 일치 조건의 배열이 포함되어 있습니다. 이러한 필드에 대한 자세한 설명은 다음 필드 설명을 참조하세요. 예제 사용자 지정 규칙은 [사용자 지정 웹 애플리케이션 방화벽 규칙 만들기 및 사용](create-custom-waf-rules.md)을 참조하세요.

## <a name="fields-for-custom-rules"></a>사용자 지정 규칙에 대한 필드

### <a name="name-optional"></a>이름[선택 사항]

규칙의 이름입니다.  로그에 표시됩니다.

### <a name="priority-required"></a>우선 순위[필수]

- 규칙의 평가 순서를 결정합니다. 값이 낮을수록 규칙의 평가가 빠릅니다. 허용되는 범위는 1-100입니다. 
- 모든 사용자 지정 규칙에서 고유해야 합니다. 우선 순위가 40인 규칙은 우선 순위가 80인 규칙보다 먼저 평가됩니다.

### <a name="rule-type-required"></a>규칙 유형[필수]

현재는 **MatchRule** 이어야 합니다.

### <a name="match-variable-required"></a>일치 변수[필수]

다음 변수 중 하나여야 합니다.

- RemoteAddr – 원격 컴퓨터 연결의 IP 주소/범위
- RequestMethod – HTTP 요청 메서드(GET, POST, PUT, DELETE 등)
- QueryString – URI의 변수
- PostArgs – POST 본문에 전달된 인수 이 일치 변수를 사용하는 사용자 지정 규칙은 'Content-Type' 헤더가 'application/x-www-form-urlencoded' 및 'multipart/form-data'로 설정된 경우에만 적용됩니다.
- RequestUri – 요청의 URI
- RequestHeaders – 요청의 헤더
- RequestBody – 전체 요청 본문을 포함합니다. 이 일치 변수를 사용하는 사용자 지정 규칙은 'Content-Type' 헤더가 'application/x-www-form-urlencoded'로 설정된 경우에만 적용됩니다. 
- RequestCookies – 요청의 쿠키

### <a name="selector-optional"></a>선택기[선택 사항]

matchVariable 컬렉션의 필드에 대해 설명합니다. 예를 들어 matchVariable이 RequestHeaders인 경우 선택기는 *User-Agent* 헤더에 있을 수 있습니다.

### <a name="operator-required"></a>연산자[필수]

다음 연산자 중 하나여야 합니다.

- IPMatch - 일치 변수가 *RemoteAddr* 인 경우에만 사용됩니다.
- Equal – 입력이 MatchValue와 동일합니다.
- 포함
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex
- Geomatch

### <a name="negate-condition-optional"></a>부정 조건[선택 사항]

현재 조건을 부정합니다.

### <a name="transform-optional"></a>변환[선택 사항]

일치를 시도하기 전에 실행할 변환 이름이 포함된 문자열의 목록입니다. 다음 변환일 수 있습니다.

- 소문자
- 공백 제거
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>일치 값[필수]

일치하는 값 목록으로 *또는*'ed'로 간주할 수 있습니다. 예를 들어 IP 주소 또는 다른 문자열일 수 있습니다. 값 형식은 이전 연산자에 따라 달라집니다.

### <a name="action-required"></a>작업[필수]

- Allow – 트랜잭션을 승인하여 다른 모든 규칙을 건너뜁니다. 지정된 요청은 허용 목록에 추가되고 일치되면 요청이 추가 평가를 중지하고 백 엔드 풀로 전송됩니다. 허용 목록에 있는 규칙은 추가 사용자 지정 규칙 또는 관리형 규칙에 대해 평가되지 않습니다.
- Block – *SecDefaultAction*(검색/방지 모드)을 기반으로 트랜잭션을 차단합니다. 허용 작업과 마찬가지로 요청이 평가되고 차단 목록에 추가되면 평가가 중지되고 요청이 차단됩니다. 같은 조건을 충족하는 모든 요청은 평가되지 않고 차단됩니다. 
- Log – 규칙을 로그에 기록할 수 있지만 나머지 규칙은 평가를 위해 실행할 수 있습니다. 다른 사용자 지정 규칙은 우선 순위에 따라 평가되고 그 다음에 관리형 규칙이 적용됩니다.

## <a name="geomatch-custom-rules"></a>Geomatch 사용자 지정 규칙

사용자 지정 규칙을 사용하면 애플리케이션 및 보안 정책의 정확한 요구에 맞게 맞춤형 규칙을 만들 수 있습니다. 국가/지역별로 웹 애플리케이션에 대한 액세스를 제한할 수 있습니다. 자세한 내용은 [Geomatch 사용자 지정 규칙](geomatch-custom-rules.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

사용자 지정 규칙에 대해 학습한 후 [자체 사용자 지정 규칙을 만듭니다](create-custom-waf-rules.md).
