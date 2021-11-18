---
title: Azure Chaos Studio의 대상 및 기능에 대한 Azure Resource Manager 템플릿 샘플
description: 대상 및 기능을 사용하여 Azure Chaos Studio에 리소스를 온보딩하기 위한 샘플 Azure Resource Manager 템플릿
services: chaos-studio
author: johnkemnetz
ms.topic: sample
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: 6e5c6b0610d2a2d48523a5d2a7a95c28cd8bae59
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404439"
---
# <a name="resource-manager-template-samples-for-targets-and-capabilities-in-azure-chaos-studio"></a>Azure Chaos Studio의 대상 및 기능에 대한 Resource Manager 템플릿 샘플
이 문서에는 Azure Chaos Studio에 리소스를 온보딩하기 위해 [대상 및 기능](chaos-studio-targets-capabilities.md)을 만드는 샘플 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/syntax.md)이 포함되어 있습니다. 각 샘플에는 템플릿 파일과 템플릿에 제공할 샘플 값이 포함된 매개 변수 파일이 포함되어 있습니다.

## <a name="onboard-service-direct-target-and-capabilities-single-capability"></a>서비스 직접 대상 및 기능 온보딩(단일 기능)

이 샘플에서는 [대상 및 기능](chaos-studio-targets-capabilities.md)을 사용하여 Azure Cosmos DB 인스턴스를 온보딩합니다. 템플릿은 [오류 라이브러리](chaos-studio-fault-library.md)를 참조하여 모든 서비스 직접 대상 및 기능에 대해 수정할 수 있습니다.

### <a name="template-file"></a>템플릿 파일

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource being enabled."
      }
    },
    "resourceGroup": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group where the resource being enabled is located."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location"
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.DocumentDB/databaseAccounts/providers/targets",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-CosmosDB')]",
      "location": "[parameters('location')]",
      "properties": {}
    },
    {
      "type": "Microsoft.DocumentDB/databaseAccounts/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-CosmosDB/Failover-1.0')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.DocumentDB/databaseAccounts', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-CosmosDB')]"
      ],
      "properties": {}
    }
  ],
  "outputs": {}
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "resourceName": {
        "value": "my-cosmos-db"
      },
      "resourceGroup": {
        "value": "my-rg"
      }
  }
}
```

## <a name="onboard-service-direct-target-and-capabilities-multiple-capabilities"></a>서비스 직접 대상 및 기능 온보딩(다중 기능)

이 샘플에서는 [대상 및 기능](chaos-studio-targets-capabilities.md)을 사용하여 Azure Kubernetes Service 클러스터를 온보딩합니다.

### <a name="template-file"></a>템플릿 파일

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource being enabled."
      }
    },
    "resourceGroup": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group where the resource being enabled is located."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location"
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh')]",
      "location": "[parameters('location')]",
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/NetworkChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/PodChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/StressChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/IOChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/TimeChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/KernelChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/DNSChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/HTTPChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    }
  ],
  "outputs": {}
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "resourceName": {
        "value": "my-aks-cluster"
      },
      "resourceGroup": {
        "value": "my-rg"
      }
  }
}
```

## <a name="next-steps"></a>다음 단계

* [Chaos Studio에 대해 자세히 알아보세요](chaos-studio-overview.md).
* [대상 및 기능에 대해 자세히 알아보세요](chaos-studio-targets-capabilities.md).
