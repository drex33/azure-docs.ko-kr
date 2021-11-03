---
title: 빠른 시작 - Apache Cassandra용 Azure Managed Instance를 사용하여 다중 지역 클러스터 구성
description: 이 빠른 시작에서는 Apache Cassandra용 Azure Managed Instance를 사용하여 다중 지역 클러스터를 구성하는 방법을 보여 줍니다.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 236b2d1b34c04c387e8cc5dd83e886bcea0a8b52
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084978"
---
# <a name="quickstart-create-a-multi-region-cluster-with-azure-managed-instance-for-apache-cassandra"></a>빠른 시작: Azure Managed Instance for Apache Cassandra를 사용하여 다중 지역 클러스터 만들기

Apache Cassandra용 Azure Managed Instance는 관리형 오픈 소스 Apache Cassandra 데이터 센터의 자동화된 배포 및 크기 조정 작업을 제공합니다. 이 서비스를 사용하면 하이브리드 시나리오를 가속화하고 지속적인 유지 관리를 줄일 수 있습니다.

이 빠른 시작에서는 Azure CLI 명령을 사용하여 Azure에서 다중 지역 클러스터를 구성하는 방법을 보여 줍니다.  

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* 이 문서를 진행하려면 Azure CLI 버전 2.12.1 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

* 자체 호스팅 또는 온-프레미스 환경에 연결된 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). 온-프레미스 환경을 Azure에 연결하는 방법에 대한 자세한 내용은 [Azure에 온-프레미스 네트워크 연결](/azure/architecture/reference-architectures/hybrid-networking/) 문서를 참조하세요.

## <a name="setting-up-the-network-environment"></a><a id="create-account"></a>네트워크 환경 설정

이 서비스를 사용하여 프로비저닝된 모든 데이터 센터는 VNet 삽입을 사용하여 전용 서브넷에 배포되어야 하며, 다중 지역 클러스터가 제대로 작동하도록 배포 전에 적절한 네트워크 피어링을 구성해야 합니다. 별도의 지역(미국 동부 및 미국 동부 2)에 두 개의 데이터 센터가 있는 클러스터를 만들겠습니다. 먼저, 각 지역에 대한 가상 네트워크를 만들어야 합니다. 

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. “cassandra-mi-multi-region”이라는 리소스 그룹을 만듭니다.

   ```azurecli-interactive
   az group create -l eastus2 -n cassandra-mi-multi-region
   ```

1. 전용 서브넷을 사용하여 미국 동부 2에 첫 번째 VNET을 만듭니다.

   ```azurecli-interactive
   az network vnet create \
     -n vnetEastUs2 \
     -l eastus2 \
     -g cassandra-mi-multi-region \
     --address-prefix 10.0.0.0/16 \
     --subnet-name dedicated-subnet
   ```

1. 이제 전용 서브넷을 사용하여 미국 동부에 두 번째 VNET을 만듭니다.

   ```azurecli-interactive
    az network vnet create \
      -n vnetEastUs \
      -l eastus \
      -g cassandra-mi-multi-region \
      --address-prefix 192.168.0.0/16 \
      --subnet-name dedicated-subnet
   ```

   > [!NOTE]
   > 피어링 시 오류가 발생하지 않도록 다른 IP 주소 범위를 명시적으로 추가합니다. 

1. 이제 첫 번째 VNet을 두 번째 VNet에 피어링해야 합니다.

   ```azurecli-interactive
   az network vnet peering create \
     -g cassandra-mi-multi-region \
     -n MyVnet1ToMyVnet2 \
     --vnet-name vnetEastUs2 \
     --remote-vnet vnetEastUs \
     --allow-vnet-access \
     --allow-forwarded-traffic
   ```

1. 두 VNet을 연결하려면 두 번째 VNet과 첫 번째 VNet 간에 다른 피어링을 만듭니다.

   ```azurecli-interactive
   az network vnet peering create \
     -g cassandra-mi-multi-region \
     -n MyVnet2ToMyVnet1 \
     --vnet-name vnetEastUs \
     --remote-vnet vnetEastUs2 \
     --allow-vnet-access \
     --allow-forwarded-traffic  
   ```

   > [!NOTE]
   > 지역을 더 추가하는 경우 각 VNet에서 다른 모든 VNet으로 피어링하고 다른 모든 VNet에서 각 VNet으로 피어링해야 합니다. 

1. 이전 명령의 출력을 확인하고 이제 "peeringState" 값이 "연결됨"인지 확인합니다. 다음 명령을 실행하여 이 값을 확인할 수도 있습니다.

   ```azurecli-interactive
   az network vnet peering show \
     --name MyVnet1ToMyVnet2 \
     --resource-group cassandra-mi-multi-region \
     --vnet-name vnetEastUs2 \
     --query peeringState
   ``` 

1. 다음으로, Azure Managed Instance for Apache Cassandra에 필요한 두 가상 네트워크에 몇 가지 특별한 권한을 적용합니다. 다음을 실행하고 `<SubscriptionID>`를 구독 ID로 바꿉니다.

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2

   az role assignment create     \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs
    ```
   > [!NOTE]
   > 이전 명령의 `assignee` 및 `role` 값은 고정된 값이므로 명령에 설명된 대로 이러한 값을 정확하게 입력합니다. 그렇게 하지 않으면 클러스터를 만들 때 오류가 발생합니다. 이 명령을 실행할 때 오류가 발생하면 실행할 권한이 없는 것일 수 있습니다. 관리자에게 문의하여 권한을 받으세요.

## <a name="create-a-multi-region-cluster"></a><a id="create-account"></a>다중 지역 클러스터 만들기

1. 이제 적절한 네트워킹을 준비했으므로 클러스터 리소스를 배포할 준비가 되었습니다(`<Subscription ID>`를 구독 ID로 대체). 5-10분 정도 소요될 수 있습니다.

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2/subnets/dedicated-subnet'
   initialCassandraAdminPassword='myPassword'
        
    az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. 클러스터 리소스가 만들어지면 데이터 센터를 만들 준비가 된 것입니다. 먼저 미국 동부 2에 데이터 센터를 만듭니다(`<SubscriptionID>`를 구독 ID로 대체). 이 작업은 최대 10분까지 걸릴 수 있습니다.

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   dataCenterName='dc-eastus2'
   dataCenterLocation='eastus2'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2/subnets/dedicated-subnet'
        
    az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedManagementSubnetId \
       --node-count 3
   ```

1. 그런 다음 미국 동부에 데이터 센터를 만듭니다(`<SubscriptionID>`를 구독 ID로 대체).

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   dataCenterName='dc-eastus'
   dataCenterLocation='eastus'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs/subnets/dedicated-subnet'
        
    az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. 두 번째 데이터 센터가 만들어지면 노드 상태를 확인하여 모든 Cassandra 노드가 성공적으로 표시되는지 확인합니다.

    ```azurecli-interactive
    resourceGroupName='cassandra-mi-multi-region'
    clusterName='test-multi-region'
    
    az managed-cassandra cluster node-status \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName
    ```

1. 마지막으로, CQLSH를 사용하여 [클러스터에 연결](create-cluster-cli.md#connect-to-your-cluster)하고 다음 CQL 쿼리를 사용하여 클러스터의 모든 데이터 센터를 포함하도록 각 키스페이스에서 복제 전략을 업데이트합니다.

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'dc-eastus2': 3, 'dc-eastus': 3};
   ```
   암호 테이블도 업데이트해야 합니다.

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'dc-eastus2': 3, 'dc-eastus': 3}
   ```

## <a name="troubleshooting"></a>문제 해결

Virtual Network에 권한을 적용할 때 *'e5007d2c-4b13-4a74-9b6a-605d99f03501'에 대한 그래프 데이터베이스에서 사용자 또는 서비스 주체를 찾을 수 없음* 과 같은 오류가 발생하는 경우 Azure Portal에서 동일한 권한을 수동으로 적용할 수 있습니다. 

Azure Portal에서 권한을 적용하려면 기존 가상 네트워크의 **액세스 제어(IAM)** 창으로 이동하여 "Azure Cosmos DB"에 대한 역할 할당을 "네트워크 관리자" 역할에 추가합니다. "Azure Cosmos DB"를 검색할 때 두 개의 항목이 나타나면 다음 이미지에 표시된 대로 항목을 추가합니다. 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="권한 적용" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Azure Cosmos DB 역할 할당은 배포 목적으로만 사용됩니다. Azure Managed Instanced for Apache Cassandra에는 Azure Cosmos DB에 대한 백 엔드 종속성이 없습니다.  

## <a name="clean-up-resources"></a>리소스 정리

이 관리형 인스턴스 클러스터를 더 이상 사용하지 않으려면 다음 단계에 따라 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
1. 목록에서 이 빠른 시작에서 만든 리소스 그룹을 선택합니다.
1. 리소스 그룹 **개요** 창에서 **리소스 그룹 삭제** 를 선택합니다.
1. 새 창에서 삭제할 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI 및 Apache Cassandra용 Azure Managed Instance를 사용하여 다중 지역 클러스터를 만드는 방법을 알아보았습니다. 이제 클러스터 사용을 시작할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 리소스 관리](manage-resources-cli.md)
