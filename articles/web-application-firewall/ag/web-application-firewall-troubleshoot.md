---
title: 문제 해결 - Azure Web Application Firewall
description: 이 문서에서는 Azure Application Gateway용 WAF(Web Application Firewall)에 대한 문제 해결 정보를 제공합니다.
services: web-application-firewall
author: KumudD
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: kumud
ms.topic: conceptual
ms.openlocfilehash: 9db2e6a3c0ee90a81b4f6cb363c3513314c18944
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550991"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Azure Application Gateway용 WAF(Web Application Firewall) 문제 해결

WAF(Web Application Firewall)를 통과해야 하는 요청이 차단된 경우 수행할 수 있는 몇 가지 작업이 있습니다.

먼저 [WAF 개요](ag-overview.md) 및 [WAF 구성](application-gateway-waf-configuration.md) 문서를 읽어야 합니다. 또한 [WAF 모니터링](../../application-gateway/application-gateway-diagnostics.md)을 사용하도록 설정했는지 확인합니다. 이 문서에서는 WAF의 작동 방식, WAF 규칙 집합의 작동 방식, WAF 로그에 액세스하는 방법을 설명합니다.

OWASP 규칙 집합은 기본적으로 매우 엄격하게 설계되었으며 WAF를 사용하는 애플리케이션 또는 조직의 특정 요구 사항에 맞게 튜닝해야 합니다. 튜닝은 완전히 정상적인 동작이며, 실제로 제외 및 사용자 지정 규칙을 만들고 이슈 또는 가양성을 일으킬 수 있는 규칙을 사용하지 않도록 설정하기 위해 필요한 경우도 많습니다. 사이트별 및 URI별 정책을 사용하면 변경 내용을 특정 사이트/URI에만 적용할 수 있으므로 실행하지 않는 다른 사이트에도 변경 내용이 영향을 미쳐 동일한 이슈가 발생하지 않도록 해야 합니다. 

## <a name="understanding-waf-logs"></a>WAF 로그 이해

WAF 로그의 목적은 WAF를 통해 일치되거나 차단된 모든 요청을 표시하는 것입니다. WAF 로그는 평가 후 일치되거나 차단된 모든 요청의 원장입니다. WAF가 차단하면 안 되는 요청을 차단하는 경우(가양성) 몇 가지 작업을 수행할 수 있습니다. 먼저 범위를 좁혀서 특정 요청을 찾습니다. 로그를 살펴보고 요청의 특정 URI, 타임스탬프 또는 트랜잭션 ID를 찾습니다. 관련 로그 항목을 찾았으면 가양성에 대한 작업을 시작할 수 있습니다.

예를 들어 WAF를 통과시키려는 문자열 *1=1* 이 포함된 합법적인 트래픽이 있다고 가정합니다. 요청을 시도하면 WAF는 매개 변수 또는 필드에 *1=1* 문자열이 포함된 트래픽을 차단합니다. 이 문자열은 SQL 삽입 공격과 관련된 경우가 많습니다. 로그를 살펴보고 요청의 타임스탬프와 차단/일치 규칙을 볼 수 있습니다.

다음 예제에서는 TransactionId 필드를 사용하여 동일한 요청에서 네 개의 규칙이 트리거되는 것을 확인할 수 있습니다. 첫 번째 규칙은 사용자가 요청에 대해 숫자/IP URL을 사용하여 요청이 일치되었으며 경고이기 때문에 변칙 점수가 3만큼 증가한다고 표시됩니다. 일치된 다음 규칙은 942130으로, 찾고 있던 규칙입니다. `details.data` 필드에서 *1=1* 을 확인할 수 있습니다. 이것도 경고이기 때문에 변칙 점수가 다시 3만큼 증가합니다. 일반적으로 **일치함** 작업이 있는 모든 규칙에서 변칙 점수가 증가하며, 이 시점에서 변칙 점수는 6입니다. 자세한 내용은 [변칙 점수 매기기 모드](ag-overview.md#anomaly-scoring-mode)를 참조하세요.

마지막 두 개의 로그 항목은 변칙 점수가 충분히 크기 때문에 요청이 차단되었음을 보여 줍니다. 이 항목의 작업은 다른 두 항목과 다릅니다. 실제로 요청을 ‘차단’했다고 표시됩니다. 이 규칙은 필수이며 사용하지 않도록 설정할 수 없습니다. 규칙이 아니라 WAF 내부 요소의 핵심 인프라로 간주해야 합니다.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>가양성 수정

이 정보와 규칙 942130이 *1=1* 문자열과 일치된 규칙이라는 정보를 사용하여 트래픽이 차단되지 않도록 몇 가지 작업을 수행할 수 있습니다.

- 제외 목록 사용

   제외 목록에 대한 자세한 내용은 [WAF 구성](application-gateway-waf-configuration.md#waf-exclusion-lists)을 참조하세요.
- 규칙을 사용하지 않도록 설정합니다.

### <a name="using-an-exclusion-list"></a>제외 목록 사용

가양성 처리에 대해 현명한 결정을 내리려면 애플리케이션에 사용되는 기술에 익숙해지는 것이 중요합니다. 예를 들어 기술 스택에 SQL 서버가 없고 해당 규칙과 관련된 가양성이 발생했다고 가정해 보겠습니다. 규칙을 사용하지 않도록 설정해도 보안이 반드시 약화되는 것은 아닙니다.

제외 목록을 사용할 경우의 한 가지 혜택은 요청의 특정 부분만 사용할 수 없게 된다는 것입니다. 그러나 전역 설정이기 때문에 WAF를 통과하는 모든 트래픽에 특정 제외를 적용할 수 있습니다. 예를 들어 *1=1* 이 특정 앱의 본문에서는 유효한 요청이지만 다른 경우에는 유효한 요청이 아닌 경우 이슈가 발생할 수 있습니다. 또 다른 혜택은 전체 요청을 제외하는 것과 반대로 특정 조건이 충족될 경우 제외할 항목을 본문, 헤더, 쿠키 중에서 선택할 수 있는 것입니다.

때로는 직관적이지 않은 방식으로 특정 매개 변수가 WAF에 전달되는 경우가 있습니다. 예를 들어 Azure Active Directory를 사용하여 인증할 때 전달되는 토큰이 있습니다. *__RequestVerificationToken* 토큰은 일반적으로 요청 쿠키로 전달됩니다. 그러나 쿠키를 사용할 수 없는 경우 이 토큰도 요청 특성이나 “arg”로 전달됩니다. 이 경우에는 *__RequestVerificationToken* 도 **요청 특성 이름** 으로 제외 목록에 추가해야 합니다.

![제외](../media/web-application-firewall-troubleshoot/exclusion-list.png)

이 예제에서는 *text1* 과 같은 **요청 특성 이름** 을 제외하려고 합니다. 방화벽 로그에서 다음과 같이 특성 이름을 확인할 수 있기 때문에 이 작업은 명확합니다. **data: Matched Data: 1=1 found within ARGS:text1: 1=1** 특성은 **text1** 입니다. 이 특성 이름을 몇 가지 다른 방법으로 찾을 수도 있습니다. [요청 특성 이름 찾기](#finding-request-attribute-names)를 참조하세요.

![WAF 제외 목록](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>규칙 사용 안 함

가양성을 피하는 또 다른 방법은 WAF가 악성이라고 생각한 입력과 일치하는 규칙을 사용하지 않도록 설정하는 것입니다. WAF 로그를 구문 분석했고 규칙을 942130으로 좁혔으므로 Azure Portal에서 사용하지 않도록 설정할 수 있습니다. [Azure Portal을 통해 웹 애플리케이션 방화벽 규칙 사용자 지정](application-gateway-customize-waf-rules-portal.md)을 참조하세요.

규칙을 사용하지 않도록 설정할 경우의 혜택 중 하나는 일반적으로 차단되는 특정 조건을 포함하는 모든 트래픽이 유효한 트래픽이라는 것을 알고 있는 경우 전체 WAF에 대해 해당 규칙을 사용하지 않도록 설정할 수 있다는 것입니다. 그러나 특정 사용 사례에서만 유효한 트래픽인 경우 전체 WAF에 대해 해당 규칙을 사용하지 않도록 설정하면 전역 설정이므로 취약성이 나타납니다.

Azure PowerShell을 사용하려면 [PowerShell을 통해 웹 애플리케이션 방화벽 규칙 사용자 지정](application-gateway-customize-waf-rules-powershell.md)을 참조하세요. Azure CLI를 사용하려면 [Azure CLI를 통해 웹 애플리케이션 방화벽 규칙 사용자 지정](application-gateway-customize-waf-rules-cli.md)을 참조하세요.

![WAF 규칙](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>요청 특성 이름 찾기

[Fiddler](https://www.telerik.com/fiddler)를 사용하여 개별 요청을 검사하고 호출된 웹 페이지의 필드를 확인합니다. 이렇게 하면 제외 목록을 사용하여 특정 필드가 검사되지 않도록 제외할 수 있습니다.

이 예제에서는 *1=1* 문자열이 입력된 필드가 **text1** 인 것을 확인할 수 있습니다.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-1.png" alt-text="Progress Telerik Fiddler Web Debugger 스크린샷. Raw 탭에서 이름 text1 뒤에 1=1이 표시됩니다." border="false":::

제외할 수 있는 필드입니다. 제외 목록에 대한 자세한 내용은 [웹 애플리케이션 방화벽 요청 크기 한도 및 제외 목록](application-gateway-waf-configuration.md#waf-exclusion-lists)을 참조하세요. 이 경우 다음 제외를 구성하여 평가를 제외할 수 있습니다.

![WAF 제외](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

방화벽 로그를 검사하면 제외 목록에 추가해야 하는 항목을 확인하기 위한 정보를 얻을 수도 있습니다. 로깅을 사용하도록 설정하려면 [Application Gateway의 백 엔드 상태, 리소스 로그, 메트릭](../../application-gateway/application-gateway-diagnostics.md)을 참조하세요.

방화벽 로그를 검사하고 PT1H.json 파일에서 검사하려는 요청이 발생한 시간을 확인합니다.

이 예제에서는 동일한 TransactionID를 사용하는 네 개의 규칙이 있으며 모두 동일한 시간에 발생했음을 확인할 수 있습니다.

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

CRS 규칙 집합의 작동 방식과 CRS 규칙 집합 3.0이 변칙 점수 매기기 시스템에서 작동한다는 지식([Azure Application Gateway용 Web Application Firewall](ag-overview.md) 참조)을 기반으로 해서 **action: Blocked** 속성이 있는 하위 두 규칙이 총 변칙 점수에 따라 차단될 것을 알 수 있습니다. 중점적으로 살펴볼 규칙은 상위 두 규칙입니다.

첫 번째 항목은 사용자가 숫자 IP 주소를 사용하여 Application Gateway로 이동했기 때문에 로깅되었으며, 이 경우에는 무시해도 됩니다.

두 번째 항목(규칙 942130)은 흥미로운 규칙입니다. 세부 정보에서 패턴(1=1)과 일치하고 필드 이름이 **text1** 인 것을 확인할 수 있습니다. 이전 단계를 동일하게 수행하여 **1=1** 과 **같은** **요청 특성 이름** 을 제외합니다.

## <a name="finding-request-header-names"></a>요청 헤더 이름 찾기

Fiddler는 요청 헤더 이름을 찾을 때 유용한 도구입니다. 다음 스크린샷에서 *Content-Type*, *User-Agent* 등이 포함된 GET 요청의 헤더를 확인할 수 있습니다.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-2.png" alt-text="Progress Telerik Fiddler Web Debugger 스크린샷. Raw 탭에는 connection, content-type, user-agent 등의 요청 헤더 정보가 나열됩니다." border="false":::

요청 및 응답 헤더를 확인하는 또 다른 방법은 Chrome의 개발자 도구를 살펴보는 것입니다. F12 키를 누르거나 마우스 오른쪽 단추 -> **검사** -> **개발자 도구** 를 클릭하고 **네트워크** 탭을 선택하면 됩니다. 웹 페이지를 로드하고 검사하려는 요청을 클릭합니다.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>요청 쿠키 이름 찾기

요청에 쿠키가 포함된 경우 **쿠키** 탭을 선택하면 Fiddler에서 볼 수 있습니다.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>가양성을 제거하기 위해 전역 매개 변수 제한

- 요청 본문 검사 사용 안 함

   **요청 본문 검사** 를 끔으로 설정하면 모든 트래픽의 요청 본문이 WAF에서 평가되지 않습니다. 이 기능은 요청 본문이 애플리케이션에 악성이 아닌 것을 알고 있는 경우에 유용할 수 있습니다.

   이 옵션을 사용하지 않도록 설정하면 요청 본문만 검사되지 않습니다. 제외 목록 기능을 사용하여 개별 항목을 제외하지 않는 한 헤더와 쿠키는 계속 검사됩니다.

- 파일 크기 제한

   WAF의 파일 크기를 제한하여 웹 서버에 대한 공격 발생 가능성을 제한합니다. 큰 파일의 업로드를 허용하면 백 엔드에 과부하가 걸릴 위험이 증가합니다. 파일 크기를 일반적인 애플리케이션 사용 사례로 제한하는 것은 공격을 방지하는 또 다른 방법입니다.

   > [!NOTE]
   > 지정된 크기를 초과하는 파일 업로드가 앱에 필요하지 않음을 알고 있다면 한도를 설정하여 제한할 수 있습니다.

## <a name="firewall-metrics-waf_v1-only"></a>방화벽 메트릭(WAF_v1만 해당)

v1 Web Application Firewall의 경우 이제 포털에서 다음 메트릭을 사용할 수 있습니다. 

1. Web Application Firewall 차단된 요청 수   차단된 요청 수
2. Web Application Firewall 차단된 규칙 수 **일치되고** 요청이 차단된 모든 규칙
3. Web Application Firewall 총 규칙 배포   평가 중 일치된 모든 규칙
     
메트릭을 사용하도록 설정하려면 포털에서 **메트릭** 탭을 선택하고 세 개의 메트릭 중 하나를 선택합니다.

## <a name="next-steps"></a>다음 단계

[Application Gateway에서 웹 애플리케이션 방화벽을 구성하는 방법](tutorial-restrict-web-traffic-powershell.md)을 참조하세요.
