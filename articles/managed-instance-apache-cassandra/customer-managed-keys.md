---
title: Apache Cassandra 고객 관리형 키에 대한 Azure Managed Instance
description: 고객 관리형 키
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 6088f87312215791ac815051b606606112160d19
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131859260"
---
# <a name="customer-managed-keys---overview"></a>고객 관리형 키 - 개요

Apache Cassandra용 Azure Managed Instance 사용자 고유의 키를 사용하여 디스크의 데이터를 암호화하는 기능을 제공합니다. 이 문서에서는 Azure Key Vault 사용하여 고객 관리형 키를 구현하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Key Vault 사용하여 비밀을 설정합니다. [여기에서](/azure/key-vault/secrets/about-secrets)Azure Key Vault 대해 자세히 알아보세요.
- 리소스 그룹에 가상 네트워크를 배포하고 Azure Cosmos DB 서비스 주체를 구성원으로 사용하여 네트워크 기여자 역할을 적용했습니다. 자세한 내용은 [Azure CLI 사용하여 Apache Cassandra 클러스터용 Azure Managed Instance 만들기를](create-cluster-cli.md) 참조하세요. 


## <a name="create-a-cluster-with-system-assigned-identity"></a><a id="create-cluster"></a>시스템 할당 ID를 사용하여 클러스터 만들기

   > [!NOTE]
   > 필수 구성요소에서 설명한 대로 배포 실패를 방지하려면 관리형 인스턴스 클러스터를 배포하기 전에 가상 네트워크에 적절한 역할을 적용했는지 확인합니다.
   > ```azurecli-interactive  
   >     az role assignment create \
   >     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
   >     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
   >     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   > ```

1. ID 형식을 SystemAssigned로 지정하고 , , 및 를 적절한 값으로 대체하여 `<subscriptionID>` `<resourceGroupName>` `<vnetName>` `<subnetName>` 클러스터를 만듭니다.

    ```azurecli-interactive    
    subnet="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
    cluster="thvankra-cmk-test-wcus"
    group="thvankra-nova-cmk-test"
    region="westcentralus"
    password="PlaceholderPassword"
    
    az managed-cassandra cluster create \
        --identity-type SystemAssigned \
        --resource-group $group \
        --location $region \
        --cluster-name $cluster \
        --delegated-management-subnet-id $subnet \
        --initial-cassandra-admin-password $password
    ```

1. 만든 클러스터의 ID 정보 얻기

    ```azurecli-interactive
    az managed-cassandra cluster show -c $cluster -g $group
    ```

    출력에는 아래와 같은 ID 섹션이 포함됩니다. `principalId`나중에 사용할 수 있는 복사:

    ```shell
      "identity": {
        "principalId": "1aa51c7f-196a-4013-a656-1ccabfdc54e0",
        "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "type": "SystemAssigned"
      }
    ```
 
1. Azure Key Vault 키에 대한 액세스 정책을 만듭니다.

   :::image type="content" source="./media/cmk/key-vault-access-policy-1.png" alt-text="Key Vault 액세스 정책 1" lightbox="./media/cmk/key-vault-access-policy-1.png" border="true":::

1. `get`위에서 `wrap` `unwrap` 검색한 클러스터에 키 자격 증명 모음에 대한 및 키 권한을 `principalId` 할당합니다. 포털에서 클러스터 이름으로 클러스터의 보안 주체 ID를 조회할 수도 있습니다.
 

   :::image type="content" source="./media/cmk/key-vault-access-policy-2.png" alt-text="Key Vault 액세스 정책 2" lightbox="./media/cmk/key-vault-access-policy-2.png" border="true":::

   > [!WARNING]
   > 키 자격 증명 모음에 제거 보호가 사용하도록 설정되어 있는지 확인합니다. 데이터 센터 배포가 없으면 실패합니다. 

1. 를 클릭하여 액세스 정책을 추가한 후 `add` 저장해야 합니다.

   :::image type="content" source="./media/cmk/save.png" alt-text="액세스 정책 저장" lightbox="./media/cmk/key-vault-access-policy-2.png" border="true":::

1. 키 식별자를 얻으려면 키를 선택합니다.

   :::image type="content" source="./media/cmk/select-key.png" alt-text="키 선택" lightbox="./media/cmk/key-identifier-1.png" border="true":::

1. 현재 버전을 클릭합니다.

   :::image type="content" source="./media/cmk/current-version.png" alt-text="현재 버전 선택" lightbox="./media/cmk/key-identifier-1.png" border="true":::

1. 나중에 사용할 수 있는 키 식별자를 저장합니다.

   :::image type="content" source="./media/cmk/key-identifier-2.png" alt-text="키 식별자 2단계" lightbox="./media/cmk/key-identifier-1.png" border="true":::


1. `<key identifier>`아래와 같이 관리 디스크(managed-disk-customer-key-uri) 및 백업 스토리지(backup-storage-customer-key-uri) 암호화 모두에 대해 동일한 키(이전 단계에서 복사한 URI)로 대체하여 데이터 센터를 만듭니다(앞에서 사용한 것과 동일한 값 사용). `subnet` 

    ```azurecli-interactive
    managedDiskKeyUri = "<key identifier>"
    backupStorageKeyUri = "<key identifier>"
    group="thvankra-nova-cmk-test"
    region="westcentralus"
    cluster="thvankra-cmk-test-2"
    dc="dc1"
    nodecount=3
    subnet="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        
    az managed-cassandra datacenter create \
        --resource-group $group \
        --cluster-name $cluster \
        --data-center-name $dc \
        --managed-disk-customer-key-uri $managedDiskKeyUri \
        --backup-storage-customer-key-uri $backupStorageKeyUri \
        --node-count $nodecount \
        --delegated-subnet-id $subnet \
        --data-center-location $region \
        --sku Standard_DS14_v2
    ```

1. 아래와 같이 ID 정보가 없는 기존 클러스터에 ID를 할당할 수 있습니다.

    ```azurecli-interactive
    az managed-cassandra cluster update --identity-type SystemAssigned -g $group -c $cluster
    ```

## <a name="rotating-the-key"></a><a id="update-cluster"></a>키 회전

1. 다음은 키를 업데이트하는 명령입니다.

    ```azurecli-interactive
    managedDiskKeyUri = "<key identifier>"
    backupStorageKeyUri = "<key identifier>"
    
    az managed-cassandra datacenter update \
        --resource-group $group \
        --cluster-name $cluster \ 
        --data-center-name $dc \
        --managed-disk-customer-key-uri $managedDiskKeyUri \
        --backup-storage-customer-key-uri $backupStorageKeyUri
    ```