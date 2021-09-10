---
title: 리소스 관리 프라이빗 링크 관리
description: REST API를 사용하여 기존 리소스 관리 프라이빗 링크 관리
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 376fa600cce4479a0ed2c04e9d3d7b3fbeb9aeaa
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227405"
---
# <a name="manage-resource-management-private-links-with-rest-api"></a>REST API를 사용하여 리소스 관리 프라이빗 링크 관리

이 문서에서는 기존 리소스 관리 프라이빗 링크를 사용하여 작업하는 방법을 설명합니다. 기존 리소스를 가져오고 삭제하기 위한 REST API 작업을 살펴봅니다.

리소스 관리 프라이빗 링크를 만들어야 하는 경우 [포털을 사용하여 Azure 리소스 관리를 위한 프라이빗 링크 만들기](create-private-link-access-portal.md) 또는 [REST API를 사용하여 Azure 리소스 관리를 위한 프라이빗 링크 만들기](create-private-link-access-rest.md)를 참조하세요.

## <a name="resource-management-private-links"></a>리소스 관리 프라이빗 링크

**특정** 리소스 관리 프라이빗 링크를 가져오려면 다음의 요청을 보냅니다.

```http
GET
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01 
```

작업은 다음을 반환합니다.

```json
{
  "properties": {
    "privateEndpointConnections": []
  },
  "id": {rmplResourceId},
  "name": {rmplName},
  "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
  "location": {region}
}
```

구독의 **모든** 리소스 관리 프라이빗 링크를 가져오려면 다음을 사용합니다.

```http
GET
https://management.azure.com/subscriptions/{subscriptionID}/providers/Microsoft.Authorization/resourceManagementPrivateLinks?api-version=2020-05-01
```

작업은 다음을 반환합니다.

```json
[
  {
    "properties": {
      "privateEndpointConnections": []
    },
    "id": {rmplResourceId},
    "name": {rmplName},
    "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
    "location": {region}
  },
  {
    "properties": {
      "privateEndpointConnections": []
    },
    "id": {rmplResourceId},
    "name": {rmplName},
    "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
    "location": {region}
  }
]
```

특정 리소스 관리 프라이빗 링크를 **삭제** 하려면 다음을 사용합니다.

```http
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01
```

작업은 다음을 반환합니다. `Status 200 OK`.

## <a name="private-link-association"></a>프라이빗 링크 연결

관리 그룹에 대한 **특정** 프라이빗 링크 연결을 가져오려면 다음을 사용합니다.

```http
GET
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupID}/providers/Microsoft.Authorization/privateLinkAssociations?api-version=2020-05-01 
```

작업은 다음을 반환합니다.

```json
{
  "value": [
    {
      "properties": {
        "privateLink": {rmplResourceID},
        "tenantId": {tenantId},
        "scope": "/providers/Microsoft.Management/managementGroups/{managementGroupId}"
      },
      "id": {plaResourceId},
      "type": "Microsoft.Authorization/privateLinkAssociations",
      "name": {plaName}
    }
  ]
}
```

프라이빗 링크 연결을 **삭제** 하려면 다음을 사용합니다.

```http
DELETE 
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupID}/providers/Microsoft.Authorization/privateLinkAssociations/{plaID}?api-version=2020-05-01
```

작업은 다음을 반환합니다. `Status 200 OK`.

## <a name="private-endpoints"></a>프라이빗 엔드포인트

구독의 **모든** 프라이빗 엔드포인트 가져오려면 다음을 사용합니다.

```http
GET 
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/privateEndpoints?api-version=2020-04-01
```

작업은 다음을 반환합니다.

```json
{
  "value": [
    {
      "name": {privateEndpointName},
      "id": {privateEndpointResourceId},
      "etag": {etag},
      "type": "Microsoft.Network/privateEndpoints",
      "location": {region},
      "properties": {
        "provisioningState": "Updating",
        "resourceGuid": {GUID},
        "privateLinkServiceConnections": [
          {
            "name": {connectionName},
            "id": {connectionResourceId},
            "etag": {etag},
            "properties": {
              "provisioningState": "Succeeded",
              "privateLinkServiceId": {rmplResourceId},
              "groupIds": [
                "ResourceManagement"
              ],
              "privateLinkServiceConnectionState": {
                "status": "Approved",
                "description": "",
                "actionsRequired": "None"
              }
            },
            "type": "Microsoft.Network/privateEndpoints/privateLinkServiceConnections"
          }
        ],
        "manualPrivateLinkServiceConnections": [],
        "subnet": {
          "id": {subnetResourceId}
        },
        "networkInterfaces": [
          {
            "id": {networkInterfaceResourceId}
          }
        ],
        "customDnsConfigs": [
          {
            "fqdn": "management.azure.com",
            "ipAddresses": [
              "10.0.0.4"
            ]
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

* 프라이빗 링크에 대해 자세히 알아보려면 [Azure Private Link](../../private-link/index.yml)를 참조하세요.
* 리소스 관리 프라이빗 링크를 만들려면 [포털을 사용하여 Azure 리소스 관리를 위한 프라이빗 링크 만들기](create-private-link-access-portal.md) 또는 [REST API를 사용하여 Azure 리소스 관리를 위한 프라이빗 링크 만들기](create-private-link-access-rest.md)를 참조하세요.