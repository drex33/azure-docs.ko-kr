---
title: 프라이빗 링크를 통해 리소스 관리
description: 리소스에 대한 관리 액세스를 프라이빗 링크로 제한
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: eb7e81ef739fdb94bc91c65b079d5b76b22636c9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227403"
---
# <a name="use-rest-api-to-create-private-link-for-managing-azure-resources"></a>REST API를 사용하여 Azure 리소스를 관리하기 위한 프라이빗 링크 만들기

이 문서에서는 [Azure Private Link](../../private-link/index.yml)를 사용하여 구독에서 리소스 관리에 대한 액세스를 제한하는 방법을 설명합니다.

[!INCLUDE [Create content](../../../includes/resource-manager-create-rmpl.md)]

## <a name="create-resource-management-private-link"></a>리소스 관리 프라이빗 링크 만들기

리소스 관리 프라이빗 링크를 만들려면 다음 요청을 보냅니다.

```http
PUT
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01
```

요청 본문에서 리소스에 대해 원하는 위치를 포함합니다.

```json
{
  "location":"{region}"
}
```

작업은 다음을 반환합니다.

```json
{  
  "id": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{name}",
  "location": "{region}",
  "name": "{rmplName}",
  "properties": {
    "privateEndpointConnections": []
  },
  "resourceGroup": "{rgName}",
  "type": "Microsoft.Authorization/resourceManagementPrivateLinks"
}
```

새 리소스 관리 프라이빗 링크에 대해 반환되는 ID를 확인합니다. 이를 프라이빗 링크 연결을 만드는 데 사용하게 됩니다.

## <a name="create-private-link-association"></a>프라이빗 링크 연결 만들기

프라이빗 링크 연결을 만들려면 다음을 사용합니다.

```http
PUT
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupId}/providers/Microsoft.Authorization/privateLinkAssociations/{GUID}?api-version=2020-05-01 
```

요청 본문에 다음을 포함합니다.

```json
{
  "properties": {
    "privateLink": "/subscriptions/{subscription-id}/resourceGroups/{rg-name}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}",
    "publicNetworkAccess": "enabled"
  }
}
```

작업은 다음을 반환합니다.

```json
{
  "id": {plaResourceId},
  "name": {plaName},
  "properties": {
    "privateLink": {rmplResourceId},
    "publicNetworkAccess": "Enabled",
    "tenantId": "{tenantId}",
    "scope": "/providers/Microsoft.Management/managementGroups/{managementGroupId}"
  },
  "type": "Microsoft.Authorization/privateLinkAssociations"
}
```

## <a name="add-private-endpoint"></a>프라이빗 엔드포인트 추가

이 문서에서는 가상 네트워크가 이미 있다고 가정합니다. 프라이빗 엔드포인트에 사용할 서브넷에서는 프라이빗 엔드포인트 네트워크 정책을 비활성화해야 합니다. 프라이빗 엔드포인트 네트워크 정책을 해제하지 않은 경우 [프라이빗 엔드포인트에 대한 네트워크 정책 사용 안 함](../../private-link/disable-private-endpoint-network-policy.md)을 참조하세요.

프라이빗 엔드포인트를 만들려면 다음 작업을 사용합니다.

```http
PUT
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/privateEndpoints/{privateEndpointName}?api-version=2020-11-01
```

요청 본문에서 `privateServiceLinkId`를 리소스 관리 프라이빗 링크의 ID로 설정합니다. `groupIds`는 반드시 `ResourceManagement`를 포함해야 합니다. 프라이빗 엔드포인트의 위치는 서브넷의 위치와 동일해야 합니다.

```json
{
  "location": "westus2",
  "properties": {
    "privateLinkServiceConnections": [
      {
        "name": "{connection-name}",
        "properties": {
           "privateLinkServiceId": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{name}",
           "groupIds": [
              "ResourceManagement"
           ]
         }
      }
    ],
    "subnet": {
      "id": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
    }
  }
}
```

다음 단계는 자동 또는 수동 승인을 사용하는지 여부에 따라 달라집니다. 승인에 대한 자세한 정보는 [승인 워크플로를 사용하여 프라이빗 링크 리소스에 액세스](../../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)를 참조하세요.

응답은 승인 상태를 포함합니다.

```json
"privateLinkServiceConnectionState": {
    "actionsRequired": "None",
    "description": "",
    "status": "Approved"
},
```

요청이 자동으로 승인되면 다음 섹션을 계속 진행할 수 있습니다. 요청에 수동 승인이 필요한 경우 네트워크 관리자가 프라이빗 엔드포인트 연결을 승인할 때까지 기다립니다.

## <a name="next-steps"></a>다음 단계

프라이빗 링크에 대해 자세히 알아보려면 [Azure Private Link](../../private-link/index.yml)를 참조하세요.