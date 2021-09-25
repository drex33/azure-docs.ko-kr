---
title: Azure용 Linux OS 업그레이드 Service Fabric
description: Azure Service Fabric 클러스터를 다른 Linux OS로 마이그레이션하기 위한 옵션에 대해 알아봅니다.
manager: tassb
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 4a5b1a97fde977fa4ba64a4a23d0c57725595c37
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634334"
---
# <a name="upgrading-linux-os-for-azure-service-fabric"></a>Azure용 Linux OS 업그레이드 Service Fabric

이 문서에서는 Linux용 Azure Service Fabric 클러스터를 Ubuntu 버전 16.04 LTS에서 18.04 LTS로 마이그레이션하는 가이드를 설명합니다. 각 OS(운영 체제) 버전에는 원활한 마이그레이션을 위해 이 문서에 설명된 단계가 필요한 고유한 SF 런타임 패키지가 필요합니다.

## <a name="overview"></a>개요

일반적인 방법은 다음과 같습니다.

1. 이 OS 버전에 대한 향후 코드 업그레이드를 끌어오려면 Service Fabric 클러스터 ARM(Azure Resource Manager) 리소스 "vmImage"를 "Ubuntu18_04"로 전환합니다. 기존 노드 형식에 대한 이러한 임시 OS 불일치는 자동 코드 업그레이드 롤아웃을 차단하여 안전한 롤오버를 보장합니다.

    * OS 마이그레이션 중에 수동 SF 클러스터 코드 업그레이드를 발급하지 않습니다. 이렇게 하면 이전 노드 형식 노드가 사람의 개입이 필요한 상태가 될 수 있습니다.

2. 클러스터의 각 노드 유형에 대해 기본 Virtual Machine Scale Set에 대한 Ubuntu 18.04 OS 이미지를 대상으로 하는 다른 노드 유형을 만듭니다. 각각의 새 노드 유형은 이전 노드 형식의 역할을 가정합니다.

    * "isPrimary": true로 표시된 이전 노드 형식을 바꾸려면 새 주 노드 형식을 만들어야 합니다.
    
    * 주 노드가 아닌 각 추가 노드 유형에 대해 이러한 노드 형식은 마찬가지로 "isPrimary": false로 표시됩니다.

    * 새 대상 OS 노드 유형을 만든 후 기존 워크로드가 계속 올바르게 작동하는지 확인합니다. 문제가 관찰되면 이전 노드 유형을 제거하기 전에 앱 또는 미리 설치된 컴퓨터 패키지에 필요한 변경 내용을 해결합니다.
3. 이전 주 노드 유형을 "isPrimary": false로 표시합니다. 이로 인해 모든 시드 노드를 전환하기 위한 장기 실행 업그레이드 집합이 생성됩니다.
4. (Bronze 내구성 노드 유형에만 해당): [sfctl](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl)  /  [PowerShell](https://docs.microsoft.com/powershell/module/ServiceFabric/?view=azureservicefabricps)  /  [FabricClient를](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient?view=azure-dotnet) 통해 클러스터에 커넥트 이전 노드 형식의 모든 노드를 사용하지 않도록 설정합니다.
5. 이전 노드 유형을 제거합니다.

> [!NOTE]
> Az PowerShell은 추가된 노드 형식에 대한 새 dns 이름을 생성하므로 외부 트래픽을 이 엔드포인트로 리디렉션해야 합니다.


## <a name="ease-of-use-steps-for-non-production-clusters"></a>비프로덕션 클러스터의 사용 편의성 단계

> [!NOTE]
> 아래 단계에서는 테스트 전용 클러스터에서 Az PowerShell cmdlet을 통해 노드 형식 마이그레이션의 프로토타입을 신속하게 제작하는 방법을 보여 줍니다. 실제 비즈니스 트래픽이 있는 프로덕션 클러스터의 경우 일관된 선언적 정보 소스에 & 재생 가능성을 유지하기 위해 ARM 업그레이드를 발급하여 동일한 단계를 수행해야 합니다.

1. [Update-AzServiceFabricVmImage를](https://docs.microsoft.com/powershell/module/az.servicefabric/update-azservicefabricvmimage)사용하여 Service Fabric 클러스터 리소스에서 vmImage 설정을 업데이트합니다.

    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps):
    ```powershell
    # Replace subscriptionId, resourceGroup, clusterName with ones corresponding to your cluster.
    $subscriptionId="cea219db-0593-4b27-8bfa-a703332bf433"
    Login-AzAccount; Select-AzSubscription -SubscriptionId $subscriptionId

    $resourceGroup="Group1"
    $clusterName="Contoso01SFCluster"
    # Update cluster vmImage to target OS. This registers the SF runtime package type that is supplied for upgrades.
    Update-AzServiceFabricVmImage -ResourceGroupName $resourceGroup -ClusterName $clusterName -VmImage Ubuntu18_04
    ```

2. 기존 노드 유형 각각에 대응하는 새 노드 유형을 추가합니다.

    ```powershell
    $nodeTypeName="nt1u18"
    # You can customize this to fetch a password from a secure store.
    $securePassword = ConvertTo-SecureString -String 'Yourpassword123!@#' -AsPlainText -Force

    # Ensure last upgrade is done - Ready means the next command can be issued.
    (Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup).ClusterState

    # Add new primary node type. Omit the IsPrimaryNodeType parameter for non-primary node types.
    Add-AzServiceFabricNodeType -ResourceGroupName $resourceGroup  -ClusterName $clusterName -NodeType $nodeTypeName -Capacity 5 -VmUserName testuser -VmPassword $securePassword -DurabilityLevel Silver -Verbose -VMImageSku 18.04-LTS -IsPrimaryNodeType $true

    # Redirect traffic to new node type dns
    # dns-Contoso01SFCluster-nt1u18.westus2.cloudapp.azure.com
    ```

3. 시드 노드 및 시스템 서비스를 새 노드 형식으로 롤오버하기 위해 이전 주 노드 형식을 주 노드가 아닌 노드 형식으로 업데이트합니다.

    ```powershell
    # Query to ensure background upgrades are done.
    (Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup).ClusterState

    # Update old nodetype to isPrimary: false
    $oldNodeTypeName="nt1"
    Update-AzServiceFabricNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -IsPrimaryNodeType $false -NodeType $oldNodeTypeName -Verbose

    # Ensure node type is showing isPrimary: False before proceeding.
    Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup
    ```

    예제 출력:
    ```
    NodeTypes :
              NodeTypeDescription :
                  Name : nt1
                  PlacementProperties :
                  Capacities :
                  ClientConnectionEndpointPort : 19000
                  HttpGatewayEndpointPort : 19080
                  DurabilityLevel : Bronze
                  ApplicationPorts :
                      StartPort : 20000
                      EndPort : 30000
                  EphemeralPorts :
                      StartPort : 49152
                      EndPort : 65534
                  IsPrimary : False
                  VmInstanceCount : 5
                  ReverseProxyEndpointPort :
              NodeTypeDescription :
                  Name : nt1u18
                  PlacementProperties :
                  Capacities :
                  ClientConnectionEndpointPort : 19000
                  HttpGatewayEndpointPort : 19080
                  DurabilityLevel : Silver
                  ApplicationPorts :
                      StartPort : 20000
                      EndPort : 30000
                  EphemeralPorts :
                      StartPort : 49152
                      EndPort : 65534
                  IsPrimary : True
                  VmInstanceCount : 5
                  ReverseProxyEndpointPort :
    ```

4. Bronze 내구성 노드 형식을 제거하려면 먼저 이전 노드 형식을 제거하기 전에 노드를 사용하지 않도록 설정합니다. *ssh를* 통해 클러스터 노드로 커넥트 다음 명령을 실행합니다.

    ```bash
    # as root user:

    # install jq tool to automatically parse JSON responses
    apt-get install jq -fy

    # retrieve the thumbprint to be used for establishing a fabric client
    dataroot=$(cat /etc/servicefabric/FabricDataRoot)
    nodename=_nt1_0
    cat $dataroot/$nodename/Fabric/ClusterManifest.current.xml | grep ClientCertThumbprints
    # 0777FE1A43E306F332D96DA339EF6834D0E4A453

    # verify node count
    sfctl cluster select --endpoint https://Contoso01SFCluster.westus2.cloudapp.azure.com:19080 --pem /var/lib/waagent/0777FE1A43E306F332D96DA339EF6834D0E4A453.pem --no-verify

    # sample command to list all nodes
    sfctl node list

    # for each node part of the node type to be removed, disable the node:
    nodeTypeBeingDisabled=nt1
    nodes=$(sfctl node list | jq --arg nodeTypeBeingDisabled "$nodeTypeBeingDisabled" '.items[] | select(.type==$nodeTypeBeingDisabled) | .name' | sed s/\"//g)
    echo $nodes
    for n in $nodes; do echo "Disabling $n"; sfctl node disable --node-name $n --deactivation-intent RemoveNode --timeout 300; done
    ```

5. SF 클러스터 리소스 노드 유형 특성을 제거하고 연결된 가상 머신 확장 집합 & 네트워킹 리소스를 해제하여 이전 노드 유형을 제거합니다.

    ```powershell
    $resourceGroup="Group1"
    $clusterName="Contoso01SFCluster"
    $oldNodeTypeName="nt1"

    # Remove the Service Fabric node type, associated virtual machine scale set resource, and any trailing networking resources that are no longer used. 
    Remove-AzServiceFabricNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -NodeType $oldNodeTypeName
    ```

    > [!NOTE]
    > 경우에 따라 아래 오류가 발생할 수 있습니다. 이러한 경우 SFX(Service Fabric Explorer)를 통해 제거된 노드 형식에 대한 InfrastructureService가 오류 상태에 있음을 확인할 수 있습니다. 이 문제를 해결하려면 제거를 다시 시도합니다.
    ```
    Remove-AzServiceFabricNodeType : Code: ClusterUpgradeFailed, Message: Long running operation failed with status 'Failed'
    ```

워크로드가 새 노드 형식으로 성공적으로 마이그레이션되고 이전 노드 형식이 제거된 것으로 확인되면 클러스터는 구성 업그레이드를 & 후속 Service Fabric 런타임 코드 버전을 계속 진행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Service Fabric 업그레이드 관리](service-fabric-cluster-upgrade-version-azure.md)
* [Service Fabric 클러스터 설정](service-fabric-cluster-fabric-settings.md) 사용자 지정
* 클러스터 [내구성 특징](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)에 대해 자세히 알아봅니다.
* [노드 유형과 Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md)에 대해 자세히 알아봅니다.
* [Service Fabric 클러스터 크기 조정](service-fabric-cluster-scaling.md)에 대해 자세히 알아봅니다.
* [클러스터 스케일 인 및 스케일 아웃](service-fabric-cluster-scale-in-out.md)
* [Azure Service Fabric 노드 형식 제거](service-fabric-how-to-remove-node-type.md)

