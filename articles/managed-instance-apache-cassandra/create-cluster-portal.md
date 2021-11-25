---
title: 빠른 시작 - Azure Portal에서 Apache Cassandra 클러스터용 Azure Managed Instance 생성
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Apache Cassandra 클러스터용 Azure Managed Instance를 생성하는 방법을 설명합니다.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6bc0fb690c208adc2908087df39ed4d0a8252ad1
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132940337"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal"></a>빠른 시작: Azure Portal에서 Azure Managed Instance for Apache Cassandra 클러스터 만들기

Apache Cassandra용 Azure Managed Instance는 관리형 오픈 소스 Apache Cassandra 데이터 센터에 대한 자동화된 배포 및 스케일링 작업을 제공하여 하이브리드 시나리오를 가속화하고 지속적인 유지 관리를 줄여줍니다.

이 빠른 시작에서는 Azure Portal을 사용하여 Apache Cassandra 클러스터용 Azure Managed Instance를 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>관리형 인스턴스 클러스터 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 검색 창에서 **Apache Cassandra용 Managed Instance** 를 검색하고 결과를 선택합니다.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Apache Cassandra용 Managed Instance를 검색합니다." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. **Apache Cassandra 클러스터용 Managed Instance 만들기** 단추를 선택합니다.

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="클러스터를 만듭니다." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. **Apache Cassandra용 Managed Instance 만들기** 창에서 다음 세부 정보를 입력합니다.

   * **구독** - 드롭다운에서 Azure 구독을 선택합니다.
   * **리소스 그룹** - 새 리소스 그룹을 만들지 또는 기존 집합을 사용할지 여부를 지정합니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 자세한 내용은 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 개요 문서를 참조하세요.
   * **클러스터 이름** - 클러스터의 이름을 입력합니다.
   * **위치** - 클러스터가 배포되는 위치입니다.
   * **최초 Cassandra 관리자 암호** - 클러스터를 만드는 데 사용되는 암호입니다.
   * **Cassandra 관리자 암호 확인** - 암호를 다시 입력합니다.
   * **Virtual Network** - Virtual Network 종료 및 서브넷을 선택하거나 새 서브넷을 만듭니다. 
   * **역할 할당** - 관리되는 Cassandra 클러스터를 배포할 수 있도록 하려면 Virtual Networks에 특별한 권한이 필요합니다. 새 Virtual Network를 만들거나 권한이 적용되지 않은 기존 Virtual Network를 사용하는 경우 이 확인란을 선택된 상태로 유지합니다. Managed Instance Cassandra 클러스터를 이미 배포한 가상 네트워크를 사용하는 경우 이 옵션을 선택 취소합니다.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="클러스터 만들기 양식을 작성합니다." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

   > [!NOTE]
   > Azure Managed Instance for Apache Cassandra를 배포하려면 인터넷 액세스가 필요합니다. 인터넷 액세스가 제한되는 환경에서는 배포가 실패합니다. Managed Cassandra가 올바르게 작동하는 데 필요한 다음과 같은 중요한 Azure 서비스에 대한 VNet 내에서 액세스가 차단되어 있는지 확인합니다. 자세한 내용은 [필수 아웃바운드 네트워크 규칙](network-rules.md)을 참조하세요.
   > - Azure Storage
   > - Azure KeyVault
   > - Azure Virtual Machine Scale Sets
   > - Azure 모니터링
   > - Azure Active Directory
   > - Azure Security

1. 다음으로, **데이터 센터** 탭을 선택합니다.

1. 다음 세부 정보를 입력합니다.

   * **데이터 센터 이름** - 텍스트 필드에 데이터 센터 이름을 입력합니다.
   * **가용성 영역** - 가용성 영역을 사용하도록 설정하려면 이 확인란을 선택합니다.
   * **SKU 크기** - 사용 가능한 가상 머신 SKU 크기 중에서 선택합니다.
   * **디스크 수** - 각 Cassandra 노드에 연결할 p30 디스크 수를 선택합니다.
   * **노드 수** - 이 데이터 센터에 배포할 Cassandra 노드 수를 선택합니다.

   :::image type="content" source="./media/create-cluster-portal/create-datacenter-page.png" alt-text="요약을 검토하고 데이터 센터를 만듭니다." lightbox="./media/create-cluster-portal/create-datacenter-page.png" border="true":::

   > [!WARNING]
   > 모든 하위 지역에서 가용성 영역이 지원되지 않습니다. 가용성 영역이 지원되지 않는 하위 지역을 선택하면 배포에 실패합니다. [여기](../availability-zones/az-overview.md#azure-regions-with-availability-zones)에서 지원되는 지역을 참조하세요. 또한 가용성 영역을 성공적으로 배포하는 경우 지정된 하위 지역의 모든 영역에서 컴퓨팅 리소스를 사용할 수 있습니다. 선택한 SKU 또는 용량을 모든 영역에서 사용할 수 없는 경우 배포가 실패할 수 있습니다. 

1. **검토 + 만들기** > **만들기** 를 차례로 선택합니다

   > [!NOTE]
   > 클러스터를 만드는 데 최대 15분이 소요될 수 있습니다.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="요약을 검토하고 클러스터를 만듭니다." lightbox="./media/create-cluster-portal/review-create.png" border="true":::

1. 배포가 완료된 후 리소스 그룹을 확인하여 새로 만든 관리형 인스턴스 클러스터를 확인:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="클러스터를 만든 후의 개요 페이지." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. 클러스터 노드를 찾아보려면 클러스터 리소스로 이동하고 **데이터 센터** 창을 열어서 확인합니다.

   :::image type="content" source="./media/create-cluster-portal/datacenter-1.png" alt-text="데이터 센터 노드를 봅니다." lightbox="./media/create-cluster-portal/datacenter-1.png" border="true":::

<!-- ## <a id="create-account"></a>Add a datacenter

1. To add another datacenter, click the add button in the **Data Center** pane:

   :::image type="content" source="./media/create-cluster-portal/add-datacenter.png" alt-text="Click on add datacenter." lightbox="./media/create-cluster-portal/add-datacenter.png" border="true":::

   > [!WARNING]
   > If you are adding a datacenter in a different region, you will need to select a different virtual network. You will also need to ensure that this virtual network has connectivity to the primary region's virtual network created above (and any other virtual networks that are hosting datacenters within the managed instance cluster). Take a look at [this article](../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks) to learn how to peer virtual networks using Azure portal. You also need to make sure you have applied the appropriate role to your virtual network before attempting to deploy a managed instance cluster, using the below CLI command.
   >
   > ```azurecli-interactive  
   >     az role assignment create \
   >     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
   >     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
   >     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   > ```

1. Fill in the appropriate fields:

   * **Datacenter name** - From the drop-down, select your Azure subscription.
   * **Availability zone** - Check this box if you want availability zones to be enabled in this datacenter.
   * **Location** - Location where your datacenter will be deployed to.
   * **SKU Size** - Choose from the available Virtual Machine SKU sizes.
   * **No. of disks** - Choose the number of p30 disks to be attached to each Cassandra node.
   * **SKU Size** - Choose the number of Cassandra nodes that will be deployed to this datacenter.
   * **Virtual Network** - Select an Exiting Virtual Network and Subnet.  

   :::image type="content" source="./media/create-cluster-portal/add-datacenter-2.png" alt-text="Add Datacenter." lightbox="./media/create-cluster-portal/add-datacenter-2.png" border="true":::

   > [!WARNING]
   > Notice that we do not allow creation of a new virtual network when adding a datacenter. You need to choose an existing virtual network, and as mentioned above, you need to ensure there is connectivity between the target subnets where datacenters will be deployed. You also need to apply the appropriate role to the VNet to allow deployment (see above).

1. When the datacenter is deployed, you should be able to view all datacenter information in the **Data Center** pane:

   :::image type="content" source="./media/create-cluster-portal/multi-datacenter.png" alt-text="View the cluster resources." lightbox="./media/create-cluster-portal/multi-datacenter.png" border="true":::

## Troubleshooting

If you encounter an error when applying permissions to your Virtual Network using Azure CLI, such as *Cannot find user or service principal in graph database for 'e5007d2c-4b13-4a74-9b6a-605d99f03501'*, you can apply the same permission manually from the Azure portal. Learn how to do this [here](add-service-principal.md).

> [!NOTE] 
> The Azure Cosmos DB role assignment is used for deployment purposes only. Azure Managed Instanced for Apache Cassandra has no backend dependencies on Azure Cosmos DB.   -->

## <a name="connecting-to-your-cluster"></a>클러스터에 연결

Apache Cassandra용 Azure Managed Instance는 공용 IP 주소가 포함된 노드를 만들지 않으므로 새로 만든 Cassandra 클러스터에 연결하려면 VNet 내에 다른 리소스를 만들어야 합니다. 애플리케이션이나 Apache의 오픈 소스 쿼리 도구인 [CQLSH](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html)가 포함된 가상 머신이 설치될 수 있습니다. [템플릿](https://azure.microsoft.com/resources/templates/vm-simple-linux/)을 사용하여 Ubuntu 가상 머신을 배포할 수 있습니다. 배포된 경우 SSH를 사용하여 가상 머신에 연결하고 아래 명령을 사용하여 CQLSH를 설치합니다.

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
initial_admin_password="Password provided when creating the cluster"
cqlsh $host 9042 -u cassandra -p $initial_admin_password --ssl
```


## <a name="clean-up-resources"></a>리소스 정리

이 관리형 인스턴스 클러스터를 더 이상 사용하지 않으려면 다음 단계에 따라 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
1. 목록에서 이 빠른 시작에서 만든 리소스 그룹을 선택합니다.
1. 리소스 그룹 **개요** 창에서 **리소스 그룹 삭제** 를 선택합니다.
1. 새 창에서 삭제할 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 Azure Portal을 사용하여 Apache Cassandra 클러스터용 Azure Managed Instance를 생성하는 방법을 알아보았습니다. 이제 클러스터 사용을 시작할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Databricks를 사용하여 관리형 Apache Spark 클러스터 배포](deploy-cluster-databricks.md)