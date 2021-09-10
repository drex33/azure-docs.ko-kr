---
title: Private Link 구성
description: Private Link 구성
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: dfc0601dddddd89559d2a7bb28d6f3d86dcdf40c
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272370"
---
# <a name="configure-your-private-link"></a>Private Link 구성
Private Link를 구성하려면 몇 가지 단계가 필요합니다. 
* 리소스를 사용하여 프라이빗 링크 범위 만들기
* 네트워크에 프라이빗 엔드포인트 만들기 및 범위에 연결
* Azure Monitor 리소스에 필요한 액세스를 구성합니다.

이 문서에서는 Azure Portal을 통해 수행되는 방법을 검토하고 프로세스를 자동화하기 위한 ARM(Azure Resource Manager) 템플릿 예를 제공합니다. 

## <a name="create-a-private-link-connection-through-the-azure-portal"></a>Azure Portal을 통해 Private Link 연결 만들기
이 섹션에서는 Azure Portal을 통해 Private Link를 설정하는 프로세스를 단계별로 검토합니다. 명령줄 또는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Private Link를 만들고 관리하려면 [API 및 명령줄 사용](#use-apis-and-command-line)을 참조하세요.

### <a name="create-an-azure-monitor-private-link-scope"></a>Azure Monitor Private Link 범위 만들기

1. Azure Portal에서 **리소스 만들기** 로 이동하여 **Azure Monitor Private Link 범위** 를 검색합니다.

   ![Azure Monitor 프라이빗 링크 범위](./media/private-link-security/ampls-find-1c.png)

2. **만들기** 를 선택합니다.
3. 구독 및 리소스 그룹을 선택합니다.
4. AMPLS 이름을 지정합니다. "AppServerProdTelem"와 같이 의미 있고 명확한 이름을 사용하는 것이 가장 좋습니다.
5. **검토 + 생성** 를 선택합니다. 

   ![Azure Monitor 프라이빗 링크 범위 생성](./media/private-link-security/ampls-create-1d.png)

6. 유효성 검사를 통과하도록 한 다음, **생성** 을 선택합니다.

### <a name="connect-azure-monitor-resources"></a>Azure Monitor 리소스 연결

Azure Monitor 리소스(Log Analytics 작업 공간 및 Application Insights 구성 요소)를 AMPLS에 연결합니다.

1. Azure Monitor 프라이빗 링크 범위에서 왼쪽 메뉴의 **Azure Monitor 리소스** 를 클릭합니다. **추가** 단추를 선택합니다.
2. 작업 영역 또는 구성 요소를 추가합니다. **추가** 단추를 클릭하면 Azure Monitor 리소스를 선택할 수 있는 대화 상자가 표시됩니다. 구독 및 리소스 그룹을 찾아보거나 이름을 입력하여 이를 필터링할 수 있습니다. 작업 영역 또는 구성 요소를 선택하고, **적용** 을 클릭하여 이를 범위에 추가합니다.

    ![범위 선택 UX의 스크린샷](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Azure Monitor 리소스를 삭제하려면 먼저 연결된 모든 AMPLS 개체에서 리소스의 연결을 끊어야 합니다. AMPLS에 연결된 리소스는 삭제할 수 없습니다.

### <a name="connect-to-a-private-endpoint"></a>프라이빗 엔드포인트에 연결

이제 AMPLS에 연결된 리소스가 있으므로 네트워크를 연결 하는 프라이빗 엔드포인트를 만듭니다. 다음 예에서와 같이 이 작업은 [Azure Portal Private Link 센터](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) 또는 Azure Monitor Private Link 범위 내에서 수행할 수 있습니다.

1. 범위 리소스에서 왼쪽 리소스 메뉴에서 **프라이빗 엔드포인트 연결** 을 클릭합니다. **프라이빗 엔드포인트** 를 클릭하여 엔드포인트 생성 프로세스를 시작합니다. 그것들을 선택하고 **승인** 을 클릭하여 여기 프라이빗 링크 센터에서 시작된 연결을 승인할 수도 있습니다.

    ![프라이빗 엔드포인트 연결 UX의 스크린샷](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. 구독, 리소스 그룹, 엔드포인트 이름 및 거주하는 지역을 선택합니다. 지역은 연결할 가상 네트워크와 동일한 영역이어야 합니다

3. 완료되면 **다음: 리소스** 를 선택합니다. 

4. [리소스] 화면에서

   a. Azure Monitor 프라이빗 범위 리소스가 포함된 **구독** 을 선택합니다. 

   b. **리소스 종류** 에 대해 **Microsoft.insights/privateLinkScopes** 를 선택합니다. 

   다. **리소스** 드롭다운에서 이전에 만든 Private Link 범위를 선택합니다. 

   d. **다음: 구성 >** 을 선택합니다.
      ![프라이빗 엔드포인트 만들기 선택의 스크린샷](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. 구성 창에서

   a.    Azure Monitor 리소스에 연결하려는 **가상 네트워크** 및 **서브넷** 을 선택합니다. 
 
   b.    **프라이빗 DNS 영역과 통합** 에 대해 **예** 를 선택하고, 새 프라이빗 DNS 영역을 자동으로 만들도록 합니다. 실제 DNS 영역은 아래 스크린샷에 표시된 것과 다를 수도 있습니다. 
   > [!NOTE]
   > **아니요** 를 선택하고 DNS 레코드를 수동으로 관리하려면, 먼저 이 프라이빗 엔드포인트 및 AMPLS 구성을 포함한 프라이빗 링크 설정을 완료합니다. 그런 다음 [Azure 프라이빗 엔드포인트 DNS 구성](../../private-link/private-endpoint-dns.md)의 지침에 따라 DNS를 구성합니다. Private Link 설정을 위한 준비로 빈 레코드를 만들지 않도록 합니다. 만드는 DNS 레코드는 기존 설정을 재정의하고 Azure Monitor와의 연결에 영향을 줄 수 있습니다.
 
   다.    **검토 + 만들기** 를 선택합니다.
 
   d.    유효성 검사를 통과하도록 합니다. 
 
   e.    **만들기** 를 선택합니다. 

    ![프라이빗 엔드포인트 선택의 세부 사항 스크린샷.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

이제 이 AMPLS에 연결된 새 프라이빗 엔드포인트를 만들었습니다.


## <a name="configure-access-to-your-resources"></a>리소스에 대한 액세스 구성
지금까지 네트워크 구성에 대해 설명했지만 모니터링되는 리소스(Log Analytics 작업 영역 및 Application Insights 구성 요소)에 대한 네트워크 액세스를 구성하는 방법도 고려해야 합니다.

Azure Portal로 이동합니다. 리소스 메뉴의 왼쪽에는 **네트워크 격리** 라는 메뉴 항목이 있습니다. 이 페이지는 Private Link를 통해 리소스에 연결할 수 있는 네트워크와 다른 네트워크에서 리소스에 연결할 수 있는지 여부를 모두 제어합니다.


> [!NOTE]
> 2021년 8월 16일부터 네트워크 격리가 엄격하게 시행됩니다. 공용 네트워크의 쿼리를 차단하도록 설정된 리소스와 개인 네트워크(AMPLS를 통해)에 연결되지 않은 리소스는 모든 네트워크의 쿼리 수락을 중지합니다.

![LA 네트워크 격리](./media/private-link-security/ampls-network-isolation.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>연결된 Azure Monitor 프라이빗 링크 범위
여기에서 Azure Monitor 프라이빗 링크 범위에 대한 리소스 연결을 검토하고 구성할 수 있습니다. 범위(AMPLS)에 연결하면 각 AMPLS에 연결된 가상 네트워크의 트래픽이 이 리소스에 도달할 수 있습니다. 이는 [Azure Monitor 리소스 연결](#connect-azure-monitor-resources)에서와 같이 범위에서 연결하는 것과 동일한 효과가 있습니다. 

새 연결을 추가하려면 **추가** 를 선택하고 Azure Monitor 프라이빗 링크 범위를 선택합니다. **적용** 을 클릭하여 연결합니다. 리소스는 [AMPLS 제한 고려](./private-link-design.md#consider-ampls-limits)에 언급된 대로 5개의 AMPLS 개체에 연결할 수 있습니다.

### <a name="virtual-networks-access-configuration---managing-access-from-outside-of-private-links-scopes"></a>가상 네트워크 액세스 구성 - 프라이빗 링크 범위 외부에서 액세스 관리
이 페이지의 맨 아래에 있는 설정은 공용 네트워크에서의 액세스를 제어합니다. 즉, 나열된 범위에 연결되지 않은 네트워크(AMPLS)를 의미합니다.

**수집을 위한 공개 네트워크 액세스 허용** 을 **아니요** 로 설정하면 연결된 범위 외부의 클라이언트(컴퓨터, SDK 등)가 데이터를 업로드하거나 리소스에 로그를 보낼 수 없습니다.

**쿼리에 대한 공개 네트워크 액세스 허용** 을 **아니요** 로 설정하면 연결된 범위 외부의 클라이언트(컴퓨터, SDK 등)가 리소스의 데이터를 쿼리할 수 없습니다. 이 데이터에는 로그, 메트릭 및 라이브 메트릭 스트림에 대한 액세스뿐만 아니라 통합 문서, 대시보드, 쿼리 API 기반 클라이언트 환경, Azure Portal의 인사이트 등을 기반으로 하는 환경도 포함됩니다. Azure Portal 외부에서 실행 중인 경험과 이 쿼리 로그 분석 데이터도 프라이빗 링크된 VNET 내에서 실행되어야 합니다.


## <a name="use-apis-and-command-line"></a>API 및 명령줄 사용

Azure Resource Manager 템플릿과 명령 줄 인터페이스를 사용하여 앞에서 설명한 프로세스를 자동화할 수 있습니다.

### <a name="create-and-manage-azure-monitor-private-link-scopes-ampls"></a>AMPLS(Azure Monitor Private Link 범위) 만들기 및 관리
프라이빗 링크 범위를 생성하고 관리하려면 [REST API](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) 혹은 [Azure CLI(az monitor private-link-scope)](/cli/azure/monitor/private-link-scope)를 사용합니다.

#### <a name="create-ampls-with-open-access-modes---cli-example"></a>공개 액세스 모드로 AMPLS 만들기 - CLI 예제
아래 CLI 명령은 쿼리 및 수집 액세스 모드가 모두 Open으로 설정된 ‘my-scope’라는 새 AMPLS 리소스를 만듭니다.
```
az resource create -g "my-resource-group" --name "my-scope" --api-version "2021-07-01-preview" --resource-type Microsoft.Insights/privateLinkScopes --properties "{\"accessModeSettings\":{\"queryAccessMode\":\"Open\", \"ingestionAccessMode\":\"Open\"}}"
```

#### <a name="create-ampls-with-mixed-access-modes---powershell-example"></a>혼합 액세스 모드를 사용하여 AMPLS 만들기 - PowerShell 예제
아래 PowerShell 스크립트는 쿼리 액세스 모드는 Open으로 설정되지만 수집 액세스 모드는 PrivateOnly(AMPLS의 리소스로만 수집을 허용한다는 의미)로 설정된 ‘my-scope’라는 새 AMPLS 리소스를 만듭니다.

```
# scope details
$scopeSubscriptionId = "ab1800bd-ceac-48cd-...-..."
$scopeResourceGroup = "my-resource-group"
$scopeName = "my-scope"
$scopeProperties = @{
    accessModeSettings = @{
        queryAccessMode     = "Open"; 
        ingestionAccessMode = "PrivateOnly"
    } 
}

# login
Connect-AzAccount

# select subscription
Select-AzSubscription -SubscriptionId $scopeSubscriptionId

# create private link scope resource
$scope = New-AzResource -Location "Global" -Properties $scopeProperties -ResourceName $scopeName -ResourceType "Microsoft.Insights/privateLinkScopes" -ResourceGroupName $scopeResourceGroup -ApiVersion "2021-07-01-preview" -Force
```

#### <a name="create-ampls---azure-resource-manager-template-arm-template"></a>AMPLS 만들기 - ARM 템플릿(Azure Resource Manager 템플릿)
아래 Azure Resource Manager 템플릿은 다음을 만듭니다.
* 이름이 "my-scope"인 프라이빗 링크 범위(AMPLS)
* 이름이 "my-workspace"인 Log Analytics 작업 영역
* 범위 리소스를 "my-scope" AMPLS(이름: "my-workspace-connection")에 추가
> [!NOTE]
> 아래 ARM 템플릿은 AMPLS 액세스 모드 설정을 지원하지 않는 이전 API 버전을 사용합니다. 아래 템플릿을 사용하면 결과 AMPLS는 QueryAccessMode="Open" 및 IngestionAccessMode="PrivateOnly"로 설정됩니다. 즉, AMPLS 안팎의 리소스에서 쿼리를 실행할 수 있지만 Private Link 리소스에만 도달하도록 허용합니다.

```
{
    "$schema": https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#,
    "contentVersion": "1.0.0.0",
    "parameters": {
        "private_link_scope_name": {
            "defaultValue": "my-scope",
            "type": "String"
        },
        "workspace_name": {
            "defaultValue": "my-workspace",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.insights/privatelinkscopes",
            "apiVersion": "2019-10-17-preview",
            "name": "[parameters('private_link_scope_name')]",
            "location": "global",
            "properties": {}
        },
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-10-01",
            "name": "[parameters('workspace_name')]",
            "location": "westeurope",
            "properties": {
                "sku": {
                    "name": "pergb2018"
                },
                "publicNetworkAccessForIngestion": "Enabled",
                "publicNetworkAccessForQuery": "Enabled"
            }
        },
        {
            "type": "microsoft.insights/privatelinkscopes/scopedresources",
            "apiVersion": "2019-10-17-preview",
            "name": "[concat(parameters('private_link_scope_name'), '/', concat(parameters('workspace_name'), '-connection'))]",
            "dependsOn": [
                "[resourceId('microsoft.insights/privatelinkscopes', parameters('private_link_scope_name'))]",
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            ],
            "properties": {
                "linkedResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            }
        }
    ]
}
```

### <a name="set-ampls-access-flags---powershell-example"></a>AMPLS 액세스 플래그 설정 - PowerShell 예제
AMPLS에서 액세스 모드 플래그를 설정하려면 다음 PowerShell 스크립트를 사용할 수 있습니다. 다음 스크립트는 플래그를 Open으로 설정합니다. 프라이빗 전용 모드를 사용하려면 ‘PrivateOnly’ 값을 사용합니다.

```
# scope details
$scopeSubscriptionId = "ab1800bd-ceac-48cd-...-..."
$scopeResourceGroup = "my-resource-group-name"
$scopeName = "my-scope"

# login
Connect-AzAccount

# select subscription
Select-AzSubscription -SubscriptionId $scopeSubscriptionId

# get private link scope resource
$scope = Get-AzResource -ResourceType Microsoft.Insights/privateLinkScopes -ResourceGroupName $scopeResourceGroup -ResourceName $scopeName -ApiVersion "2021-07-01-preview"

# set access mode settings
$scope.Properties.AccessModeSettings.QueryAccessMode = "Open";
$scope.Properties.AccessModeSettings.IngestionAccessMode = "Open";
$scope | Set-AzResource -Force
```

### <a name="set-resource-access-flags"></a>리소스 액세스 플래그 설정
작업 영역 또는 구성 요소 액세스 플래그를 관리하려면 [Log Analytics 작업 영역](/cli/azure/monitor/log-analytics/workspace) 또는 [Application Insights 구성 요 소](/cli/azure/ext/application-insights/monitor/app-insights/component)에 `[--ingestion-access {Disabled, Enabled}]` 및 `[--query-access {Disabled, Enabled}]` 플래그를 사용합니다.


## <a name="review-and-validate-your-private-link-setup"></a>프라이빗 링크 설정 검토 및 유효성 검사

### <a name="reviewing-your-endpoints-dns-settings"></a>엔드포인트의 DNS 설정 검토
만든 프라이빗 엔드포인트에는 5개의 DNS 영역이 구성되어 있어야 합니다.

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net
* privatelink-blob-core-windows-net

> [!NOTE]
> 이러한 각 영역은 특정 Azure Monitor 엔드포인트를 VNet의 IP 풀에서 프라이빗 IP에 매핑합니다. 아래 이미지에 표시된 IP 주소는 예시일 뿐입니다. 대신 구성에 사용자 네트워크의 프라이빗 IP가 표시되어야 합니다.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
이 영역에는 Azure Monitor에서 사용하는 전역 엔드포인트가 포함되어 있으며, 이러한 엔드포인트는 특정 리소스가 아닌 모든 리소스를 고려하는 요청을 처리합니다. 이 영역에는 다음에 대한 엔드포인트가 매핑되어야 합니다.
* `in.ai` - Application Insights 수집 엔드포인트(글로벌 항목과 지역 항목 모두)
* `api` - Application Insights 및 Log Analytics API 엔드포인트
* `live` - Application Insights 라이브 메트릭 엔드포인트
* `profiler` - Application Insights 프로파일러 엔드포인트
* `snapshot` - Application Insights 스냅샷 엔드포인트 [![프라이빗 DNS 영역 모니터의 스크린샷-azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
이 영역에서는 OMS 엔드포인트에 대한 작업 영역별 매핑을 다룹니다. 이 프라이빗 엔드포인트와 연결된 AMPLS에 연결된 각 작업 영역에 대한 항목이 표시됩니다.
[![프라이빗 DNS 영역의 스크린샷 oms-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
이 영역에서는 Log Analytics의 수집 엔드포인트와 ODS 엔드포인트에 특정한 작업 영역별 매핑을 다룹니다. 이 프라이빗 엔드포인트와 연결된 AMPLS에 연결된 각 작업 영역에 대한 항목이 표시됩니다.
[![프라이빗 DNS 영역의 스크린샷 ods-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
이 영역에서는 에이전트 서비스 자동화 엔드포인트에 특정한 작업 영역별 매핑을 다룹니다. 이 프라이빗 엔드포인트와 연결된 AMPLS에 연결된 각 작업 영역에 대한 항목이 표시됩니다.
[![프라이빗 DNS 영역 에이전트의 스크린샷 svc-azure-automation-net.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>privatelink-blob-core-windows-net
이 영역은 글로벌 에이전트의 솔루션 팩 저장소 계정에 대한 연결을 구성합니다. 이를 통해 에이전트는 새 솔루션 팩 또는 업데이트된 솔루션 팩(관리 팩이라고도 함)을 다운로드할 수 있습니다. 사용된 작업 영역 수에 관계없이 Log Analytics 에이전트를 처리하려면 하나의 항목만 필요합니다.
[![프라이빗 DNS 영역 blob-core-windows-net의 스크린샷](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> 이 항목은 2021년 4월 19일 이후에(또는 Azure 소버린 클라우드에서 2021년 6월부터) 만든 프라이빗 링크 설정에만 추가됩니다.


### <a name="validating-you-are-communicating-over-a-private-link"></a>프라이빗 링크를 통해 통신하고 있는지 확인
* 이제 프라이빗 엔드포인트를 통해 요청이 전송되어 있는지 확인하려면 네트워크 추적 도구 또는 브라우저를 사용하여 검토할 수 있습니다. 예를 들어, 작업 영역 또는 애플리케이션을 쿼리하려는 경우 요청이 API 엔드포인트에 매핑되는 프라이빗 IP로 전송되었는지 확인해야 하며, 이 예제에서는 *172.17.0.9* 입니다.

    참고: 일부 브라우저는 다른 DNS 설정을 사용할 수도 있습니다 ( [브라우저 DNS 설정](./private-link-design.md#browser-dns-settings)참조). DNS 설정이 적용되는지 확인합니다.

* 작업 영역 또는 구성 요소가 공용 네트워크에서 요청을 받지 않도록 하려면(AMPLS을 통해 연결되지 않음), [리소스에 대한 액세스 구성](#configure-access-to-your-resources)에 설명된 대로 리소스의 공개 수집 및 쿼리 플래그를 *아니요* 로 설정합니다.

* 보호된 네트워크의 클라이언트에서 `nslookup` DNS 영역에 나열된 엔드포인트 중 하나를 사용합니다. DNS 서버에서 기본적으로 확인되는 공용 IP 대신 매핑된 프라이빗 IP로 확인되어야 합니다.


## <a name="next-steps"></a>다음 단계

- [개인 스토리지](private-storage.md)에 대한 자세한 정보
- [Automation을 위한 Private Link](../../automation/how-to/private-link-security.md)에 대해 알아보기