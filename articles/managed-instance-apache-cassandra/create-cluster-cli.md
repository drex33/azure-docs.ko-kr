---
title: 빠른 시작 - CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 클러스터 만들기
description: Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 클러스터를 만들려면 이 빠른 시작을 사용하세요.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 09/08/2021
ms.openlocfilehash: b2d00a30a1b53ecb6c854e84c3202e10a92abcea
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736319"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>빠른 시작: Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 클러스터 만들기(미리 보기)

Apache Cassandra용 Azure Managed Instance는 관리형 오픈 소스 Apache Cassandra 데이터 센터의 자동화된 배포 및 크기 조정 작업을 제공합니다. 이 서비스를 사용하면 하이브리드 시나리오를 가속화하고 지속적인 유지 관리를 줄일 수 있습니다.

> [!IMPORTANT]
> Apache Cassandra용 Azure Managed Instance는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 빠른 시작에서는 Azure CLI 명령을 사용하여 Apache Cassandra용 Azure Managed Instance를 지원하는 클러스터를 만드는 방법을 설명합니다. 데이터 센터를 만들고 데이터 센터 내에서 노드를 스케일 업 또는 다운하는 방법도 보여 줍니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* 자체 호스팅 또는 온-프레미스 환경에 연결된 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). 온-프레미스 환경을 Azure에 연결하는 방법에 대한 자세한 내용은 [Azure에 온-프레미스 네트워크 연결](/azure/architecture/reference-architectures/hybrid-networking/) 문서를 참조하세요.

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!IMPORTANT]
> 이 문서를 진행하려면 Azure CLI 버전 2.17.1 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>관리형 인스턴스 클러스터 만들기

1. [Azure 포털](https://portal.azure.com/)

1. Azure CLI에 구독 ID를 설정합니다.

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. 다음으로, 리소스 그룹에 전용 서브넷을 사용하는 Virtual Network를 만듭니다.

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

   > [!NOTE]
   > Azure Managed Instance for Apache Cassandra를 배포하려면 인터넷 액세스가 필요합니다. 인터넷 액세스가 제한되는 환경에서는 배포가 실패합니다. Managed Cassandra가 올바르게 작동하는 데 필요한 다음과 같은 중요한 Azure 서비스에 대한 VNet 내에서 액세스가 차단되어 있는지 확인합니다.
   > - Azure Storage
   > - Azure KeyVault
   > - Azure Virtual Machine Scale Sets
   > - Azure 모니터링
   > - Azure Active Directory
   > - Azure Security

1. 관리형 인스턴스에 필요한 몇 가지 특수 권한을 Virtual Network에 적용합니다. `az role assignment create` 명령을 사용하여 `<subscriptionID>`, `<resourceGroupName>` 및 `<vnetName>`을 적절한 값으로 바꿉니다.

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   ```

   > [!NOTE]
   > 이전 명령의 `assignee` 및 `role` 값은 고정된 값이므로 명령에 설명된 대로 이러한 값을 정확하게 입력합니다. 그렇게 하지 않으면 클러스터를 만들 때 오류가 발생합니다. 이 명령을 실행할 때 오류가 발생하면 실행할 권한이 없는 것일 수 있습니다. 관리자에게 문의하여 권한을 받으세요.

1. 그런 다음, [az managed-cassandra cluster create](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create) 명령을 사용하여 새로 만든 Virtual Network에서 클러스터를 만듭니다. `delegatedManagementSubnetId` 변수 값으로 다음 명령을 실행합니다.

   > [!NOTE]
   > 아래에 제공할 `delegatedManagementSubnetId` 변수 값은 위의 명령에서 제공한 `--scope` 값과 정확히 동일합니다.

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
     --cluster-name $clusterName \
     --resource-group $resourceGroupName \
     --location $location \
     --delegated-management-subnet-id $delegatedManagementSubnetId \
     --initial-cassandra-admin-password $initialCassandraAdminPassword \
     --debug
   ```

1. 마지막으로 [az managed-cassandra datacenter create](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create) 명령을 사용하여 세 개의 노드가 있는 클러스터용 데이터 센터를 만듭니다.

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --data-center-location $dataCenterLocation \
     --delegated-subnet-id $delegatedManagementSubnetId \
     --node-count 3 
   ```

1. 데이터 센터가 생성된 후 데이터 센터의 노드를 스케일 업 또는 스케일 다운하려는 경우 [az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update) 명령을 실행합니다. `node-count` 매개 변수 값을 원하는 값으로 변경합니다.

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>클러스터에 연결

Apache Cassandra용 Azure Managed Instance는 공용 IP 주소를 사용하여 노드를 만들지 않습니다. 새로 만든 Cassandra 클러스터에 연결하려면 가상 네트워크 내에 다른 리소스를 만들어야 합니다. 이 리소스는 Apache의 오픈 소스 쿼리 도구인 [CQLSH](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html)가 설치된 가상 머신 또는 애플리케이션이 될 수 있습니다. [Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/vm-simple-linux/)을 사용하여 Ubuntu 가상 머신을 배포할 수 있습니다. 배포 후 다음 명령에 표시된 것처럼 SSH를 사용하여 머신에 연결하고 CQLSH를 설치합니다.

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>문제 해결

Virtual Network에 권한을 적용할 때 *'e5007d2c-4b13-4a74-9b6a-605d99f03501'에 대한 그래프 데이터베이스에서 사용자 또는 서비스 주체를 찾을 수 없음* 과 같은 오류가 발생하는 경우 Azure Portal에서 동일한 권한을 수동으로 적용할 수 있습니다. 포털에서 권한을 적용하려면 기존 가상 네트워크의 **액세스 제어(IAM)** 창으로 이동하여 "Azure Cosmos DB"에 대한 역할 할당을 "네트워크 관리자" 역할에 추가합니다. "Azure Cosmos DB"를 검색할 때 두 개의 항목이 나타나면 다음 이미지에 표시된 대로 항목을 추가합니다. 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="권한 적용" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Azure Cosmos DB 역할 할당은 배포 목적으로만 사용됩니다. Azure Managed Instanced for Apache Cassandra에는 Azure Cosmos DB에 대한 백 엔드 종속성이 없습니다.  

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 `az group delete` 명령을 사용하여 리소스 그룹, 관리형 인스턴스 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 CLI를 사용하여 Apache Cassandra용 Azure Managed Instance를 만드는 방법을 알아보았습니다. 이제 클러스터 사용을 시작할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Databricks를 사용하여 관리형 Apache Spark 클러스터 배포](deploy-cluster-databricks.md)
