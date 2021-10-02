---
title: Bicep 함수 - 배포
description: Bicep 파일에서 배포 정보를 검색하는 데 사용할 함수에 대해 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 9d06bd78d7ba6dcf0f73326f744e975a9c4e3f53
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352409"
---
# <a name="deployment-functions-for-bicep"></a>Bicep의 배포 함수

이 문서에서는 현재 배포와 관련 된 값을 가져오는 Bicep 함수에 대해 설명 합니다.

## <a name="deployment"></a>배포

`deployment()`

현재 배포 작업에 대한 정보를 반환합니다.

네임 스페이스: [az](bicep-functions.md#namespaces-for-functions).

### <a name="return-value"></a>반환 값

이 함수는 배포하는 동안 전달되는 개체를 반환합니다. 반환된 개체의 속성은 다음 여부에 따라 달라집니다.

* 로컬 Bicep 파일 배포
* 리소스 그룹에 배포 또는 기타 범위([Azure 구독](deploy-to-subscription.md), [관리 그룹](deploy-to-management-group.md) 또는 [테넌트](deploy-to-tenant.md)) 중 하나에 배포

리소스 그룹에 로컬 Bicep 파일을 배포하는 경우 함수는 다음 형식을 반환합니다.

```json
{
  "name": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Azure 구독, 관리 그룹 또는 테넌트에 배포하는 경우 반환 개체에는 `location` 속성이 포함됩니다. 위치 속성은 로컬 Bicep 파일을 배포할 때 포함됩니다. 형식:

```json
{
  "name": "",
  "location": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

### <a name="example"></a>예제

다음 예제에서는 배포 개체를 반환합니다.

```bicep
output deploymentOutput object = deployment()
```

앞의 예제에서는 다음 개체를 반환합니다.

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

## <a name="environment"></a>environment

`environment()`

배포에 사용되는 Azure 환경에 대한 정보를 반환합니다.

네임 스페이스: [az](bicep-functions.md#namespaces-for-functions).

### <a name="return-value"></a>반환 값

이 함수는 현재 Azure 환경에 대한 속성을 반환합니다. 다음 예제는 전체 Azure에 대한 속성을 보여 줍니다. 소버린 클라우드가 반환하는 속성은 약간 다를 수 있습니다.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>예제

다음 예제 Bicep 파일은 환경 개체를 반환합니다.

```bicep
output environmentOutput object = environment()
```

위의 예제는 전체 Azure에 배포되는 경우 다음 개체를 반환합니다.

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="next-steps"></a>다음 단계

* 리소스, 리소스 그룹 또는 구독에서 값을 가져오려면 [리소스 함수](./bicep-functions-resource.md)를 참조하세요.
