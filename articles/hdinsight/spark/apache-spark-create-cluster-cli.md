---
title: '빠른 시작: Azure CLI를 사용하는 Apache Spark - Azure HDInsight'
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Azure HDInsight에서 Apache Spark 클러스터를 만드는 방법을 보여줍니다.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.custom: devx-track-azurecli, mode-api
ms.openlocfilehash: f7477a874cf7d1f7775118e9a57c54208c3db2f3
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133041811"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure HDInsight에서 Apache Spark 클러스터 만들기

이 빠른 시작에서는 Azure CLI(명령줄 인터페이스)를 사용하여 Azure HDInsight에서 Apache Spark 클러스터를 만드는 방법에 대해 알아봅니다. Azure HDInsight는 엔터프라이즈를 위한 관리형의 전체 스펙트럼 오픈 소스 분석 서비스입니다. HDInsight용 Apache Spark 프레임워크를 통해 메모리 내 처리 기능을 사용하여 데이터 분석 및 클러스터 컴퓨팅을 신속하게 처리합니다. Azure CLI는 Azure 리소스를 관리하기 위한 Microsoft의 플랫폼 간 명령줄 환경입니다.

여러 클러스터를 함께 사용하는 경우 가상 네트워크를 만들고, Spark 클러스터를 사용하는 경우 Hive Warehouse Connector도 사용하는 것이 좋습니다. 자세한 내용은 [Azure HDInsight에 대한 가상 네트워크 계획](../hdinsight-plan-virtual-network-deployment.md) 및 [Hive Warehouse Connector를 사용하여 Apache Spark 및 Apache Hive 통합](../interactive-query/apache-hive-warehouse-connector.md)을 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-apache-spark-cluster"></a>Apache Spark 클러스터 만들기

1. Azure 구독에 로그인합니다. Azure Cloud Shell을 사용하려는 경우 다음 코드 블록의 오른쪽 위 모서리에 있는 **사용해보기** 를 선택합니다. 그렇지 않으면 다음 명령을 입력합니다.

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. 환경 변수를 설정합니다. 이 빠른 시작에서 변수를 사용하는 것은 Bash를 기반으로 합니다. 다른 환경에서는 약간의 변형이 필요합니다. 아래 코드 조각에서 RESOURCEGROUPNAME, LOCATION, CLUSTERNAME, STORAGEACCOUNTNAME 및 PASSWORD를 원하는 값으로 바꿉니다. 그런 다음, CLI 명령을 입력하여 환경 변수를 설정합니다.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'
    
    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. 아래 명령을 입력하여 리소스 그룹을 만듭니다.

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. 아래 명령을 입력하여 Azure Storage 계정을 만듭니다.

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Azure Storage 계정에서 기본 키를 추출하고 아래 명령을 입력하여 변수에 저장합니다.

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. 아래 명령을 입력하여 Azure Storage 컨테이너를 만듭니다.

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. 다음 명령을 입력하여 Apache Spark 클러스터를 만듭니다.

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

## <a name="clean-up-resources"></a>리소스 정리

빠른 시작을 완료한 후 클러스터를 삭제하는 것이 좋습니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 스토리지에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다.

리소스를 제거하려면 다음 명령의 전체 또는 일부를 입력합니다.

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 Azure HDInsight에서 Apache Spark 클러스터를 만드는 방법에 대해 알아보았습니다.  다음 자습서를 진행하여 샘플 데이터에서 대화형 쿼리를 실행하는 데 HDInsight 클러스터를 사용하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [Apache Spark에서 대화형 쿼리 실행](./apache-spark-load-data-run-query.md)
