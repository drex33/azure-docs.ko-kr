---
title: Azure CLI 통해 리소스 관리 - Azure Resource Manager | Microsoft Docs
description: Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance의 관리를 자동화하는 일반적인 명령을 알아봅니다.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: thvankra
ms.custom: devx-track-azurecli, seo-azure-cli, ignite-fall-2021
keywords: azure resource manager cli
ms.openlocfilehash: 917cf53cbd90427cc03ecbef59c8dd35a273a3b5
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132941456"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli"></a>Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 리소스 관리

이 문서에서는 Azure CLI를 사용하여 Apache Cassandra 클러스터용 Azure Managed Instance의 관리를 자동화하는 일반적인 명령을 설명합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> 이 문서를 진행하려면 Azure CLI 버전 2.30.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.
>
> Azure Resource Manager가 리소스 URI와 함께 작동하는 방식을 위반하므로 Apache Cassandra 리소스에 대한 Azure Managed Instance 관리의 이름을 바꿀 수 없습니다.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Apache Cassandra 클러스터용 Azure Managed Instance

다음 섹션에서는 다음을 포함하여 Apache Cassandra 클러스터용 Azure Managed Instance를 관리하는 방법을 보여 줍니다.

* [관리되는 인스턴스 클러스터 만들기](#create-cluster)
* [관리되는 인스턴스 클러스터 삭제](#delete-cluster)
* [클러스터 세부 정보 가져오기](#get-cluster-details)
* [클러스터 노드 상태 가져오기](#get-cluster-status)
* [리소스 그룹별로 클러스터 나열](#list-clusters-resource-group)
* [구독 ID별 클러스터 나열](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>관리형 인스턴스 클러스터 만들기

[az managed-cassandra cluster create](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create) 명령을 사용하여 Apache Cassandra 클러스터용 Azure Managed Instance를 만듭니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>관리되는 인스턴스 클러스터 삭제

[Az managed-cassandra cluster delete](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_delete) 명령을 사용하여 클러스터를 삭제합니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>클러스터 세부 정보 가져오기

[az managed-cassandra cluster show](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_show) 명령을 사용하여 클러스터 세부 정보를 가져옵니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>클러스터 노드 상태 가져오기

[az managed-cassandra cluster node-status](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_node_status) 명령을 사용하여 클러스터 세부 정보를 가져옵니다.

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>리소스 그룹별 클러스터 나열

[az managed-cassandra cluster list](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_list) 명령을 사용하여 리소스 그룹별로 클러스터를 나열합니다.

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>구독 ID별 클러스터 나열

[az managed-cassandra cluster list](/cli/azure/managed-cassandra?view=azure-cli-latest&preserve-view=true) 명령을 사용하여 구독 ID별로 클러스터를 나열합니다

```azurecli-interactive
# set your subscription id
az account set -s <subscriptionID>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>관리되는 인스턴스 데이터 센터

다음 섹션에서는 다음을 포함하여 Apache Cassandra 데이터 센터용 Azure Managed Instance를 관리하는 방법을 보여 줍니다.

* [데이터 센터 만들기](#create-datacenter)
* [데이터 센터 삭제](#delete-datacenter)
* [데이터 센터 세부 정보 가져오기](#get-datacenter-details)
* [클러스터의 데이터 센터 가져오기](#get-datacenters-cluster)
* [데이터 센터 업데이트 또는 크기 조정](#update-datacenter)
* [Cassandra 구성 업데이트](#update-yaml)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>데이터 센터 만들기

[az managed-cassandra datacenter create](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create) 명령을 사용하여 데이터 센터를 만듭니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'
virtualMachineSKU='Standard_D8s_v4'
noOfDisksPerNode=4

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
    --sku $virtualMachineSKU \
    --disk-capacity $noOfDisksPerNode \
    --availability-zone false
```

> [!NOTE]
> `--sku`의 값은 다음과 같은 사용 가능한 SKU에서 선택할 수 있습니다.
>
> - Standard_E8s_v4
> - Standard_E16s_v4 
> - Standard_E20s_v4
> - Standard_E32s_v4 
> - Standard_DS13_v2
> - Standard_DS14_v2
> - Standard_D8s_v4
> - Standard_D16s_v4
> - Standard_D32s_v4 
> 
> 또한 `--availability-zone`은 `false`로 설정됩니다. 가용성 영역을 사용하도록 설정하려면 이를 `true`로 설정합니다. 가용성 영역은 서비스의 가용성 SLA를 증가합니다. 자세한 내용은 여기에서 전체 SLA 세부 정보를 [검토하세요.](https://azure.microsoft.com/support/legal/sla/managed-instance-apache-cassandra/v1_0/)

> [!WARNING]
> 모든 하위 지역에서 가용성 영역이 지원되지 않습니다. 가용성 영역이 지원되지 않는 하위 지역을 선택하면 배포에 실패합니다. [여기](../availability-zones/az-overview.md#azure-regions-with-availability-zones)에서 지원되는 지역을 참조하세요. 또한 가용성 영역을 성공적으로 배포하는 경우 지정된 하위 지역의 모든 영역에서 컴퓨팅 리소스를 사용할 수 있습니다. 선택한 SKU 또는 용량을 모든 영역에서 사용할 수 없는 경우 배포가 실패할 수 있습니다. 

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>데이터 센터 삭제

[Az managed-cassandra datacenter delete](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_delete) 명령을 사용하여 데이터 센터를 삭제합니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

> [!WARNING]
> 클러스터에 둘 이상의 데이터 센터가 있는 경우 먼저 [모든 키스스페이스 복제 전략 설정에서](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/operations/opsChangeKSStrategy.html) 삭제하려는 데이터 센터에 대한 참조를 제거해야 합니다. 클러스터 내의 모든 키스스페이스에 데이터 센터에 대한 참조가 여전히 있는 경우 이 명령은 실패합니다. 

### <a name="get-datacenter-details"></a><a id="get-datacenter-details">데이터 센터 세부 정보를 가져옵니다.</a>

[Az managed-cassandra datacenter show](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_show) 명령을 사용하여 데이터 센터 세부 정보를 가져옵니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>데이터 센터 업데이트 또는 크기 조정

[Az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update) 명령을 사용하여 데이터 센터를 업데이트하거나 크기를 조정하여 변경 nodeCount 값을 조정합니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="update-cassandra-configuration"></a><a id="update-yaml"></a>Cassandra 구성 업데이트

[az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update) 명령을 사용하여 데이터 센터에서 Cassandra 구성을 변경합니다. [온라인 도구](https://www.base64encode.org/)를 사용하여 YAML 조각을 base64로 인코딩해야 합니다. 다음 YAML 설정이 지원됩니다.

- column_index_size_in_kb
- allocate_tokens_for_keyspace
- compaction_throughput_mb_per_sec
- read_request_timeout_in_ms
- range_request_timeout_in_ms
- aggregated_request_timeout_in_ms
- write_request_timeout_in_ms
- request_timeout_in_ms
- internode_compression
- batchlog_replay_throttle_in_kb

예를 들어 다음과 같은 YAML 조각이 있습니다.

```yaml
column_index_size_in_kb: 16
read_request_timeout_in_ms: 10000
```

인코딩된 경우 YAML은 `Y29sdW1uX2luZGV4X3NpemVfaW5fa2I6IDE2CnJlYWRfcmVxdWVzdF90aW1lb3V0X2luX21zOiAxMDAwMA==`로 변환됩니다. 

아래 내용을 참조하세요.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
yamlFragment='Y29sdW1uX2luZGV4X3NpemVfaW5fa2I6IDE2CnJlYWRfcmVxdWVzdF90aW1lb3V0X2luX21zOiAxMDAwMA=='

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --base64-encoded-cassandra-yaml-fragment $yamlFragment
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>클러스터의 데이터 센터 가져오기

[Az managed-cassandra datacenter list](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_list) 명령을 사용하여 클러스터의 데이터 센터를 가져옵니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>다음 단계

* [Azure Portal에서 관리형 인스턴스 만들기](create-cluster-portal.md)
* [Azure Databricks를 사용하여 관리형 Apache Spark 클러스터 배포](deploy-cluster-databricks.md)
