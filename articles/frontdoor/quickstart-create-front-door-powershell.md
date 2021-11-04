---
title: '빠른 시작: Azure Front Door를 사용하여 고가용성 설정 - Azure PowerShell'
description: 이 빠른 시작에서는 Azure Front Door를 사용하여 Azure PowerShell을 사용하는 고가용성 및 고성능 글로벌 웹 애플리케이션을 만드는 방법을 보여 줍니다.
services: front-door
documentationcenter: na
author: duongau
ms.author: duau
manager: KumudD
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: 29c0718f048358867f78ef58f2d0a688240d08a0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131086268"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하는 고가용성 글로벌 웹 애플리케이션을 위한 Front Door 만들기

Azure PowerShell을 사용하여 고가용성 및 고성능 글로벌 웹 애플리케이션을 만들어 Azure Front Door를 시작하세요.

Front Door는 웹 트래픽을 백 엔드 풀의 특정 리소스로 보냅니다. 프런트 엔드 도메인을 정의하고, 백 엔드 풀에 리소스를 추가하고, 라우팅 규칙을 만듭니다. 이 문서에서는 두 개의 웹앱 리소스와 기본 경로 일치 “/*”를 사용하는 단일 라우팅 규칙을 포함하는 하나의 백 엔드 풀에 대한 간단한 구성을 사용합니다.

:::image type="content" source="media/quickstart-create-front-door/environment-diagram.png" alt-text="PowerShell을 사용하는 Front Door 환경의 다이어그램." border="false":::

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>리소스 그룹 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 다음 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>웹앱의 두 인스턴스 만들기

이 빠른 시작에는 서로 다른 Azure 지역에서 실행되는 웹 애플리케이션의 두 인스턴스가 필요합니다. 두 웹 애플리케이션 인스턴스는 모두 활성/활성 모드로 실행되므로 두 인스턴스 중 하나에서 트래픽을 수행할 수 있습니다. 이 구성은 장애 조치(failover)로 작동하는 활성/대기 구성과 다릅니다.

웹앱이 아직 없는 경우 다음 스크립트를 사용하여 두 가지 예제 웹앱을 설정합니다.

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-1" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-2" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanEastUS
```

## <a name="create-a-front-door"></a>Front Door 만들기

이 섹션에서는 다음과 같은 Front Door의 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.
    
* 프런트 엔드 개체는 Front Door 기본 도메인을 포함합니다.
* 백 엔드 풀은 Front Door가 클라이언트 요청의 부하를 분산하는 동일한 백 엔드 세트입니다.
* 라우팅 규칙이 프런트 엔드 호스트와 일치하는 URL 경로 패턴을 특정 백 엔드 풀에 매핑합니다.

### <a name="create-a-frontend-object"></a>프런트 엔드 개체 만들기

프런트 엔드 개체는 Front Door에 대한 호스트 이름을 구성합니다. 기본적으로 호스트 이름은 * *.azurefd.net* 접미사를 갖습니다.

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>백 엔드 풀 만들기

백 엔드 풀은 이 빠른 시작을 시작할 때 생성된 두 개의 웹앱으로 구성됩니다. 이 단계에서 정의된 상태 프로브 및 부하 분산 설정은 기본값을 사용합니다.

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>라우팅 규칙 만들기

라우팅 규칙은 백 엔드 풀을 프런트 엔드 도메인에 매핑하고 기본 경로 일치 값을 “/*”로 설정합니다.

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>Front Door 만들기

이제 필요한 개체를 만들었으므로 Front Door를 만듭니다.

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

배포가 성공적으로 완료되면 다음 섹션의 단계에 따라 테스트를 수행할 수 있습니다.

## <a name="test-the-front-door"></a>Front Door 테스트

다음 명령을 실행하여 Front Door에 대한 호스트 이름을 가져옵니다.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

웹 브라우저를 열고 명령에서 호스트 이름 가져오기를 입력합니다. Front Door가 백 엔드 리소스 중 하나로 요청을 보냅니다. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Front Door 테스트 페이지":::

## <a name="clean-up-resources"></a>리소스 정리

Front Door로 만든 리소스가 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 Front Door 및 모든 관련 리소스도 함께 삭제됩니다. 

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 만들었습니다.
* Front Door
* 두 개의 웹앱

사용자 지정 도메인을 Front Door에 추가하는 방법을 알아보려면 Front Door 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [사용자 지정 도메인 추가](front-door-custom-domain.md)
