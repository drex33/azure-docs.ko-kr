---
title: Azure App Service 액세스 제한
description: 액세스 제한을 설정하여 Azure App Service에서 앱을 보호하는 방법을 알아봅니다.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 715c352a0c81ce2fe3abceaef04e85d89383fa58
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133439763"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Azure App Service 액세스 제한 설정

액세스 제한을 설정하여 앱에 대한 네트워크 액세스를 제어하는 ​​우선 순위가 지정된 허용/거부 목록을 정의할 수 있습니다. 이 목록에는 IP 주소나 Azure Virtual Network 서브넷이 포함될 수 있습니다. 하나 이상의 항목이 있는 경우 목록 끝에 암시적 *모두 거부* 가 표시됩니다.

액세스 제한 기능은 모든 Azure App Service 호스팅 워크로드와 함께 작동합니다. 워크로드에는 웹앱, API 앱, Linux 앱, Linux 사용자 지정 컨테이너 및 Functions가 포함될 수 있습니다.

앱에 대한 요청이 수행될 경우 액세스 제한 목록 규칙과 비교하여 FROM 주소를 평가합니다. FROM 주소가 Microsoft.Web에 대한 서비스 엔드포인트로 구성된 서브넷에 있는 경우 원본 서브넷을 액세스 제한 목록의 가상 네트워크 규칙과 비교합니다. 주소가 목록의 규칙을 기준으로 허용되지 않을 경우 서비스는 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 상태 코드로 답변합니다.

액세스 제한 기능은 코드가 실행되는 작업자 역할의 업스트림에 해당하는 App Service 프런트 엔드 역할에 구현됩니다. 따라서 액세스 제한은 사실상 네트워크 ACL(액세스 제어 목록)입니다.

Azure 가상 네트워크에서 웹앱에 대한 액세스를 제한하는 기능은 [서비스 엔드포인트 ][serviceendpoints]를 통해 사용으로 설정됩니다. 서비스 엔드포인트를 사용하면 선택한 서브넷에서 다중 테넌트 서비스에 대한 액세스를 제한할 수 있습니다. App Service Environment에서 호스트하는 앱에 대한 트래픽을 제한하는 것은 작동하지 않습니다. App Service Environment에 있는 경우 IP 주소 규칙을 적용하여 해당 앱에 대한 액세스를 제어할 수 있습니다.

> [!NOTE]
> 서비스 엔드포인트는 네트워킹 측과 사용으로 설정되는 Azure 서비스 모두에서 사용으로 설정되어야 합니다. 서비스 엔드포인트를 지원하는 Azure 서비스 목록은 [Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요.
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="액세스 제한 흐름 다이어그램.":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>포털에서 액세스 제한 규칙 관리

앱에 액세스 제한 규칙을 추가하려면 다음을 수행합니다.

1. Azure Portal에 로그인합니다.

1. 왼쪽 창에서 **네트워킹** 을 선택합니다.

1. **네트워킹** 창의 **액세스 제한** 에서 **액세스 제한 구성** 을 선택합니다.

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Azure Portal의 App Service 네트워킹 옵션 창 스크린샷.":::

1. **액세스 제한** 페이지에서 앱에 정의된 액세스 제한 규칙의 목록을 검토합니다.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="선택한 앱에 정의된 액세스 제한 규칙 목록을 보여 주는 Azure Portal의 액세스 제한 페이지 스크린샷.":::

   목록에는 앱에 적용되는 현재 제한 사항이 모두 표시됩니다. 앱에 가상 네트워크 제한이 있는 경우 이 테이블에는 서비스 엔드포인트가 Microsoft.Web에 사용하도록 설정되어 있는지 표시됩니다. 앱에 제한이 정의되어 있지 않으면 어디서나 앱에 액세스할 수 있습니다.

### <a name="add-an-access-restriction-rule"></a>액세스 제한 규칙 추가

앱에 액세스 제한 규칙을 추가하려면 **액세스 제한** 창에서 **규칙 추가** 를 선택합니다. 규칙을 추가하면 즉시 적용됩니다. 

규칙은 **우선 순위** 열의 가장 작은 번호부터 우선적으로 적용됩니다. 단일 규칙을 추가해도 암시적 *모두 거부* 가 적용됩니다.

**액세스 제한 추가** 창에서 규칙을 만들 때 다음을 수행합니다.

1. **작업** 에서 **허용** 또는 **거부** 를 선택합니다.  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="‘액세스 제한 추가’ 창의 스크린샷.":::

1. 선택적으로 규칙의 이름과 설명을 입력합니다.
1. **우선 순위** 상자에서 우선 순위 값을 입력합니다.
1. **형식** 드롭다운 목록에서 규칙의 형식을 선택합니다.

다양한 규칙 형식은 다음 섹션에서 설명합니다.

> [!NOTE]
> - 액세스 제한 규칙은 512개로 제한됩니다. 512개가 넘는 액세스 제한 규칙이 필요하면 Azure Front Door, Azure App Gateway 또는 대체 WAF와 같은 독립 실행형 보안 제품을 설치하는 것이 좋습니다.
>
#### <a name="set-an-ip-address-based-rule"></a>IP 주소 기반 규칙 설정

이전 섹션에 간략히 설명된 프로시저를 따르되, 다음과 같은 추가 작업을 수행합니다.
* 4단계의 **형식** 드롭다운 목록에서 **IPv4** 나 **IPv6** 을 선택합니다. 

IPv4와 IPv6 둘 다 CIDR(Classless Inter-Domain Routing) 표기법을 사용하여 **IP 주소 블록** 을 지정합니다. 주소를 지정하려면 *1.2.3.4/32* 와 같이 처음 4개의 8진수로 IP 주소를 나타내고 */32* 로 마스크를 나타낼 수 있습니다. 모든 주소에 대한 IPv4 CIDR 표기법은 0.0.0.0/0입니다. CIDR 표기법에 관한 자세한 내용은 [Classless Inter-domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)을 참조하세요. 

#### <a name="set-a-service-endpoint-based-rule"></a>서비스 엔드포인트 기반 규칙 설정

* 4단계의 **형식** 드롭다운 목록에서 **Virtual Network** 를 선택합니다.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="Virtual Network 형식이 선택된 ‘제한 추가’ 창의 스크린샷.":::

액세스를 제한하려는 항목과 일치하는 **구독**, **Virtual Network** 및 **서브넷** 드롭다운 목록을 지정합니다.

서비스 엔드포인트를 사용하여 선택한 Azure 가상 네트워크 서브넷에 대한 액세스를 제한할 수 있습니다. 선택한 서브넷에 대한 서비스 엔드포인트가 아직 Microsoft 웹에서 사용하도록 설정되지 않은 경우 **누락된 Microsoft.Web 서비스 엔드포인트를 무시** 확인란을 선택하지 않으면 자동으로 사용하도록 설정됩니다. 앱에서 서비스 엔드포인트는 사용하고 서브넷은 사용하지 않도록 설정할 수 있는 시나리오는 대체로 서브넷에서 사용하도록 설정할 권한이 있는지에 따라 달라집니다. 

다른 사용자가 서브넷에서 서비스 엔드포인트를 사용하도록 설정해야 하는 경우 **누락된 Microsoft.Web 서비스 엔드포인트를 무시** 확인란을 선택합니다. 앱은 나중에 서브넷에서 사용하도록 설정할 것을 예측하여 서비스 엔드포인트에 대해 구성됩니다. 

서비스 엔드포인트를 사용하여 App Service Environment에서 실행되는 앱에 대한 액세스를 제한할 수 없습니다. 앱이 App Service Environment에 있는 경우 IP 액세스 규칙을 적용하여 해당 앱에 대한 액세스를 제어할 수 있습니다. 

서비스 엔드포인트를 사용하면 애플리케이션 게이트웨이 또는 기타 WAF(웹 애플리케이션 방화벽) 디바이스로 앱을 구성할 수 있습니다. 또한 보안 백 엔드를 사용하여 다중 계층 애플리케이션을 구성할 수 있습니다. 자세한 내용은 [네트워킹 기능 및 App Service](networking-features.md)와 [서비스 엔드포인트와 Application Gateway 통합](networking/app-gateway-with-service-endpoints.md)을 참조하세요.

> [!NOTE]
> - 현재 VIP(가상 IP)에서 IP 기반 TLS/SSL 바인딩을 사용하는 웹앱에는 서비스 엔드포인트가 지원되지 않습니다.
>
#### <a name="set-a-service-tag-based-rule"></a>서비스 태그 기반 규칙 설정

* 4단계의 **형식** 드롭다운 목록에서 **서비스 태그** 를 선택합니다.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png?v2" alt-text="서비스 태그 형식이 선택된 ‘제한 추가’ 창의 스크린샷.":::

각 서비스 태그는 Azure 서비스의 IP 범위 목록을 나타냅니다. 이 서비스 목록과 특정 범위의 링크는 [서비스 태그 문서][servicetags]에서 찾을 수 있습니다.

사용 가능한 모든 서비스 태그는 액세스 제한 규칙에서 지원됩니다. 간단히 하기 위해 가장 일반적인 태그 목록만 Azure Portal을 통해 사용할 수 있습니다. Azure Resource Manager 템플릿이나 스크립팅을 사용하여 지역 범위 규칙과 같은 고급 규칙을 구성합니다. Azure Portal을 통해 사용 가능한 태그는 다음과 같습니다.

* ActionGroup
* ApplicationInsightsAvailability
* AzureCloud
* AzureCognitiveSearch
* AzureEventGrid
* AzureFrontDoor.Backend
* AzureMachineLearning
* AzureTrafficManager
* LogicApps

### <a name="edit-a-rule"></a>규칙 편집

1. 기존 액세스 제한 규칙 편집을 시작하려면 **액세스 제한** 페이지에서 편집하려는 규칙을 선택합니다.

1. **액세스 제한 편집** 창에서 변경한 후 **규칙 업데이트** 를 선택합니다. 우선 순위 변경을 비롯한 편집 내용은 즉시 적용됩니다.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="기존 액세스 제한 규칙의 필드를 보여 주는 Azure Portal의 ‘액세스 제한 편집’ 창의 스크린샷.":::

   > [!NOTE]
   > 규칙을 편집할 때 규칙 형식 간에 전환할 수 없습니다. 

### <a name="delete-a-rule"></a>규칙 삭제

규칙을 삭제하려면 **액세스 제한** 페이지에서 삭제하려는 규칙 옆에 있는 줄임표( **...** )를 선택한 다음, **제거** 를 선택합니다.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="삭제할 액세스 제한 규칙 옆에 ‘제거’ 줄임표가 표시된 ‘액세스 제한’ 페이지의 스크린샷.":::

## <a name="access-restriction-advanced-scenarios"></a>액세스 제한 고급 시나리오
다음 섹션에서는 액세스 제한을 사용하는 몇 가지 고급 시나리오에 관해 설명합니다.

### <a name="filter-by-http-header"></a>http 헤더로 필터링

모든 규칙의 일부로 추가 http 헤더 필터를 추가할 수 있습니다. 지원되는 http 헤더 이름은 다음과 같습니다.
* X-Forwarded-For
* X-Forwarded-Host
* X-Azure-FDID
* X-FD-HealthProbe

헤더 이름마다 최대 8개의 값을 쉼표로 구분하여 추가할 수 있습니다. http 헤더 필터는 규칙 자체 후에 평가되며 규칙을 적용하려면 두 조건이 모두 참이어야 합니다.

### <a name="multi-source-rules"></a>다중 원본 규칙

다중 원본 규칙을 사용하면 단일 규칙에 최대 8개의 IP 범위 또는 8개의 서비스 태그를 결합할 수 있습니다. 512개가 넘는 IP 범위가 있거나 여러 IP 범위가 단일 http 헤더 필터와 결합되는 논리 규칙을 만들려는 경우 사용할 수 있습니다.

다중 원본 규칙은 단일 원본 규칙을 정의하는 것과 같은 방식으로 정의되지만 각 범위는 쉼표로 구분됩니다.

PowerShell 예제:

  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Multi-source rule" -IpAddress "192.168.1.0/24,192.168.10.0/24,192.168.100.0/24" `
    -Priority 100 -Action Allow
  ```

### <a name="block-a-single-ip-address"></a>단일 IP 주소 차단

첫 번째 액세스 제한 규칙을 추가하면 서비스에서 우선 순위가 2147483647인 명시적 *모두 거부* 규칙을 추가합니다. 실제로 명시적 *모두 거부* 규칙은 실행될 최종 규칙이며 *허용* 규칙에서 명시적으로 허용하지 않는 모든 IP 주소에 대한 액세스를 차단합니다.

단일 IP 주소나 IP 주소 블록을 명시적으로 차단하지만, 다른 모든 항목에 대한 액세스는 허용하려는 시나리오에서는 명시적인 *모두 허용* 규칙을 추가합니다.

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="차단된 단일 IP 주소를 표시하는 Azure Portal의 ‘액세스 제한’ 페이지 스크린샷.":::

### <a name="restrict-access-to-an-scm-site"></a>SCM 사이트에 대한 액세스 제한 

앱에 대한 액세스를 제어할 수 있을 뿐만 아니라 앱에서 사용하는 SCM 사이트에 대한 액세스를 제한할 수 있습니다. SCM 사이트는 웹 배포 엔드포인트이자 Kudu 콘솔입니다. 앱에서 SCM 사이트에 대한 액세스 제한을 별도로 할당하거나 앱과 SCM 사이트 모두에 대해 같은 제한 세트를 사용할 수 있습니다. **\<app name>과 같은 제한 사항** 확인란을 선택하면 모든 항목을 비웁니다. 확인란을 선택 취소하면 SCM 사이트 설정이 다시 적용됩니다. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="SCM 사이트 또는 앱에 대한 액세스 제한이 설정되어 있지 않음을 보여 주는 Azure Portal의 ‘액세스 제한’ 페이지 스크린샷.":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance"></a>특정 Azure Front Door 인스턴스에 대한 액세스 제한
Azure Front Door에서 애플리케이션으로의 트래픽은 AzureFrontDoor.Backend 서비스 태그에 정의된 잘 알려진 IP 범위 세트에서 시작됩니다. 서비스 태그 제한 규칙을 사용하여 Azure Front Door에서 시작되는 트래픽으로만 제한할 수 있습니다. 트래픽이 특정 인스턴스에서만 시작되도록 하려면 Azure Front Door가 보내는 고유한 http 헤더를 기반으로 들어오는 요청을 추가로 필터링해야 합니다.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-frontdoor.png?v2" alt-text="Azure Front Door 제한을 추가하는 방법을 보여 주는 Azure Portal의 ‘액세스 제한’ 페이지 스크린샷.":::

PowerShell 예제:

  ```azurepowershell-interactive
  $afd = Get-AzFrontDoor -Name "MyFrontDoorInstanceName"
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
    -HttpHeader @{'x-azure-fdid' = $afd.FrontDoorId}
  ```

## <a name="manage-access-restriction-rules-programmatically"></a>프로그래매틱 방식으로 액세스 제한 규칙 관리

다음 중 하나를 수행하여 프로그래매틱 방식으로 액세스 제한을 추가할 수 있습니다. 

* [Azure CLI](/cli/azure/webapp/config/access-restriction)를 사용합니다. 예를 들면 다음과 같습니다.
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

   > [!NOTE]
   > Azure CLI에서 서비스 태그, http 헤더 또는 다중 원본 규칙을 사용하려면 2.23.0 이상의 버전이 필요합니다. ```az version```로 설치된 모듈의 버전을 확인할 수 있습니다.

* [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule)을 사용합니다. 예를 들면 다음과 같습니다.


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```
   > [!NOTE]
   > Azure PowerShell에서 서비스 태그, http 헤더 또는 다중 원본 규칙을 사용하려면 5.7.0 이상의 버전이 필요합니다. ```Get-InstalledModule -Name Az```로 설치된 모듈의 버전을 확인할 수 있습니다.

다음 중 하나를 수행하여 수동으로 값을 설정할 수도 있습니다.

* Azure Resource Manager의 앱 구성에서 [Azure REST API](/rest/api/azure/) PUT 작업을 사용합니다. Azure Resource Manager에서 이 정보는 다음 위치에 제공됩니다.

  management.azure.com/subscriptions/**구독 ID**/resourceGroups/**리소스 그룹**/providers/Microsoft.Web/sites/**웹앱 이름**/config/web?api-version=2020-06-01

* Resource Manager 템플릿을 사용합니다. 예를 들어, resources.azure.com을 사용하고, ipSecurityRestrictions 블록을 편집하여 필요한 JSON을 추가할 수 있습니다.

  앞의 예제에 대한 JSON 구문은 다음과 같습니다.

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```
  서비스 태그와 http 헤더 제한을 사용하는 고급 예제의 JSON 구문은 다음과 같습니다.
  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "AzureFrontDoor.Backend",
          "tag": "ServiceTag",
          "action": "Allow",
          "priority": 100,
          "name": "Azure Front Door example",
          "headers": {
            "x-azure-fdid": [
              "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
            ]
          }
        }
      ]
    }
  }
  ```
## <a name="set-up-azure-functions-access-restrictions"></a>Azure Functions 액세스 제한 설정

App Service 플랜과 같은 기능을 사용하는 함수 앱에 대한 액세스 제한도 사용할 수 있습니다. 액세스 제한을 사용하는 경우 허용되지 않는 IP에 대해 Azure Portal 코드 편집기도 사용하지 않게 설정합니다.

## <a name="next-steps"></a>다음 단계
[Azure Functions에 대한 액세스 제한](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[서비스 엔드포인트와 Application Gateway 통합](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md
