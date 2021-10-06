---
title: Log Analytics 클러스터에 대한 Resource Manager 템플릿 샘플
description: Log Analytics 클러스터를 배포하기 위한 샘플 Azure Resource Manager 템플릿
ms.topic: sample
author: yossiy
ms.author: yossiy
ms.date: 09/12/2021
ms.openlocfilehash: 1e585db0e7e0cec05319c74419bb840200d1d86a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659233"
---
# <a name="resource-manager-template-samples-for-log-analytics-clusters-in-azure-monitor"></a>Azure Monitor의 Log Analytics 클러스터에 대한 Resource Manager 템플릿 샘플
이 문서에는 Azure Monitor에서 Log Analytics 클러스터를 만들고 구성할 수 있도록 [Azure Resource Manager 템플릿](../../azure-resource-manager/templates/syntax.md) 샘플이 포함되어 있습니다. 각 샘플에는 템플릿 파일 및 템플릿에 제공할 샘플 값이 포함된 매개 변수 파일이 포함되어 있습니다.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="template-references"></a>템플릿 참조

- [Microsoft.OperationalInsights clusters](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/clusters) 

## <a name="create-a-log-analytics-cluster"></a>Log Analytics 클러스터 만들기
다음 샘플에서는 빈 Log Analytics 클러스터를 새로 만듭니다.

### <a name="template-file"></a>템플릿 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Log Analytics cluster."
      }
    },
    "CommitmentTier": {
      "type": "int",
      "allowedValues": [
        500,
        1000,
        2000,
        5000
      ],
      "defaultValue": 500,
      "metadata": {
        "description": "The Capacity Reservation value."
      }
  },
  "billingType": {
      "type": "string",
      "allowedValues": [
        "Cluster",
        "Workspaces"
      ],
      "defaultValue": "Cluster",
      "metadata": {
          "description": "The billing type settings. Can be 'Cluster' (default) or 'Workspaces' for proportional billing on workspaces."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.OperationalInsights/clusters",
      "apiVersion": "2021-06-01",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
      },
      "sku": {
        "name": "CapacityReservation",
        "capacity": "[parameters('CommitmentTier')]"
      },
      "properties":  {
        "billingType": "[parameters('billingType')]"
      }
    }
  ]
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "MyCluster"
    },
    "CommitmentTier": {
      "value": 500
    },
    "billingType": {
      "value": "Cluster"
    }
  }
}
```

## <a name="update-a-log-analytics-cluster"></a>Log Analytics 클러스터 업데이트
다음 샘플에서는 고객 관리형 키를 사용하도록 Log Analytics 클러스터를 업데이트합니다.

### <a name="template-file"></a>템플릿 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Log Analytics cluster."
      }
    },
    "keyVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The key identifier URI."
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "The key name."
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "The key version. When empty, latest key version is used."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.OperationalInsights/clusters",
      "apiVersion": "2021-06-01",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties":  {
        "keyVaultProperties": {
        "keyVaultUri": "https://key-vault-name.vault.azure.net",
        "keyName": "key-name",
        "keyVersion": "current-version"
        }
      }  
    }
  ]
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "MyCluster"
    },
    "keyVaultUri": {
      "value": "https://key-vault-name.vault.azure.net"
    },
    "keyName": {
      "value": "MyKeyName"
    },
    "keyVersion": {
      "value": ""
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

* [Azure Monitor에 대한 다른 샘플 템플릿을 가져옵니다](../resource-manager-samples.md).
* [Log Analytics 전용 클러스터에 대해 자세히 알아보세요](./logs-dedicated-clusters.md).
* [에이전트 데이터 원본에 대해 자세히 알아봅니다](../agents/agent-data-sources.md).
