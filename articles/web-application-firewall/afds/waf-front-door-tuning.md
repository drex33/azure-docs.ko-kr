---
title: Azure Front Door용 WAF(Web Application Firewall) 튜닝
description: 이 문서에서는 Front Door용 WAF를 조정하는 방법에 대해 알아봅니다.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 17bf4c75d9db989a9556808c4bcf6c080fbbbd38
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063092"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Azure Front Door용 WAF(Web Application Firewall) 튜닝
 
Azure 관리형 기본 규칙 집합은 [OWASP CRS(Core Rule Set)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev)를 기반으로 하며 기본적으로 엄격하게 설계되었습니다. WAF를 사용하는 애플리케이션이나 조직의 특정 요구에 맞게 WAF 규칙을 조정해야 하는 경우가 종종 있습니다. 이런 경우 일반적으로 규칙 제외를 정의하고, 사용자 지정 규칙을 만들고, 문제나 가양성을 유발할 수 있는 규칙을 사용하지 않도록 설정하여 조정할 수 있습니다. WAF(Web Application Firewall)를 통과해야 하는 요청이 차단된 경우 수행할 수 있는 몇 가지 작업이 있습니다.

먼저 [Front Door WAF 개요](afds-overview.md) 및 [Front Door용 WAF 정책](waf-front-door-create-portal.md) 설명서를 읽어야 합니다. 또한 [WAF 모니터링 및 로깅](waf-front-door-monitor.md)을 사용하도록 설정했는지 확인하세요. 이러한 문서에는 WAF 함수, WAF 규칙 집합 작동 방식 및 WAF 로그에 액세스 하는 방법이 설명되어 있습니다.
 
## <a name="understanding-waf-logs"></a>WAF 로그 이해
 
WAF 로그의 목적은 WAF에 의해 일치 또는 차단된 모든 요청을 표시하는 것입니다. 일치 또는 차단되어 평가된 모든 요청의 컬렉션입니다. WAF가 차단하면 안 되는 요청(가양성)을 차단하는 경우 몇 가지 작업을 수행할 수 있습니다. 먼저 범위를 좁혀서 특정 요청을 찾습니다. 원하는 경우 `trackingReference` 필드를 포함하도록 [사용자 지정 응답 메시지를 구성](./waf-front-door-configure-custom-response-code.md)하여 이벤트를 쉽게 식별하고 특정 값에 대한 로그 쿼리를 수행할 수 있습니다. 로그를 살펴보고 요청의 특정 URI, 타임스탬프 또는 클라이언트 IP를 찾습니다. 관련 로그 항목을 찾으면 가양성에 대한 작업을 시작할 수 있습니다. 
 
예를 들어 WAF를 통과시키려는 문자열 `1=1`이 포함된 합법적인 트래픽이 있다고 가정합니다. 요청은 다음과 같은 모양입니다.

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

요청을 시도하면 WAF는 매개 변수 또는 필드에 *1=1* 문자열이 포함된 트래픽을 차단합니다. 이 문자열은 SQL 삽입 공격과 관련된 경우가 많습니다. 로그를 살펴보고 요청의 타임스탬프와 차단/일치 규칙을 볼 수 있습니다.
 
다음 예제에서는 규칙 일치로 인해 생성된 `FrontdoorWebApplicationFirewallLog` 로그를 살펴봅니다. 다음 Log Analytics 쿼리를 사용하여 지난 24시간 내에 차단된 요청을 찾을 수 있습니다.

```kusto
AzureDiagnostics
| where Category == 'FrontDoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
`requestUri` 필드에서 특히 `/api/Feedbacks/`에 요청이 수행된 것을 볼 수 있습니다. 계속해서 `ruleName` 필드에서 규칙 ID `942110`을 찾을 수도 있습니다. 규칙 ID를 알면 [OWASP ModSecurity Core Rule Set 공식 리포지토리](https://github.com/coreruleset/coreruleset)로 이동하여 [규칙 ID](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf)로 검색하고 코드를 검토하여 규칙이 정확히 무엇과 일치하는지 파악할 수 있습니다. 
 
그런 다음, `action` 필드를 확인하여 일치 시 요청을 차단하도록 규칙이 설정되어 있는 것을 확인하고, `policyMode`가 `prevention`으로 설정되어 있기 때문에 요청이 WAF에 의해 실제로 차단된 것을 확인합니다. 
 
이제 `details` 필드의 정보를 확인해보겠습니다. 여기에서는 `matchVariableName` 및 `matchVariableValue` 정보를 볼 수 있습니다. 누군가가 웹앱의 `comment` 필드에 *1=1* 을 입력했기 때문에 규칙이 트리거되었음을 알 수 있습니다.
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
액세스 로그를 확인하여 특정 WAF 이벤트에 대한 지식을 넓힐 수도 있습니다. 아래에서는 위 이벤트에 대한 응답으로 생성된 `FrontdoorAccessLog` 로그를 검토합니다.
 
`trackingReference` 값이 동일하기 때문에 관련 로그임을 알 수 있습니다. 일반적인 인사이트를 제공하는 다양한 필드(예: `userAgent` 및 `clientIP`) 중에서 `httpStatusCode` 및 `httpStatusDetails` 필드에 주의를 기울여야 합니다. 여기에서 클라이언트가 HTTP 403 응답을 받았음을 확인할 수 있으며, 이것은 요청이 거부 및 차단되었음을 확실히 나타냅니다. 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>가양성 해결
 
가양성 처리에 대해 현명한 결정을 내리려면 애플리케이션에 사용되는 기술에 익숙해지는 것이 중요합니다. 예를 들어 기술 스택에 SQL 서버가 없고 해당 규칙과 관련된 가양성이 발생했다고 가정해 보겠습니다. 이러한 규칙을 사용하지 않도록 설정하면 반드시 보안이 약화되는 것은 아닙니다. 

이러한 정보 및 예제의 `1=1` 문자열과 일치하는 규칙이 규칙 942110이라는 사실을 알면, 합법적인 요청이 차단되는 것을 막기 위채 몇 가지 작업을 수행할 수 있습니다.
 
* 제외 목록 사용
  * 제외 목록에 대한 자세한 내용은 [Front Door Service 제외 목록이 있는 WAF(Web Application Firewall)](waf-front-door-exclusion.md)을 참조하세요. 
* WAF 작업 변경
  * [WAF 작업](afds-overview.md#waf-actions)에서 요청이 규칙의 조건과 일치할 때 수행할 수 있는 작업에 대한 정보를 참조하세요.
* 사용자 지정 규칙 사용
  * [Azure Front Door를 사용하는 Web Application Firewall의 사용자 지정 규칙](waf-front-door-custom-rules.md)에서 사용자 지정 규칙에 대한 자세한 내용을 참조하세요.
* 규칙 사용 안 함 

> [!TIP]
> WAF를 통해 합법적인 요청을 허용하는 방식을 선택할 때는 가능하면 좁게 설정하는 것이 좋습니다. 예를 들어 규칙을 완전히 사용하지 않도록 설정하기 보다는 제외 목록을 사용하는 것이 좋습니다.

### <a name="using-exclusion-lists"></a>제외 목록 사용

제외 목록을 사용하는 한 가지 이점은 제외하기로 선택한 일치 변수만큼은 특정 요청에 대해 더 이상 검사되지 않는다는 것입니다. 다시 말해, 전체 요청을 검사에서 제외하는 대신 특정 조건이 충족되면 제외할 항목을 특정 요청 헤더, 요청 쿠키, 쿼리 문자열 인수 또는 요청 본문 게시 인수 중에서 선택할 수 있습니다. 요청에서 지정하지 않은 다른 변수는 여전히 정상적으로 검사됩니다.
 
제외는 전역 설정이라는 점을 고려하는 것이 중요합니다. 제외를 구성하면 특정 웹앱이나 URI뿐만 아니라 WAF를 통과하는 모든 트래픽에 적용된다는 의미입니다. 예를 들어, 특정 웹앱의 본문에서는 *1=1* 이 유효한 요청이지만 동일한 WAF 정책에 있는 다른 앱에서는 그렇지 않은 경우 문제가 될 수 있습니다. 애플리케이션마다 다른 제외 목록을 사용하는 것이 타당하다면, 애플리케이션마다 다른 WAF 정책을 사용하고 이러한 정책을 각 애플리케이션의 프런트 엔드에 적용하는 것이 좋습니다.
 
관리되는 규칙에 대한 제외 목록을 구성할 때 규칙 집합 내 모든 규칙, 규칙 그룹 내 모든 규칙 또는 개별 규칙을 제외하도록 선택할 수 있습니다. 제외 목록은 [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject), [Azure CLI](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add), [Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate) 또는 Azure Portal을 사용하여 구성할 수 있습니다.

* 규칙 수준에서 제외
  * 규칙 수준에서 제외를 적용하면 지정한 제외가 개별 규칙에 대해서만 분석되는 것이 아니라 규칙 집합의 다른 모든 규칙에 따라 분석됩니다. 이것은 제외에 대한 가장 세분화된 수준이며 이벤트 문제를 해결할 때 WAF 로그에서 찾은 정보를 기반으로 관리되는 규칙 집합을 미세 조정하는 데 사용할 수 있습니다.
* 규칙 그룹 수준에서 제외
  * 규칙 그룹 수준에서 제외를 적용하면 지정한 제외가 특정 규칙 유형 집합에 대해 분석되지 않습니다. 예를 들어, *SQLI* 를 제외된 규칙 그룹으로 선택하면 정의한 요청 제외가 SQLI 관련 규칙에서는 검사되지 않지만 *PHP*, *RFI*, 또는 *XSS* 와 같은 다른 그룹의 규칙에 대해 검사됩니다. 이런 유형의 제외는 애플리케이션이 특정 유형의 공격에 취약하지 않다고 확신하는 경우 유용할 수 있습니다. 예를 들어, SQL 데이터베이스가 없는 애플리케이션에서는 보안 수준에 부정적인 영향을 주지 않으면서 모든 *SQLI* 규칙을 제외할 수 있습니다.
* 규칙 집합 수준에서 제외 
  * 규칙 집합 수준에서 제외를 적용하면 지정한 제외가 규칙 집합에서 사용 가능한 보안 규칙에 대해 분석되지 않습니다. 이것은 포괄적인 제외이므로 신중하게 사용해야 합니다.

이 예제에서는 가장 세분화된 수준(단일 규칙에 제외 적용)에서 제외를 수행하고 `comment`가 포함된 일치 변수 **요청 본문 게시물 인수 이름** 을 제외하려고 합니다. 방화벽 로그 `"matchVariableName": "PostParamValue:comment"`에서 일치 변수 세부 정보를 볼 수 있기 때문에 분명합니다. 특성은 `comment`입니다. 이 특성 이름을 몇 가지 다른 방법으로 찾을 수도 있습니다. [요청 특성 이름 찾기](#finding-request-attribute-names)를 참조하세요.

![제외 규칙](../media/waf-front-door-tuning/exclusion-rules.png)

![특정 규칙에 대한 규칙 제외](../media/waf-front-door-tuning/exclusion-rule.png)

때로는 직관적이지 않은 방식으로 특정 매개 변수가 WAF에 전달되는 경우가 있습니다. 예를 들어 Azure Active Directory를 사용하여 인증할 때 전달되는 토큰이 있습니다. 일반적으로 이 토큰 `__RequestVerificationToken`은 요청 쿠키로 전달됩니다. 그러나 쿠키를 사용하지 않도록 설정한 경우 이 토큰은 요청 게시 인수로도 전달됩니다. 이러한 이유로 Azure AD 토큰 가양성을 해결하려면 `__RequestVerificationToken`이 `RequestCookieNames` 및 `RequestBodyPostArgsNames` 모두에 대한 제외 목록에 추가되었는지 확인해야 합니다.

필드 이름(*selector*)에 대한 제외는 값이 더 이상 WAF에서 평가되지 않습니다. 하지만 필드 이름 자체는 계속 평가되며 드물게 WAF 규칙과 일치하여 작업을 트리거할 수 있습니다.

![규칙 집합에 대한 규칙 제외](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>WAF 작업 변경

WAF 규칙의 동작을 처리하는 또 다른 방법은 요청이 규칙의 조건과 일치할 때 수행할 작업을 선택하는 것입니다. 사용 가능한 작업은 [허용, 차단, 로그 및 리디렉션](afds-overview.md#waf-actions)입니다.

이 예제에서는 기본 작업인 차단(*Block*)을 규칙 942110에 대한 로그(*Log*) 작업으로 변경했습니다. 이렇게 하면 WAF가 요청을 로그하고 나머지 낮은 우선 순위 규칙에 대해 동일한 요청을 계속해서 평가합니다.

![WAF 작업](../media/waf-front-door-tuning/actions.png)

동일한 요청을 수행한 후 로그를 다시 참조하면 이 요청이 규칙 ID 942110과 일치했고, `action_s` 필드가 이제는 차단(*Block*) 대신 로그(*Log*)를 나타내는 것을 볼 수 있습니다. 그런 다음, `trackingReference_s` 정보를 포함하도록 로그 쿼리를 확장하고 이 요청을 통해 발생한 다른 내용을 확인합니다.

![여러 규칙 일치를 보여주는 로그](../media/waf-front-door-tuning/actions-log.png)

흥미롭게도 규칙 ID 942110이 처리되고 밀리초 후에 다른 SQLI 규칙 일치가 발생한 것이 보입니다. 동일한 요청이 규칙 ID 942310에서 일치했고 이번에는 기본 동작 차단(*Block*)이 트리거되었습니다.

WAF 튜닝 또는 문제 해결 중에 로그(*Log*) 작업을 사용하는 또 다른 이점은 특정 규칙 그룹 내 여러 규칙이 지정한 요청과 일치하고 이것을 차단하는지 여부를 식별할 수 있다는 것입니다. 그런 다음, 적절한 수준, 즉 규칙 또는 규칙 그룹 수준에서 제외를 만들 수 있습니다. 

### <a name="using-custom-rules"></a>사용자 지정 규칙 사용

WAF 규칙 일치의 원인을 파악한 후에는 사용자 지정 규칙을 사용하여 WAF가 이벤트에 응답하는 방식을 조정할 수 있습니다. 사용자 지정 규칙은 관리되는 규칙보다 먼저 처리되고, 둘 이상의 조건을 포함할 수 있으며, 해당 작업은 [허용, 거부, 로그 또는 리디렉션](afds-overview.md#waf-actions)일 수 있습니다. 규칙이 일치하면 WAF 엔진이 처리를 중지합니다. 그러면 우선 순위가 낮은 다른 사용자 지정 규칙 및 관리되는 규칙이 더 이상 실행되지 않습니다.

아래 예에서는 두 가지 조건으로 사용자 지정 규칙을 만들었습니다. 첫 번째 조건은 요청 본문에서 `comment` 값을 찾습니다. 두 번째 조건은 요청 URI에서 `/api/Feedbacks/` 값을 찾습니다.

사용자 지정 규칙을 사용하면 WAF 규칙을 미세 조정하고 가양성을 처리할 때 가장 세분화할 수 있습니다. 이 경우 동일한 WAF 정책에 따라 여러 사이트 또는 앱에 존재할 수 있는 `comment` 요청 본문 값만을 기반으로 작업을 수행하지 않습니다. 특정 요청 URI `/api/Feedbacks/`에도 일치하는 다른 조건을 포함하여, 사용자 지정 규칙이 전에 조사하여 제외한 명시적 사용 사례에 실제로 적용되는지 확인합니다. 이렇게 하면 WAF 엔진이 동일한 공격(다른 조건에 대해 수행되는 경우)을 검사하고 방지할 수 있습니다.

![로그](../media/waf-front-door-tuning/custom-rule.png)

로그를 살펴보면 `ruleName_s` 필드에 전에 만든 사용자 지정 규칙에 지정된 이름인 `redirectcomment`가 포함된 것이 보입니다. `action_s` 필드에서 이벤트에 대해 *리디렉션* 작업이 수행된 것을 볼 수 있습니다. `details_matches_s` 필드에서는 두 조건이 일치하는 세부 정보를 볼 수 있습니다.

### <a name="disabling-rules"></a>규칙 사용 안 함

가양성을 피하는 또 다른 방법은 WAF가 악성이라고 생각한 입력과 일치하는 규칙을 사용하지 않도록 설정하는 것입니다. WAF 로그를 구문 분석했고 규칙을 942110으로 좁혔으므로 Azure Portal에서 사용하지 않도록 설정할 수 있습니다. [Azure Portal을 사용하여 Web Application Firewall 규칙 사용자 지정](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules)을 참조하세요.
 
특정 조건을 충족하는 모든 요청이 실제로 합법적인 요청이라고 확신하거나 규칙이 단순히 환경에 적용되지 않는다고 확신하는 경우(예: SQL이 아닌 백 엔드가 있기 때문에 SQL 삽입 규칙을 비활성화하는 경우)에는 규칙을 사용하지 않도록 설정하는 것이 유용합니다. 
 
하지만 규칙 비활성화는 전역 설정이며 WAF 정책에 연결된 모든 프런트 엔드 호스트에 적용됩니다. 규칙을 비활성화하기로 선택하면 WAF 정책에 연결된 다른 프런트 엔드 호스트에 대한 보호 또는 탐지 기능을 사용하지 않고 취약성에 노출된 상태로 남을 수 있습니다.
 
Azure PowerShell을 사용하여 관리되는 규칙을 사용하지 않도록 설정하려면 [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject) 개체 설명서를 참조하세요. Azure CLI를 사용하려면 [`az network front-door waf-policy managed-rules override`](/cli/azure/network/front-door/waf-policy/managed-rules/override) 설명서를 참조하세요.

![WAF 규칙](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> WAF 정책에 대한 변경 내용은 모두 문서화하는 것이 좋습니다. 가양성 탐지를 설명하는 예제 요청을 포함하고, 사용자 지정 규칙을 추가하거나, 규칙 또는 규칙 집합을 사용하지 않도록 설정하거나, 예외를 추가 하는 이유를 명확하게 설명합니다. 이 설명서는 나중에 애플리케이션을 다시 디자인하고 변경 내용이 여전히 유효한지 확인해야 하는 경우 유용할 수 있습니다. 감사를 받는 경우 또는 기본 설정에서 WAF 정책을 재구성한 이유의 근거를 보여야 하는 경우에도 유용할 수 있습니다.

## <a name="finding-request-fields"></a>요청 필드 찾기

[Fiddler](https://www.telerik.com/fiddler)와 같은 브라우저 프록시를 사용하면 개별 요청을 검사하고 웹 페이지의 어떠한 특정 필드가 호출되는지 확인할 수 있습니다. 이 기능은 WAF의 제외 목록을 사용하여 검사에서 특정 필드를 제외해야 하는 경우 유용합니다.

### <a name="finding-request-attribute-names"></a>요청 특성 이름 찾기
 
이 예에서는 `1=1` 문자열이 입력된 필드가 `comment`라는 것을 볼 수 있습니다. 이 데이터는 POST 요청의 본문에 포함되어 전달되었습니다.

![본문을 보여주는 Fiddler 요청](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

이것이 제외할 수 있는 필드입니다. 제외 목록에 대해 자세히 알아보려면 [웹 애플리케이션 방화벽 제외 목록](./waf-front-door-exclusion.md)을 참조하세요. 이 경우 다음 제외를 구성하여 평가를 제외할 수 있습니다.

![제외 규칙](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

방화벽 로그를 검사하면 제외 목록에 추가해야 하는 항목을 확인하기 위한 정보를 얻을 수도 있습니다. 로깅을 사용하도록 설정하려면 [Azure Front Door에서 메트릭 및 로그 모니터링](./waf-front-door-monitor.md)을 참조하세요.

검사하려는 요청이 발생한 시간은 `PT1H.json` 파일에서 방화벽 로그를 검토하여 찾을 수 있습니다. `PT1H.json` 파일은 `FrontDoorWebApplicationFirewallLog` 및 `FrontDoorAccessLog` 진단 로그가 저장된 스토리지 계정 컨테이너에 제공됩니다.

이 예에서는 요청을 차단하고(동일한 트랜잭션 참조를 사용하여) 정확히 동일한 시간에 발생한 규칙을 볼 수 있습니다.

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

Azure 관리형 규칙 집합이 작동하는 방식에 대한 지식을 통해([Azure Front Door의 Web Application Firewall](afds-overview.md) 참조) *action: Block* 속성이 있는 규칙은 요청 본문에 일치하는 데이터를 기반으로 차단한다는 것을 알 수 있습니다. 세부 정보에서 패턴(`1=1`)과 일치하고 필드 이름이 `comment`라는 것을 볼 수 있습니다. 동일한 이전 단계를 수행하여 `comment`가 포함된 요청 본문 게시물 인수 이름을 제외합니다.

### <a name="finding-request-header-names"></a>요청 헤더 이름 찾기

Fiddler는 요청 헤더 이름을 찾을 때 유용한 도구입니다. 다음 스크린샷에서는 Content-Type, User-Agent 등이 포함된 GET 요청의 헤더를 볼 수 있습니다. 요청 헤더를 사용하여 WAF에서 제외 및 사용자 지정 규칙을 만들 수도 있습니다.

![헤더를 보여주는 Fiddler 요청](../media/waf-front-door-tuning/fiddler-request-header-name.png)

요청 및 응답 헤더를 보는 또 다른 방법은 Edge 또는 Chrome과 같은 브라우저의 개발자 도구 내부를 보는 것입니다. F12 키를 누르거나 마우스 오른쪽 단추 -> **검사** -> **개발자 도구** 를 클릭하고 **네트워크** 탭을 선택하면 됩니다. 웹 페이지를 로드하고 검사하려는 요청을 클릭합니다.

![네트워크 검사기 요청](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>요청 쿠키 이름 찾기

요청에 쿠키가 포함된 경우 쿠키 탭을 선택하면 Fiddler에서 볼 수 있습니다. 쿠키 정보를 사용하여 WAF에서 제외 또는 사용자 지정 규칙을 만들 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 웹 애플리케이션 방화벽](../overview.md)에 대해 알아봅니다.
- [Front Door를 만드는](../../frontdoor/quickstart-create-front-door.md) 방법을 알아봅니다.
