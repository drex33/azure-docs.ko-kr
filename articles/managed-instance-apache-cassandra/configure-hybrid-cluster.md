---
title: 빠른 시작 - Apache Cassandra용 Azure Managed Instance를 사용하여 하이브리드 클러스터 구성
description: 이 빠른 시작에서는 Apache Cassandra용 Azure Managed Instance를 사용하여 하이브리드 클러스터를 구성하는 방법을 보여 줍니다.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 09/08/2021
ms.openlocfilehash: 00fcf15f94b14b7f16d65fd008ae89b2320c49ba
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124775090"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>빠른 시작: Apache Cassandra용 Azure Managed Instance를 사용하여 하이브리드 클러스터 구성(미리 보기)

Apache Cassandra용 Azure Managed Instance는 관리형 오픈 소스 Apache Cassandra 데이터 센터의 자동화된 배포 및 크기 조정 작업을 제공합니다. 이 서비스를 사용하면 하이브리드 시나리오를 가속화하고 지속적인 유지 관리를 줄일 수 있습니다.

> [!IMPORTANT]
> Apache Cassandra용 Azure Managed Instance는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 빠른 시작에서는 Azure CLI 명령을 사용하여 하이브리드 클러스터를 구성하는 방법을 보여 줍니다. 온-프레미스 또는 자체 호스팅 환경에 기존 데이터 센터가 있는 경우 Apache Cassandra용 Azure Managed Instance를 사용하여 해당 클러스터에 다른 데이터 센터를 추가하고 유지 관리할 수 있습니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* 이 문서를 진행하려면 Azure CLI 버전 2.12.1 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

  > [!NOTE]
  > Cloud Shell에서 실행되는 CLI 모듈 `cosmosdb-preview`의 버전이 **0.9.0**(또는 그 이상)이어야 합니다. 그래야 아래에 나열된 모든 명령이 제대로 작동할 수 있습니다. `az --version`을 실행하여 확장 버전을 확인할 수 있습니다. 필요한 경우 `az extension update --name cosmosdb-preview`를 사용하여 업그레이드합니다.

* 자체 호스팅 또는 온-프레미스 환경에 연결된 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). 온-프레미스 환경을 Azure에 연결하는 방법에 대한 자세한 내용은 [Azure에 온-프레미스 네트워크 연결](/azure/architecture/reference-architectures/hybrid-networking/) 문서를 참조하세요.

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>하이브리드 클러스터 구성

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Virtual Network로 이동합니다.

1. **서브넷** 탭을 열고 새 서브넷을 만듭니다. **서브넷 추가** 양식의 필드에 대한 자세한 내용은 [Virtual Network](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) 문서를 참조하세요.

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Virtual Network에 새 서브넷을 추가합니다." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

   > [!NOTE]
   > Azure Managed Instance for Apache Cassandra를 배포하려면 인터넷 액세스가 필요합니다. 인터넷 액세스가 제한되는 환경에서는 배포가 실패합니다. Managed Cassandra가 올바르게 작동하는 데 필요한 다음과 같은 중요한 Azure 서비스에 대한 VNet 내에서 액세스가 차단되어 있는지 확인합니다. 또한 [여기](network-rules.md)에서 IP 주소 및 포트 의존성의 전체 목록을 확인할 수 있습니다. 
   > - Azure Storage
   > - Azure KeyVault
   > - Azure Virtual Machine Scale Sets
   > - Azure 모니터링
   > - Azure Active Directory
   > - Azure Security

1. 이제 Azure CLI를 사용하여 Cassandra Managed Instance를 사용하는 데 필요한 몇 가지 특수 권한을 VNet 및 서브넷에 적용합니다. `az role assignment create` 명령을 사용하여 `<subscriptionID>`, `<resourceGroupName>` 및 `<vnetName>`을 적절한 값으로 바꿉니다.

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   ```

   > [!NOTE]
   > 이전 명령에서 `assignee`와 `role` 값은 각각 고정된 서비스 주체 및 역할 식별자입니다.

1. 다음으로, 하이브리드 클러스터의 리소스를 구성합니다. 클러스터가 이미 있으므로 여기서는 기존 클러스터의 이름을 식별하는 논리적 리소스만 클러스터 이름이 됩니다. 다음 스크립트에서 `clusterName` 및 `clusterNameOverride` 변수를 정의할 때는 기존 클러스터의 이름을 사용해야 합니다. 
 
   또한 기존 데이터 센터의 시드 노드와 노드 간 암호화에 필요한 가십 인증서가 최소한 필요합니다. Azure Managed Instance for Apache Cassandra에는 데이터 센터 간 통신을 위해 노드 간 암호화가 필요합니다. 기존 클러스터에서 노드 간 암호화를 구현하지 않은 경우 이를 구현해야 합니다. [여기](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLNodeToNode.html)에 있는 설명서를 참조하세요. 인증서의 위치에 대한 경로를 제공해야 합니다. 각 인증서는 PEM 형식이어야 합니다(예: `-----BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----`). 일반적으로 인증서를 구현하는 방법에는 두 가지가 있습니다.

   1. 자체 서명된 인증서. 각 노드에 대한 프라이빗 및 공용(CA 없음) 인증서를 의미합니다. 이 경우 모든 공용 인증서가 필요합니다.

   1. CA 서명 인증서. 자체 서명 CA 또는 퍼블릭 CA일 수 있습니다. 이 경우 루트 CA 인증서([프로덕션](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLCertWithCA.html)용 SSL 인증서 준비 지침 참조) 및 모든 중간자(해당하는 경우)가 필요합니다.

   필요에 따라 클라이언트-노드 인증서도 구현한 경우([여기](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLClientToNode.html) 참조) 하이브리드 클러스터를 만들 때 이를 동일한 형식으로 제공해야 합니다. 아래 샘플을 참조하세요.

   > [!NOTE]
   > 아래에 제공할 `delegatedManagementSubnetId` 변수 값은 위의 명령에서 제공한 `--scope` 값과 정확히 동일합니다.

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
     --cluster-name $clusterName \
     --resource-group $resourceGroupName \
     --location $location \
     --delegated-management-subnet-id $delegatedManagementSubnetId \
     --external-seed-nodes 10.52.221.2 10.52.221.3 10.52.221.4 \
     --external-gossip-certificates /usr/csuser/clouddrive/rootCa.pem /usr/csuser/clouddrive/gossipKeyStore.crt_signed
     # optional - add your existing datacenter's client-to-node certificates (if implemented):
     # --client-certificates /usr/csuser/clouddrive/rootCa.pem /usr/csuser/clouddrive/nodeKeyStore.crt_signed 
   ```

   > [!NOTE]
   > 클러스터에 노드 간 및 클라이언트-노드 암호화가 이미 설정된 경우 기존 클라이언트 및/또는 가십 SSL 인증서가 유지되는 위치를 알아야 합니다. 잘 모르겠으면 `keytool -list -keystore <keystore-path> -rfc -storepass <password>`를 실행하여 인증서를 출력해야 합니다. 

1. 클러스터 리소스가 생성되면 다음 명령을 실행하여 클러스터 설정 세부 정보를 가져옵니다.

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
   ```

1. 이전 명령은 관리형 인스턴스 환경에 대한 정보를 반환합니다. 기존 데이터 센터의 노드에 대한 신뢰 저장소에 이를 설치할 수 있도록 가십 인증서가 필요합니다. 다음 스크린샷에서는 이전 명령의 출력과 인증서 형식을 보여 줍니다.

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="클러스터에서 인증서 세부 정보를 가져옵니다." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

   > [!NOTE]
   > 위의 명령에서 반환된 인증서에는 텍스트로 표현되는 줄바꿈(예: `\r\n`)이 포함되어 있습니다. 기존 데이터 센터의 신뢰 저장소로 가져오기 전에 각 인증서를 파일에 복사하고 형식을 지정해야 합니다.

   > [!TIP]
   > 위의 스크린샷에 표시된 `gossipCertificates` 배열 값을 파일에 복사하고, 다음 bash 스크립트(플랫폼용 [jq를 다운로드하고 설치](https://stedolan.github.io/jq/download/)해야 하는 경우)를 사용하여 인증서의 형식을 지정하고 각각에 대해 별도의 pem 파일을 만듭니다.
   >
   > ```bash
   > readarray -t cert_array < <(jq -c '.[]' gossipCertificates.txt)
   > # iterate through the certs array, format each cert, write to a numbered file.
   > num=0
   > filename=""
   > for item in "${cert_array[@]}"; do
   >   let num=num+1
   >   filename="cert$num.pem"
   >   cert=$(jq '.pem' <<< $item)
   >   echo -e $cert >> $filename
   >   sed -e '1d' -e '$d' -i $filename
   > done
   > ```

1. 다음으로, 하이브리드 클러스터에 새 데이터 센터를 만듭니다. 변수 값을 클러스터 세부 정보로 바꾸어야 합니다.

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --data-center-location $dataCenterLocation \
     --delegated-subnet-id $delegatedManagementSubnetId \
     --node-count 9 
   ```

1. 이제 새 데이터 센터가 생성되었으므로 데이터 센터 표시 명령을 실행하여 세부 정보를 확인합니다.

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName 
   ```

1. 이전 명령은 새 데이터 센터의 시드 노드를 출력합니다. 

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="데이터 센터 세부 정보를 가져옵니다." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

1. 이제 새 데이터 센터의 시드 노드를 [cassandra.yaml](https://docs.datastax.com/en/cassandra-oss/3.0/cassandra/configuration/configCassandra_yaml.html) 파일 내에 있는 기존 데이터 센터의 [시드 노드 구성](https://docs.datastax.com/en/cassandra-oss/3.0/cassandra/configuration/configCassandra_yaml.html#configCassandra_yaml__seed_provider)에 추가합니다. 그리고 각 인증서에 대해 `keytool` 명령을 사용하여 기존 클러스터의 각 노드에 대한 신뢰 저장소에 이전에 수집한 관리형 인스턴스 가십 인증서를 설치합니다.

   ```bash
   keytool -importcert -keystore generic-server-truststore.jks -alias CassandraMI -file cert1.pem -noprompt -keypass myPass -storepass truststorePass
   ```

   > [!NOTE]
   > 데이터 센터를 더 추가하려는 경우 시드 노드만 있으면 위의 단계를 반복할 수 있습니다. 

1. 마지막으로, 다음 CQL 쿼리를 사용하여 클러스터의 모든 데이터 센터를 포함하도록 각 키스페이스에서 복제 전략을 업데이트합니다.

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'on-premise-dc': 3, 'managed-instance-dc': 3};
   ```

   암호 테이블도 업데이트해야 합니다.

   ```bash
   ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'on-premise-dc': 3, 'managed-instance-dc': 3}
   ```

## <a name="troubleshooting"></a>문제 해결

Virtual Network에 권한을 적용할 때 *'e5007d2c-4b13-4a74-9b6a-605d99f03501'에 대한 그래프 데이터베이스에서 사용자 또는 서비스 주체를 찾을 수 없음* 과 같은 오류가 발생하는 경우 Azure Portal에서 동일한 권한을 수동으로 적용할 수 있습니다. 포털에서 권한을 적용하려면 기존 가상 네트워크의 **액세스 제어(IAM)** 창으로 이동하여 "Azure Cosmos DB"에 대한 역할 할당을 "네트워크 관리자" 역할에 추가합니다. "Azure Cosmos DB"를 검색할 때 두 개의 항목이 나타나면 다음 이미지에 표시된 대로 항목을 추가합니다. 

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

이 빠른 시작에서는 Azure CLI 및 Apache Cassandra용 Azure Managed Instance를 사용하여 하이브리드 클러스터를 만드는 방법을 알아보았습니다. 이제 클러스터 사용을 시작할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 리소스 관리](manage-resources-cli.md)
