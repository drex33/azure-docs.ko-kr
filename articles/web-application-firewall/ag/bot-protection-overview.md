---
title: Azure Application Gateway 봇 보호의 WAF 개요
titleSuffix: Azure Web Application Firewall
description: 이 문서에서는 Application Gateway 봇 보호의 WAF(웹 애플리케이션 방화벽)에 대한 개요를 제공합니다.
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 07/30/2021
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 19453e987c276ae2716e06900ea2fc8ecc2a6ba6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566460"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure Application Gateway 봇 보호의 Azure 웹 애플리케이션 방화벽 개요

모든 인터넷 트래픽의 약 20%가 잘못된 봇에서 나옵니다. 웹 애플리케이션에서 스크래핑, 스캔 및 취약성 찾기 등의 작업을 수행합니다. 이러한 봇이 WAF(웹 애플리케이션 방화벽)에서 중지되면 사용자를 공격할 수 없습니다. 또한 백 엔드 및 기타 기본 인프라와 같은 리소스와 서비스를 사용할 수 없습니다.

WAF에 대해 관리형 봇 보호 규칙 집합을 사용하도록 설정하여 알려진 악성 IP 주소의 요청을 차단하거나 기록할 수 있습니다. IP 주소는 Microsoft 위협 인텔리전스 피드에서 제공됩니다. Intelligent Security Graph는 Microsoft 위협 인텔리전스를 구동하며 Azure Security Center를 비롯한 여러 서비스에서 사용됩니다.

## <a name="use-with-owasp-rulesets"></a>OWASP 규칙 집합으로 사용

OWASP 규칙 집합(2.2.9, 3.0 및 3.1)과 함께 봇 보호 규칙 집합을 사용할 수 있습니다. 지정된 시간에 OWASP 규칙 집합을 하나만 사용할 수 있습니다. 봇 보호 규칙 집합에는 자체 규칙 집합에 표시되는 추가 규칙이 포함되어 있습니다. 제목은 **Microsoft_BotManagerRuleSet_0.1** 이며 다른 OWASP 규칙처럼 사용하거나 사용하지 않도록 설정할 수 있습니다.

![봇 규칙 집합](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>규칙 집합 업데이트

알려진 잘못된 IP 주소의 봇 완화 규칙 집합 목록은 Microsoft Threat Intelligence 피드에서 하루에 여러 번 업데이트되어 봇과 동기화 상태를 유지합니다. 웹 애플리케이션은 봇 공격 벡터가 변경되는 경우에도 지속적으로 보호됩니다.

## <a name="log-example"></a>로그 예

봇 보호에 대한 로그 항목의 예는 다음과 같습니다.

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>다음 단계

- [Azure Application Gateway에서 Web Application Firewall에 대한 봇 보호 구성](bot-protection.md)
