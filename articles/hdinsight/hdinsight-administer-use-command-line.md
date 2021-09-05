---
title: Azure CLI를 사용하여 Azure HDInsight 클러스터 관리
description: Azure CLI를 사용하여 Azure HDInsight 클러스터를 관리하는 방법을 알아봅니다. 클러스터 유형으로는 Apache Hadoop, Spark, HBase, Storm, Kafka, 대화형 쿼리가 있습니다.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-azurecli
ms.date: 02/26/2020
ms.openlocfilehash: 74f228c8c1d9ca29e3f9e60062891a90a3a4abf6
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "122642110"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Azure CLI를 사용하여 Azure HDInsight 클러스터 관리

[!INCLUDE [selector](includes/hdinsight-portal-management-selector.md)]

[Azure CLI](/cli/azure/)를 사용하여 Azure HDInsight 클러스터를 관리하는 방법을 알아봅니다. Azure CLI(명령줄 인터페이스)는 Azure 리소스를 관리하기 위한 Microsoft의 플랫폼 간 명령줄 환경입니다.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure CLI. Azure CLI를 설치하지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)에 나온 단계를 참조하세요.

* HDInsight의 Apache Hadoop 클러스터. [Linux에서 HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

## <a name="connect-to-azure"></a>Azure에 연결

Azure 구독에 로그인합니다. Azure Cloud Shell을 사용하려는 경우 코드 블록의 오른쪽 위 모서리에 있는 **사용해보기** 를 선택합니다. 그렇지 않은 경우 아래 명령을 입력합니다.

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>클러스터 나열

[az hdinsight list](/cli/azure/hdinsight#az_hdinsight_list)를 사용하여 클러스터를 나열합니다. `RESOURCE_GROUP_NAME`을 클러스터의 이름으로 바꿔서 아래 명령을 편집한 후, 다음 명령을 입력합니다.

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>클러스터 표시

[az hdinsight show](/cli/azure/hdinsight#az_hdinsight_show)를 사용하여 지정된 클러스터에 대한 정보를 표시합니다. `RESOURCE_GROUP_NAME` 및 `CLUSTER_NAME`을 관련 정보로 바꿔서 아래 명령을 편집한 후, 다음 명령을 입력합니다.

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>클러스터 삭제

[az hdinsight delete](/cli/azure/hdinsight#az_hdinsight_delete)를 사용하여 지정된 클러스터를 삭제합니다. `RESOURCE_GROUP_NAME` 및 `CLUSTER_NAME`을 관련 정보로 바꿔서 아래 명령을 편집한 후, 다음 명령을 입력합니다.

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

또한 클러스터를 포함하는 리소스 그룹을 삭제하여 클러스터를 삭제할 수도 있습니다. 이렇게 하면 기본 스토리지 계정을 포함한 그룹 내 모든 리소스가 삭제됩니다.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>클러스터 크기 조정

[az hdinsight resize](/cli/azure/hdinsight#az_hdinsight_resize)를 사용하여 지정된 HDInsight 클러스터의 크기를 지정된 크기로 조정합니다. `RESOURCE_GROUP_NAME` 및 `CLUSTER_NAME`을 관련 정보로 바꿔서 아래 명령을 편집합니다. `WORKERNODE_COUNT`를 클러스터에 대해 원하는 작업자 노드 수로 바꿉니다. 클러스터 크기 조정에 대한 자세한 내용은 [HDInsight 클러스터 크기 조정](./hdinsight-scaling-best-practices.md)을 참조하세요. 다음 명령을 입력합니다.

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight 클러스터 관리 작업을 수행하는 여러 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure Portal을 사용하여 HDInsight의 Apache Hadoop 클러스터 관리](hdinsight-administer-use-portal-linux.md)
* [Azure PowerShell을 사용하여 HDInsight 클러스터 관리](hdinsight-administer-use-powershell.md)
* [Azure HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure CLI 시작](/cli/azure/get-started-with-azure-cli)
