---
title: Azure HDInsight 클러스터의 VM 다시 부팅
description: Azure HDInsight 클러스터의 응답하지 않는 VM을 다시 부팅하는 방법을 알아봅니다.
ms.custom: hdinsightactive, devx-track-azurepowershell
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: f2bb1ebd6e8db80a071dc3f82e1eedded26f6116
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634488"
---
# <a name="reboot-vms-for-hdinsight-clusters"></a>HDInsight 클러스터의 VM 다시 부팅

Azure HDInsight 클러스터는 VM(가상 머신) 그룹을 클러스터 노드로 포함합니다. 장기 실행 클러스터의 경우 이러한 노드는 다양한 이유로 응답하지 않을 수 있습니다. 이 문서에서는 HDInsight 클러스터에서 응답하지 않는 VM을 다시 부팅하는 방법을 설명합니다.

## <a name="when-to-reboot"></a>다시 부팅해야 하는 경우

> [!WARNING]
> 클러스터에서 VM을 다시 부팅하면 노드를 사용할 수 없으며 노드의 서비스를 다시 시작해야 합니다.

노드가 다시 부팅되면 클러스터가 비정상 상태가 되고 작업이 느려지거나 실패할 수 있습니다. 활성 헤드 노드를 다시 부팅하려는 경우 실행 중인 모든 작업이 중지됩니다. 서비스가 다시 실행될 때까지 클러스터에 작업을 제출할 수 없습니다. 이러한 이유로 필요한 경우에만 VM을 다시 부팅해야 합니다. 다음과 같은 경우 VM을 다시 부팅하는 것이 좋습니다.

- SSH를 사용하여 노드에 들어갈 수는 없지만, 노드가 ping에는 응답합니다.
- 작업자 노드가 Ambari UI에서 하트비트 없이 다운됩니다.
- 노드의 임시 디스크가 가득 찼습니다.
- VM의 프로세스 테이블에는 프로세스가 완료된 많은 항목이 있지만 “종료된 상태”로 나열됩니다.

> [!NOTE]
> 다시 부팅하면 데이터가 손실될 수 있으므로 **HBase** 및 **Kafka** 클러스터에서는 VM 다시 부팅이 지원되지 않습니다.

## <a name="use-powershell-to-reboot-vms"></a>PowerShell을 사용하여 VM 다시 부팅

노드 다시 부팅 작업을 사용하려면 노드 나열 및 노드 다시 시작의 두 단계가 필요합니다.

1. 노드를 나열합니다. [Get-AzHDInsightHost](/powershell/module/az.hdinsight/get-azhdinsighthost)에서 클러스터 노드 목록을 가져올 수 있습니다.

      ```
      Get-AzHDInsightHost -ClusterName myclustername
      ```

1. 호스트를 다시 시작합니다. 다시 부팅하려는 노드의 이름을 확인한 후 [Restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost)를 사용하여 노드를 다시 시작합니다.

      ```
      Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
      ```

## <a name="use-a-rest-api-to-reboot-vms"></a>REST API를 사용하여 VM 다시 부팅

API 문서의 **체험해 보기** 기능을 사용하여 HDInsight에 요청을 보낼 수 있습니다. 노드 다시 부팅 작업을 사용하려면 노드 나열 및 노드 다시 시작의 두 단계가 필요합니다.

1. 노드를 나열합니다. REST API 또는 Ambari에서 클러스터 노드 목록을 가져올 수 있습니다. 자세한 내용은 [HDInsight 호스트 나열 REST API 작업](/rest/api/hdinsight/virtualmachines/listhosts)을 참조하세요.

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

1. 호스트를 다시 시작합니다. 다시 부팅하려는 노드의 이름을 확인한 후 REST API를 사용하여 노드를 다시 부팅함으로써 노드를 다시 시작합니다. 노드 이름은 *NodeType(wn/hn/zk)*  + *x* + *클러스터 이름의 처음 여섯 글자* 패턴을 따릅니다. 자세한 내용은 [HDInsight 호스트 다시 시작 REST API 작업](/rest/api/hdinsight/virtualmachines/restarthosts)을 참조하세요.

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

다시 부팅하려는 노드의 실제 이름은 요청 본문의 JSON 배열에 지정됩니다.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>다음 단계

* [Restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [HDInsight 가상 머신 REST API](/rest/api/hdinsight/virtualmachines)
* [HDInsight REST API](/rest/api/hdinsight/)
