---
title: Service Fabric 관리형 클러스터에 대한 네트워크 설정 구성
description: NSG 규칙, RDP 포트 액세스, 부하 분산 규칙 등에 대해 Service Fabric 관리형 클러스터를 구성하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 3482f414029c79ceea9c0ee8bcc258ed2fc495e1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558880"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters"></a>Service Fabric 관리형 클러스터에 대한 네트워크 설정 구성

Service Fabric 관리형 클러스터는 기본 네트워킹 구성을 사용하여 만들어집니다. 이 구성은 공용 IP가 있는 [Azure Load Balancer](../load-balancer/load-balancer-overview.md), 하나의 서브넷이 할당된 VNet 및 필수 클러스터 기능에 대해 구성된 NSG로 구성됩니다. 또한 고객 구성을 더 쉽게 하기 위해 기본값으로 모든 아웃바운드 트래픽을 허용하는 것과 같이 선택적 NSG 규칙이 적용됩니다. 이 문서에서는 다음 네트워킹 구성 옵션 등을 수정하는 방법을 안내합니다.

- [NSG 규칙 관리](#nsgrules)
- [RDP 액세스 관리](#rdp)
- [부하 분산 장치 구성 관리](#lbconfig)
- [IPv6 사용](#ipv6)
- [사용자 고유의 가상 네트워크 가져오기](#byovnet)
- [사용자 고유의 부하 분산 장치 가져오기](#byolb)

<a id="nsgrules"></a>
## <a name="manage-nsg-rules"></a>NSG 규칙 관리

### <a name="nsg-rules-guidance"></a>NSG 규칙 참고 자료

관리형 클러스터에 대한 새 NSG 규칙을 만들 때 다음의 고려 사항을 알고 있어야 합니다.

* Service Fabric 관리형 클러스터는 필수 기능을 위해 NSG 규칙 우선 순위 범위 0 ~ 999를 예약합니다. 1000보다 작은 우선 순위 값으로 사용자 지정 NSG 규칙을 만들 수는 없습니다.
* Service Fabric 관리형 클러스터는 선택적인 NSG 규칙을 만들기 위해 우선 순위 범위 3001 ~ 4000을 예약합니다. 이러한 규칙을 자동으로 추가하여 구성을 쉽고 빠르게 만들 수 있습니다. 우선 순위 범위 1000 ~ 3000에 사용자 지정 NSG 규칙을 추가하여 이러한 규칙을 재정의할 수 있습니다.
* 사용자 지정 NSG 규칙은 우선 순위가 범위 1000 ~ 3000 내에 있어야 합니다.

### <a name="apply-nsg-rules"></a>NSG 규칙 적용
Service Fabric 관리형 클러스터를 사용하여 배포 템플릿의 클러스터 리소스 내에서 직접 NSG 규칙을 할당할 수 있습니다.

*Microsoft.ServiceFabric/managedclusters* 리소스(버전 `2021-05-01` 이상)의 [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) 속성을 사용하여 NSG 규칙을 할당합니다. 예를 들면 다음과 같습니다.

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters",
  "properties": {
    "networkSecurityRules": [
      {
        "name": "AllowCustomers",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33000-33499",
        "access": "Allow",
        "priority": 2001,
        "direction": "Inbound"
      },
      {
        "name": "AllowARM",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "AzureResourceManager",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33500-33699",
        "access": "Allow",
        "priority": 2002,
        "direction": "Inbound"
      },
      {
        "name": "DenyCustomers",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33700-33799",
        "access": "Deny",
        "priority": 2003,
        "direction": "Outbound"
      },
      {
        "name": "DenyRDP",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "destinationPortRange": "3389",
        "access": "Deny",
        "priority": 2004,
        "direction": "Inbound",
        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
      }
    ],
    "fabricSettings": [
      "..."
    ]
  }
}
```

## <a name="clientconnection-and-httpgatewayconnection-default-and-optional-rules"></a>ClientConnection 및 HttpGatewayConnection 기본값 및 선택적 규칙
### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>NSG 규칙: SFMC_AllowServiceFabricGatewayToSFRP

Service Fabric 리소스 공급자가 클러스터의 clientConnectionPort 및 httpGatewayConnectionPort에 액세스할 수 있도록 기본 NSG 규칙이 추가됩니다. 이 규칙은 'ServiceFabric' 서비스 태그를 통해 포트에 대한 액세스를 허용합니다.

>[!NOTE]
>이 규칙은 항상 추가되며 재정의할 수 없습니다.

```json
{
    "name": "SFMC_AllowServiceFabricGatewayToSFRP",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.",
        "protocol": "TCP",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "ServiceFabric",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 500,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>NSG 규칙: SFMC_AllowServiceFabricGatewayPorts

이 선택적 규칙을 사용하면 고객이 SFX에 액세스하고, PowerShell을 통해 클러스터에 연결하며, clientConnectionPort 및 httpGatewayPort용 LB 포트를 열어 인터넷에서 Service Fabric 클러스터 API 엔드포인트를 사용할 수 있습니다.

>[!NOTE]
>동일한 포트에 대해 액세스, 방향, 프로토콜 값이 동일한 사용자 지정 규칙이 있는 경우 이 규칙은 추가되지 않습니다. 사용자 지정 NSG 규칙으로 이 규칙을 재정의할 수 있습니다.

```json
{
    "name": "SFMC_AllowServiceFabricGatewayPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3001,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

<a id="rdp"></a>
## <a name="enable-access-to-rdp-ports-from-internet"></a>인터넷에서 RDP 포트 액세스 사용

Service Fabric 관리형 클러스터는 기본값으로 인터넷에서 RDP 포트에 대한 액세스를 사용하도록 설정하지 않습니다. Service Fabric 관리형 클러스터 리소스에서 다음 속성을 설정하여 인터넷에서 RDP 포트에 대한 인바운드 액세스를 열 수 있습니다.

```json
"allowRDPAccess": true
```

allowRDPAccess 속성을 true로 설정하면 다음 NSG 규칙이 클러스터 배포에 추가됩니다.

```json
{
    "name": "SFMC_AllowRdpPort",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

Service Fabric 관리형 클러스터는 노드 형식의 각 인스턴스에 대한 인바운드 NAT 규칙을 자동으로 만듭니다. 특정 인스턴스(클러스터 노드)에 도달하는 포트 매핑을 찾으려면 다음 단계를 수행합니다.

Azure Portal을 사용하여 RDP(원격 데스크톱 프로토콜)에 대한 인바운드 NAT 규칙을 만든 관리형 클러스터를 찾습니다.

1. SFC_{cluster-id} 형식으로 명명된 구독 내에서 관리형 클러스터 리소스 그룹으로 이동합니다.

2. LB-{cluster-name} 형식을 사용하여 클러스터용 부하 분산 장치를 선택합니다.

3. 부하 분산 장치 페이지에서 인바운드 NAT 규칙을 선택합니다. 인바운드 NAT 규칙을 검토하여 노드의 대상 포트 매핑에 대한 인바운드 프런트 엔드 포트를 확인합니다. 

   다음 스크린샷에는 세 가지 다른 노드 형식에 대한 인바운드 NAT 규칙이 나와 있습니다.

   ![인바운드 NAT 규칙][Inbound-NAT-Rules]

   기본적으로 Windows 클러스터의 경우 프런트 엔드 포트 할당은 5만에서 시작 하 고 대상 포트는 포트 3389 이며, 대상 노드의 RDP 서비스에 매핑됩니다.
   >[!NOTE]
   > BYOLB 기능을 사용 하 고 RDP를 사용 하려는 경우에는 별도로 NAT 풀을 구성 해야 합니다. 이러한 노드 유형에 대 한 NAT 규칙을 자동으로 만들지 않습니다.

4. 특정 노드(확장 집합 인스턴스)에 원격으로 연결합니다. 클러스터를 만들 때 설정한 사용자 이름 및 암호 또는 구성한 다른 자격 증명을 사용할 수 있습니다.

다음 스크린샷에는 원격 데스크톱 연결을 사용하여 Windows 클러스터의 앱(인스턴스 0) 노드에 연결하는 방법이 나와 있습니다.

![원격 데스크톱 연결][sfmc-rdp-connect]

<a id="lbconfig"></a>
## <a name="modify-default-load-balancer-configuration"></a>기본 부하 분산 장치 구성 수정

### <a name="load-balancer-ports"></a>부하 분산 장치 포트

Service Fabric 관리형 클러스터는 *Managedcluster* 속성의 "loadBalancingRules" 섹션에 구성된 모든 부하 분산 장치(LB) 포트에 대해 기본 우선 순위 범위에서 NSG 규칙을 만듭니다. 이 규칙은 인터넷에서 인바운드 트래픽용 LB 포트를 엽니다.  

>[!NOTE]
>이 규칙은 선택적 우선 순위 범위에 추가되며 사용자 지정 NSG 규칙을 추가하여 재정의할 수 있습니다.

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

### <a name="load-balancer-probes"></a>부하 분산 장치 프로브

Service Fabric 관리형 클러스터는 관리형 클러스터 속성의 `loadBalancingRules` 섹션에 구성된 모든 포트뿐만 아니라 패브릭 게이트웨이 포트에 대한 부하 분산 장치 프로브를 자동으로 만듭니다.

```json
{
  "value": [
    {
        "name": "FabricTcpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19000,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "FabricHttpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "probe1_tcp_8080",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 8080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
            {
                "id": "<>"
            }
        ]
      },
      "type": "Microsoft.Network/loadBalancers/probes"
    }
  ]
}
```

<a id="ipv6"></a>
## <a name="enable-ipv6-preview"></a>IPv6 사용(미리 보기)
관리형 클러스터는 IPv6을 기본값으로 사용하도록 설정하지 않습니다. 이 기능은 Load Balancer 프런트 엔드에서 백 엔드 리소스까지 전체 이중 스택 IPv4/IPv6 기능을 사용하도록 설정합니다. 관리형 클러스터 부하 분산 장치 구성 또는 NSG 규칙을 변경하면 IPv4 및 IPv6 라우팅 모두에 영향을 미칩니다.

> [!NOTE]
> 이 설정은 포털에서 사용할 수 없으며 클러스터를 만든 후에는 변경할 수 없습니다.

1. Service Fabric 관리형 클러스터 리소스에서 다음 속성을 설정합니다.
   ```json
            "apiVersion": "2021-07-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                "enableIpv6": true
                },
            }
   ```

2. 사용자의 IPv6 사용 관리형 클러스터를 배포합니다. 필요에 따라 [샘플 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-IPv6)을 사용자 지정하거나 직접 빌드합니다.
   다음 예제에서는 `westus`에서 `MyResourceGroup`라는 리소스 그룹을 만들고 이 기능을 사용하도록 설정된 클러스터를 배포합니다.
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```
   배포 후 클러스터 가상 네트워크 및 리소스는 이중 스택이 됩니다. 결과적으로 클러스터 프런트 엔드 부하 분산 장치는 Azure Load Balancer의 공용 IPv6 주소와 VM의 개인 IPv6에 연결된 고유한 DNS 주소(예: `mycluster-ipv6.southcentralus.cloudapp.azure.com`)를 만듭니다. 


<a id="byovnet"></a>
## <a name="bring-your-own-virtual-network-preview"></a>사용자 고유의 가상 네트워크 불러오기(미리 보기)
이 기능을 통해 고객은 관리형 클러스터가 리소스를 배포할 전용 서브넷을 지정하여 기존 가상 네트워크를 사용할 수 있습니다. 이는 사용하려는 관련 보안 정책 및 트래픽 라우팅이 있는 VNet 및 서브넷이 이미 구성된 경우에 유용할 수 있습니다. 기존 가상 네트워크에 배포한 후 Azure ExpressRoute, Azure VPN Gateway, 네트워크 보안 그룹 및 가상 네트워크 피어링 등의 다른 네트워킹 기능을 쉽게 통합할 수 있습니다. 또한 필요한 경우 [자체 Azure Load Balancer 가져오기](#byolb)가 가능합니다.

> [!NOTE]
> BYOVNET을 사용 하는 경우 관리 되는 클러스터 리소스가 하나의 서브넷에 배포 됩니다. 

> [!NOTE]
> 클러스터가 만들어지고 관리형 클러스터가 제공된 서브넷에 NSG를 할당한 후에는 이 설정을 변경할 수 없습니다. NSG 할당을 재정의하지 마십시오. 그렇지 않으면 트래픽이 중단됩니다.


**사용자 고유의 가상 네트워크 불러오기**

1. Service Fabric 리소스 공급자 애플리케이션에 대한 구독에서 서비스 `Id`를 받습니다.
   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
   ```

   > [!NOTE]
   > 올바른 구독에 있는지 확인합니다. 구독이 다른 테넌트에 있는 경우 보안 주체 ID가 변경됩니다.

   ```powershell
   ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
   ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
   ObjectType            : ServicePrincipal
   DisplayName           : Azure Service Fabric Resource Provider
   Id                    : 00000000-0000-0000-0000-000000000000
   ```

   이후 단계에서 사용할 수 있도록 이전 출력의 **ID** 를 **principalId** 로 기록합니다.

   |역할 정의 이름|역할 정의 ID|
   |----|-------------------------------------|
   |네트워크 참가자|4d97b98b-1d4f-4787-a291-c67834d212e7|

   이후 단계에서 사용할 수 있도록 `Role definition name` 및 `Role definition ID` 속성 값을 기록합니다.

2. Service Fabric 리소스 공급자 애플리케이션으로 역할 할당을 추가합니다. 역할 할당을 추가하는 것은 일회성 작업입니다. 다음 PowerShell 명령을 실행하거나 아래에 설명된 대로 ARM(Azure Resource Manager) 템플릿을 구성하여 역할을 추가합니다. 

   다음 단계에서는 ExistingRG 리소스 그룹에서 ExistingRG-vnet이라는 기존 가상 네트워크로 시작합니다. 서브넷의 이름은 default입니다.

   기존 VNet에서 필요한 정보를 얻습니다.

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzVirtualNetwork -Name ExistingRG-vnet -ResourceGroupName ExistingRG
   ```
   이후 단계에서 사용할 응답의 `Subnets` 섹션에서 반환되는 다음 서브넷 이름 및 `Id` 속성 값을 기록합니다.

   ```JSON
   Subnets:[
   {
   ...
   "Id": "/subscriptions/<subscriptionId>/resourceGroups/Existing-RG/providers/Microsoft.Network/virtualNetworks/ExistingRG-vnet/subnets/default"
   }]
   ```

   보안 주체 ID, 2단계의 역할 정의 이름 및 위에서 얻은 할당 범위 `Id`를 사용하여 다음 PowerShell 명령을 실행합니다.
   ```powershell
   New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Network Contributor" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
   ```

   또는`principalId`, `roleDefinitionId`, `vnetName`, `subnetName`에 적절한 값으로 구성된 ARM(Azure Resource Manager) 템플릿을 사용하여 역할 할당을 추가할 수 있습니다.

   ```JSON
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2020-04-01-preview",
      "name": "[parameters('VNetRoleAssignmentID')]",
      "scope": "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
      ],
      "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7')]",
        "principalId": "00000000-0000-0000-0000-000000000000"
      }
   ```
   > [!NOTE]
   > VNetRoleAssignmentID는 [GUID](../azure-resource-manager/templates/template-functions-string.md#examples-16)여야 합니다. 템플릿에 이 역할 할당을 포함하여 재배포하는 경우 GUID가 원래 사용된 것과 동일한지 확인합니다. 이 리소스는 한 번만 만들면 되므로, 배포 후 클러스터 템플릿에서 격리하거나 제거하는 것이 좋습니다.

   다음은 이 단계에 사용할 수 있는 [VNet 서브넷을 만들고 역할 할당을 수행하는 ARM(Azure Resource Manager) 템플릿](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/master/SF-Managed-Standard-SKU-2-NT-BYOVNET/createVNet-assign-role.json)입니다.

3. 아래와 같이 역할이 설정된 후 클러스터 배포에 대한 `subnetId` 속성을 구성합니다.

   ```JSON
    "resources": [
        {
            "apiVersion": "2021-07-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            },
            "properties": {
                "subnetId": "subnetId",
            ...
            }
   ```
   [사용자 고유의 VNet 클러스터 샘플 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-BYOVNET)을 참조하거나 사용자 고유의 템플릿을 사용자 지정합니다.

4. 구성된 관리형 클러스터 ARM(Azure Resource Manager) 템플릿을 배포합니다.

   다음 예제에서는 `westus`에서 `MyResourceGroup`라는 리소스 그룹을 만들고 이 기능을 사용하도록 설정된 클러스터를 배포합니다.
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```

   사용자 고유의 VNet 서브넷을 가져오는 경우 리소스 공급자가 여전히 공용 엔드포인트를 만들고 관리하지만 구성된 서브넷에서 진행합니다. 이 기능을 사용하면 Azure Load Balancer에서 공용 IP/재사용 고정 IP를 지정할 수 없습니다. 기본적으로 지원되지 않는 부사 분산 장치나 다른 부하 분산 장치 시나리오가 필요한 경우 이 기능과 함께 [자체 Azure Load Balancer를 가져올 수 있습니다](#byolb).

<a id="byolb"></a>
## <a name="bring-your-own-azure-load-balancer-preview"></a>사용자 고유의 Azure Load Balancer 가져오기(미리 보기)
관리형 클러스터는 주 및 보조 노드 유형 모두에 대해 고정적인 공용 IP를 사용하여 Azure Load Balancer 및 정규화된 도메인 이름을 만듭니다. 이 기능을 사용하면 인바운드 및 아웃바운드 트래픽 모두에 대해 보조 노드 유형에 대한 Azure Load Balancer 만들거나 다시 사용할 수 있습니다. 고유한 Azure Load Balancer 가져오는 경우 다음을 수행할 수 있습니다.

* 개인 또는 공용 트래픽에 대해 미리 구성된 Load Balancer 고정 IP 주소 사용
* 특정 노드 형식에 Load Balancer 매핑
* 각 노드 유형이 고유한 NSG를 사용하여 자체 서브넷에 배포되기 때문에 노드 유형별 네트워크 보안 그룹 규칙 구성 
* 보유하고 있을 수 있는 기존 정책 및 컨트롤 유지

> [!NOTE]
> BYOVNET을 사용하는 경우 관리형 클러스터 리소스는 구성된 추가 부하 분산에 관계없이 하나의 NSG가 있는 하나의 서브넷에 배포됩니다.

> [!NOTE]
> 노드 유형에 대한 클러스터 배포 후 기본값에서 사용자 지정으로 전환할 수는 없지만 배포 후 사용자 지정 부하 분산 장치 구성을 수정할 수 있습니다.

**기능 요구 사항**
 * 기본 및 표준 SKU Azure Load Balancer 형식이 지원됩니다.
 * 기존 Azure Load Balancer에 구성된 백 엔드 및 NAT 풀이 있어야 합니다. 예제는 전체 [여기에서 역할 만들기 및 할당하기 샘플](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/master/SF-Managed-Standard-SKU-2-NT-BYOLB/createlb-and-assign-role.json)을 참조하세요. 

다음은 고객이 사용할 수 있는 몇 가지 예제 시나리오입니다.

이 예제에서 고객은 기존 고정 IP 주소로 구성된 기존 Azure Load Balancer 통해 트래픽을 두 노드 형식으로 라우팅하려고 합니다.
![사용자 고유의 Load Balancer 예제 1][sfmc-byolb-example-1]

이 예제에서 고객은 기존 Azure Load Balancer를 통해 트래픽을 라우팅하여 별도의 노드 유형에 있는 애플리케이션에 대한 트래픽 흐름을 독립적으로 관리하려고 합니다. 이 예제처럼 설정하면 각 노드 유형은 관리할 수 있는 고유한 NSG 뒤에 있습니다.
![사용자 고유의 Load Balancer 예제 2][sfmc-byolb-example-2]

사용자 고유의 부하 분산 장치 가져오기를 구성하려면 다음을 수행합니다.

1. Service Fabric 리소스 공급자 애플리케이션에 대한 구독에서 서비스 `Id`를 받습니다.

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
   ```

   > [!NOTE]
   > 올바른 구독에 있는지 확인합니다. 구독이 다른 테넌트에 있는 경우 보안 주체 ID가 변경됩니다.

   ```powershell
   ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
   ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
   ObjectType            : ServicePrincipal
   DisplayName           : Azure Service Fabric Resource Provider
   Id                    : 00000000-0000-0000-0000-000000000000
   ```

   이후 단계에서 사용할 수 있도록 이전 출력의 **ID** 를 **principalId** 로 기록합니다.

   |역할 정의 이름|역할 정의 ID|
   |----|-------------------------------------|
   |네트워크 참가자|4d97b98b-1d4f-4787-a291-c67834d212e7|

   이후 단계에서 사용할 수 있도록 `Role definition name` 및 `Role definition ID` 속성 값을 기록합니다.

2. Service Fabric 리소스 공급자 애플리케이션으로 역할 할당을 추가합니다. 역할 할당을 추가하는 것은 일회성 작업입니다. 다음 PowerShell 명령을 실행하거나 아래에 설명된 대로 ARM(Azure Resource Manager) 템플릿을 구성하여 역할을 추가합니다.

   다음 단계에서는 Existing-RG 리소스 그룹에서 Existing-LoadBalancer1이라는 기존 부하 분산 장치로 시작합니다.

   기존 Azure Load Balancer에서 필요한 `Id` 속성 정보를 얻습니다. 우리는 

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzLoadBalancer -Name "Existing-LoadBalancer1" -ResourceGroupName "Existing-RG"
   ```
   다음 단계에서 사용할 다음 `Id`을 기록합니다.
   ```JSON
   {
   ...
   "Id": "/subscriptions/<subscriptionId>/resourceGroups/Existing-RG/providers/Microsoft.Network/loadBalancers/Existing-LoadBalancer1"
   }
   ```
   보안 주체 ID, 2단계의 역할 정의 이름 및 방금 얻은 할당 범위 `Id`를 사용하여 다음 PowerShell 명령을 실행합니다.

   ```powershell
   New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Network Contributor" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/loadBalancers/<LoadBalancerName>"
   ```

   또는 1단계, 및 에서 가져온 에 대한 적절한 값으로 구성된 ARM(Azure Resource Manager) 템플릿을 사용하여 역할 할당을 추가할 수 `principalId` `loadBalancerRoleAssignmentID` 있습니다. `roleDefinitionId`

   ```JSON
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2020-04-01-preview",
      "name": "[parameters('loadBalancerRoleAssignmentID')]",
      "scope": "[concat('Microsoft.Network/loadBalancers/', variables('lbName'))]",
      "dependsOn": [
        "[concat('Microsoft.Network/loadBalancers/', variables('lbName'))]"
      ],
      "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7')]",
        "principalId": "00000000-0000-0000-0000-000000000000"
      }
   ```
   > [!NOTE]
   > loadBalancerRoleAssignmentID는 [GUID](../azure-resource-manager/templates/template-functions-string.md#examples-16)여야 합니다. 템플릿에 이 역할 할당을 포함하여 재배포하는 경우 GUID가 원래 사용된 것과 동일한지 확인합니다. 이 리소스는 한 번만 만들면 되므로, 배포 후 클러스터 템플릿에서 격리하거나 제거하는 것이 좋습니다.

3. 필요한 아웃바운드 연결 구성 모든 노드는 포트 443의 아웃바운드를 ServiceFabric 리소스 공급자로 라우팅할 수 있어야 합니다. NSG에서 `ServiceFabric` 서비스 태그를 사용하여 트래픽 대상을 Azure 엔드포인트로 제한할 수 있습니다.

4. 필요에 따라 기존 Azure Load Balancer에서 인바운드 애플리케이션 포트 및 관련 프로브를 구성합니다.

5. 필요에 따라 노드 유형에 적용된 관리형 클러스터 NSG 규칙을 구성하여 Azure Load Balancer 구성한 모든 필수 트래픽 또는 트래픽이 차단되도록 합니다.

   인바운드 규칙을 여는 방법에 대한 예제는 [사용자 고유의 부하 분산 장치 샘플 ARM(Azure Resource Manager) 템플릿 가져오기](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-BYOLB)를 참조하세요.

6. 구성된 관리형 클러스터 ARM 템플릿 배포

   다음 예제에서는 `westus`에서 `MyResourceGroup`라는 리소스 그룹을 만들고 이 기능을 사용하도록 설정된 클러스터를 배포합니다.
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```

   배포 후 보조 노드 유형은 인바운드 및 아웃바운드 트래픽에 대해 지정된 부하 분산 장치를 사용하도록 구성됩니다. Service Fabric 클라이언트 연결 및 게이트웨이 엔드포인트는 관리형 클러스터 주 노드 유형 고정 IP 주소의 공용 DNS를 가리킵니다.


## <a name="next-steps"></a>다음 단계
[Service Fabric 관리형 클러스터 구성 옵션](how-to-managed-cluster-configuration.md)
[Service Fabric 관리형 클러스터 개요](overview-managed-cluster.md)

<!--Image references-->
[Inbound-NAT-Rules]: ./media/how-to-managed-cluster-networking/inbound-nat-rules.png
[sfmc-rdp-connect]: ./media/how-to-managed-cluster-networking/sfmc-rdp-connect.png
[sfmc-byolb-example-1]: ./media/how-to-managed-cluster-networking/sfmc-byolb-scenario-1.png
[sfmc-byolb-example-2]: ./media/how-to-managed-cluster-networking/sfmc-byolb-scenario-2.png

