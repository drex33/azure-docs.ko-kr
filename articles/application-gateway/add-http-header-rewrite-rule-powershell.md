---
title: Azure Application Gateway의 HTTP 헤더 다시 쓰기
description: 이 문서에서는 Azure PowerShell을 사용하여 Azure Application Gateway에 HTTP 헤더를 다시 쓰는 방법에 관한 정보를 제공합니다.
services: application-gateway
author: KumudD
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/12/2019
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1a3f64167978196afc5d86b983c44f021b554115
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836292"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Azure Application Gateway로 HTTP 요청 및 응답 헤더 다시 쓰기 - Azure PowerShell

이 문서에서는 Azure PowerShell로 요청 및 응답의 HTTP 헤더를 다시 쓰도록 [Application Gateway v2 SKU](./application-gateway-autoscaling-zone-redundant.md) 인스턴스를 구성하는 방법을 설명합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

- 이 문서의 단계를 완료하려면 Azure PowerShell을 로컬로 실행해야 합니다. 또한 Az 모듈 버전 1.0.0 이상을 설치해야 합니다. `Import-Module Az`를 실행한 뒤 `Get-Module Az`를 실행하여 설치한 버전을 확인합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell 버전을 확인한 후 `Login-AzAccount`를 실행하여 Azure와의 연결을 만듭니다.
- Application Gateway v2 SKU 인스턴스가 있어야 합니다. 헤더 재작성은 v1 SKU에서 지원되지 않습니다. v2 SKU가 없는 경우 시작하기 전에 [Application Gateway v2 SKU](./tutorial-autoscale-ps.md) 인스턴스를 만듭니다.

## <a name="create-required-objects"></a>필수 개체 만들기

HTTP 헤더 다시 쓰기를 구성하려면 다음 단계를 완료해야 합니다.

1. HTTP 헤더 다시 쓰기에 필요한 개체를 만듭니다.

   - **RequestHeaderConfiguration**: 다시 쓰려는 요청 및 요청 헤더 필드와 헤더의 새 값을 지정하는 데 사용됩니다.

   - **ResponseHeaderConfiguration**: 다시 쓰려는 요청 및 응답 필드와 헤더의 새 값을 지정하는 데 사용됩니다.

   - **ActionSet**: 이전에 지정된 요청 및 응답 헤더의 구성을 포함합니다.

   - **조건**: 선택적 구성입니다. 다시 쓰기 조건은 HTTP 요청 및 응답의 콘텐츠를 평가합니다. HTTP(S) 요청 또는 응답이 다시 쓰기 조건과 일치하면 다시 쓰기 작업이 수행됩니다.

     작업에 둘 이상의 조건을 연결할 경우 모든 조건이 충족되는 경우에만 작업이 수행됩니다. 즉, 작업은 논리적 AND 작업입니다.

   - **RewriteRule**: 여러 다시 쓰기 작업/다시 쓰기 조건 조합을 포함합니다.

   - **RuleSequence**: 다시 쓰기 규칙이 실행되는 순서를 결정하는 데 도움이 되는 선택적 구성입니다. 이 구성은 다시 쓰기 집합에 다시 쓰기 규칙이 여러 개 있는 경우에 유용합니다. 규칙 시퀀스값이 낮은 다시 쓰기 규칙이 먼저 실행됩니다. 두 다시 쓰기 규칙에 동일한 규칙 시퀀스값을 할당할 경우 실행 순서는 불명확합니다.

     RuleSequence를 명시적으로 지정하지 않으면 기본값인 100이 설정됩니다.

   - **RewriteRuleSet**: 요청 회람 규칙과 연결되는 다시 쓰기 규칙을 여러 개 포함합니다.

2. RewriteRuleSet을 회람 규칙에 연결합니다. 이 다시 쓰기 구성이 회람 규칙을 통해 원본 수신기에 첨부됩니다. 기본 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 원본 수신기와 연결되어 글로벌 헤더 다시 쓰기가 됩니다. 경로 기반 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 URL 경로 맵에 정의됩니다. 이 경우 사이트의 특정 경로 영역에만 적용됩니다.

여러 HTTP 헤더 다시 쓰기 집합을 만들고 각 다시 쓰기 집합을 여러 수신기에 적용할 수 있습니다. 그러나 특정 수신기에는 다시 쓰기 집합을 하나만 적용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>HTTP 헤더 다시 쓰기 규칙 구성 지정

이 예제에서는 위치 헤더에 azurewebsites.net 참조가 포함될 때마다 HTTP 응답의 위치 헤더를 다시 작성하여 리디렉션 URL을 수정합니다. 이렇게 하기 위해 응답의 위치 헤더에 azurewebsites.net이 포함되어 있는지를 평가하는 조건을 추가하겠습니다. `(https?)://.*azurewebsites.net(.*)$` 패턴을 사용합니다. 헤더 값으로 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}`을 사용합니다. 이 값은 위치 헤더에서 *azurewebsites.net* 을 *contoso.com* 으로 바꿉니다.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet -Condition $condition
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>애플리케이션 게이트웨이 구성 검색

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>요청 회람 규칙의 구성 검색

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>HTTP 헤더를 다시 쓰기 위한 구성으로 애플리케이션 게이트웨이를 업데이트합니다.

이 예제에서는 다시 쓰기 집합이 기본 회람 규칙에 즉시 연결됩니다. 경로 기반 회람 규칙의 경우 연결은 기본적으로 사용하도록 설정되지 않습니다. 다시 쓰기 세트는 포털을 통해 적용해야 하는 경로를 확인하거나 각 경로에 RewriteRuleSet를 지정하여 URL 경로 맵 구성을 제공하는 방법으로 사용할 수 있습니다.  

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name $rewriteRuleSet.Name  -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name $reqRoutingRule.Name -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>다시 쓰기 규칙 삭제

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>다음 단계

몇 가지 일반적인 사용 사례를 설정하는 방법에 관한 자세한 내용은 [공용 헤더 다시 쓰기 시나리오](./rewrite-http-headers-url.md)를 참조하세요.
