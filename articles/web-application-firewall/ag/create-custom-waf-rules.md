---
title: v2 사용자 지정 규칙 만들기 및 사용
titleSuffix: Azure Web Application Firewall
description: 이 문서에서는 Application Gateway에서 WAF(Web Application Firewall) v2 사용자 지정 규칙을 만드는 방법을 설명합니다.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 11/20/2020
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a1710b293278e3b36fdabdf70bd698d8408e946d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680075"
---
# <a name="create-and-use-web-application-firewall-v2-custom-rules-on-application-gateway"></a>Application Gateway에서 Web Application Firewall v2 사용자 지정 규칙 만들기 및 사용

Azure Application Gateway의 WAF(Web Application Firewall) v2는 웹 애플리케이션을 보호합니다. 이 보호 기능은 OWASP(Open Web Application Security Project) CRS(핵심 규칙 집합)를 통해 제공됩니다. 경우에 따라 특정 요구 사항을 충족하기 위해 사용자 지정 규칙을 만들어야 할 수도 있습니다. WAF 사용자 지정 규칙에 대한 자세한 내용은 [사용자 지정 웹 애플리케이션 방화벽 규칙 개요](custom-waf-rules-overview.md)를 참조하세요.

이 문서에서는 v2 WAF에서 만들고 사용할 수 있는 몇 가지 사용자 지정 규칙의 예를 보여줍니다. Azure PowerShell을 사용하여 사용자 지정 규칙으로 WAF를 배포하는 방법을 알아보려면 [Azure PowerShell을 사용하여 Web Application Firewall 사용자 지정 규칙 구성](configure-waf-custom-rules.md)을 참조하세요.

이 문서에 표시된 JSON 코드 조각은 [ApplicationGatewayWebApplicationFirewallPolicies](/azure/templates/microsoft.network/applicationgatewaywebapplicationfirewallpolicies) 리소스에서 파생됩니다.

>[!NOTE]
> Application Gateway에서 WAF 계층을 사용하지 않는 경우 Application Gateway를 WAF 계층으로 업그레이드하는 옵션이 오른쪽 창에 표시됩니다.

![WAF 사용][fig1]

## <a name="example-1"></a>예제 1

웹 사이트 크롤링에서 차단하려는 *evilbot* 이라는 봇이 있음을 알고 있습니다. 이 경우 요청 헤더에서 User-Agent *evilbot* 을 차단합니다.

논리: p

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

다음은 해당 JSON입니다.

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

이 사용자 지정 규칙을 사용하여 배포된 WAF를 보려면 [Azure PowerShell을 사용하여 웹 애플리케이션 방화벽 사용자 지정 규칙 구성](configure-waf-custom-rules.md)을 참조하세요.

### <a name="example-1a"></a>예 1a

정규식을 사용하여 동일한 작업을 수행할 수 있습니다.

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Regex `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

해당 JSON:

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-2"></a>예 2

GeoMatch 운영자를 사용하여 미국으로부터의 트래픽만 허용하고 관리되는 규칙을 적용하려고 합니다.

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr `

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator GeoMatch `
   -MatchValue "US" `
   -Transform Lowercase `
   -NegationCondition $True

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name "allowUS" `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

해당 JSON:

```json
  {
    "customRules": [
      {
        "name": "allowUS",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "GeoMatch",
            "NegationConditon": false,
            "matchValues": [
              "US"
            ]
          }
        ]
      }
    ]
  }
```



## <a name="example-3"></a>예제 3

198.168.5.0/24 범위의 IP 주소로 들어오는 모든 요청을 차단하려고 합니다.

이 예제에서는 IP 주소 범위로 들어오는 모든 트래픽을 차단합니다. 규칙 이름은 *myrule1* 이고 우선 순위는 10으로 설정됩니다.

논리: p

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

해당 JSON은 다음과 같습니다.

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "IPMatch",
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      }
    ]
  }
```

해당 CRS 규칙: `SecRule REMOTE_ADDR "@ipMatch 192.168.5.0/24" "id:7001,deny"`

## <a name="example-4"></a>예제 4

이 예제에서는 User-Agent *evilbot* 및 192.168.5.0/24 범위의 트래픽을 차단하려고 합니다. 이를 수행하기 위해 두 개의 개별 일치 조건을 만든 다음, 둘 다 동일한 규칙에 둘 수 있습니다. 이렇게 하면 User-Agent 헤더에 있는 두 *evilbot* **및** 192.168.5.0/24 범위의 IP 주소가 모두 일치하는 경우 요청이 차단됩니다.

논리: p **및** q

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

 $variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

 $rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1, $condition2 `
   -Action Block
```

해당 JSON은 다음과 같습니다.

```json
{ 

    "customRules": [ 
      { 
        "name": "myrule", 
        "ruleType": "MatchRule", 
        "priority": 10, 
        "action": "block", 
        "matchConditions": [ 
            { 
              "matchVariable": "RemoteAddr", 
              "operator": "IPMatch", 
              "negateCondition": false, 
              "matchValues": [ 
                "192.168.5.0/24" 
              ] 
            }, 
            { 
              "matchVariable": "RequestHeaders", 
              "selector": "User-Agent", 
              "operator": "Contains", 
              "transforms": [ 
                "Lowercase" 
              ], 
              "matchValues": [ 
                "evilbot" 
              ] 
            } 
        ] 
      } 
    ] 
  } 
```

## <a name="example-5"></a>예제 5

이 예제에서는 요청이 IP 주소 범위 *192.168.5.0/24* 를 벗어나거나 사용자 에이전트 문자열이 *chrome* 이 아닌 경우(사용자가 chrome 브라우저를 사용하지 않음) 차단하려고 합니다. 이 논리는 **or** 를 사용하기 때문에 다음 예제에서 볼 수 있듯이 두 조건은 별도의 규칙에 있습니다. *myrule1* 및 *myrule2* 는 모두 트래픽을 차단하기 위해 일치해야 합니다.

논리: **not**(p **및** q) = **not** p **or not** q.

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $True

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "chrome" `
   -Transform Lowercase `
   -NegationCondition $True

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block
```

해당 JSON:

```json
{
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "IPMatch",
            "negateCondition": true,
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "selector": "User-Agent",
            "operator": "Contains",
            "negateCondition": true,
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "chrome"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-6"></a>예제 6

사용자 지정 SQLI를 차단하려고 합니다. 여기서 사용되는 논리는 **or** 이고 모든 값이 *RequestUri* 에 있으므로 모든 *MatchValues* 는 쉼표로 구분된 목록에 있을 수 있습니다.

논리: p **or** q **or** r

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri 
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1", "drop tables", "'—" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule4 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

해당 JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule4",
        "ruleType": "MatchRule",
        “priority”: 10
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1",
              "drop tables",
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

대체 Azure PowerShell:

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
-Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "drop tables" `
   -NegationCondition $False

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block

$variable3 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition3 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable3 `
   -Operator Contains `
   -MatchValue "’—" `
   -NegationCondition $False

$rule3 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule3 `
   -Priority 30 `
   -RuleType MatchRule `
   -MatchCondition $condition3 `
   -Action Block
```

해당 JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "drop tables"
            ]
          }
        ]
      },
      {
        "name": "myrule3",
        "ruleType": "MatchRule",
        "priority": 30,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="next-steps"></a>다음 단계

사용자 지정 규칙을 만든 후에는 WAF 로그를 보는 방법을 알아볼 수 있습니다. 자세한 내용은 [Application Gateway 진단](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요.

[fig1]: ../media/create-custom-waf-rules/1.png
