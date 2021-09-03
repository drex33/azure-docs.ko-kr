---
title: Azure Private Link에 대한 Azure RBAC 권한
description: 프라이빗 엔드포인트 및 프라이빗 링크 서비스를 배포하는 데 필요한 Azure RBAC 권한에 대한 학습을 시작하세요.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 5/25/2021
ms.custom: template-concept
ms.openlocfilehash: 50b648c5419b995f9c808288895af49b12f29b86
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537686"
---
# <a name="azure-rbac-permissions-for-azure-private-link"></a>Azure Private Link에 대한 Azure RBAC 권한

클라우드 리소스에 대한 액세스 관리는 모든 조직에서 중요한 기능입니다. Azure RBAC(Azure 역할 기반 액세스 제어)는 Azure 리소스의 액세스 및 작업을 관리합니다.

프라이빗 엔드포인트 또는 프라이빗 링크 서비스를 배포하려면 사용자가 다음과 같은 기본 제공 역할을 할당해야 합니다. 

* [소유자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#owner)
* [기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#contributor)
* [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#network-contributor)

다음 섹션에 설명된 권한으로 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json)을 만들어 더 세분화된 액세스 권한을 제공할 수 있습니다.

> [!IMPORTANT]
> 이 문서에는 프라이빗 엔드포인트 또는 프라이빗 링크 서비스를 만들기 위한 특정 권한이 나열되어 있습니다. Azure SQL에 대한 Microsoft.SQL 기여자 역할과 같이 프라이빗 링크를 통해 액세스 권한을 부여하려는 서비스와 관련된 특정 권한을 추가해야 합니다. 기본 제공 역할에 대한 자세한 내용은 [역할 기반 액세스 제어](../role-based-access-control/built-in-roles.md)를 참조하세요.

Microsoft.Network 및 배포 중인 특정 리소스 공급자(예: Microsoft.Sql)는 구독 수준에서 등록해야 합니다.

![이미지](https://user-images.githubusercontent.com/20302679/129105527-b946eee9-038a-46ef-b446-be371eb23ca9.png)

## <a name="private-endpoint"></a>프라이빗 엔드포인트

이 섹션에는 프라이빗 엔드포인트를 배포하는 데 필요한 세분화된 권한이 나열되어 있습니다.

| 작업                                                              | 설명                                                                      |
| ---------                                                           | -------------                                                                 |
| Microsoft.Resources/deployments/*                                   | 배포를 만들고 관리합니다.                                                |
| Microsoft.Resources/subscriptions/resourcegroups/resources/read     | 리소스 그룹에 대한 리소스를 읽습니다.                                     |
| Microsoft.Network/virtualNetworks/read                              | 가상 네트워크 정의를 읽습니다.                                            |
| Microsoft.Network/virtualNetworks/subnets/read                      | 가상 네트워크 서브넷 정의를 읽습니다.                                      |
| Microsoft.Network/virtualNetworks/subnets/write                     | 가상 네트워크 서브넷을 만들거나 기존 가상 네트워크 서브넷을 업데이트합니다.|
| Microsoft.Network/virtualNetworks/subnets/join/action               | 가상 네트워크를 연결합니다.                                                       |
| Microsoft.Network/privateEndpoints/read                             | 프라이빗 엔드포인트 리소스를 읽습니다.                                             |
| Microsoft.Network/privateEndpoints/write                            | 새 프라이빗 엔드포인트를 만들거나 기존 프라이빗 엔드포인트를 업데이트합니다.       |
| Microsoft.Network/locations/availablePrivateEndpointTypes/read      | 사용 가능한 프라이빗 엔드포인트 리소스를 읽습니다.                                     |

위 권한의 JSON 형식은 다음과 같습니다. 사용자 고유의 roleName, description 및 assignableScopes를 입력합니다.

```JSON
{
 "properties": {
   "roleName": "Role Name",
   "description": "Description",
   "assignableScopes": [
     "/subscriptions/SubscriptionID/resourceGroups/ResourceGroupName"
   ],
   "permissions": [
     {
       "actions": [
         "Microsoft.Resources/deployments/*",
         "Microsoft.Resources/subscriptions/resourceGroups/read",
         "Microsoft.Network/virtualNetworks/read",
         "Microsoft.Network/virtualNetworks/subnets/read",
         "Microsoft.Network/virtualNetworks/subnets/write",
         "Microsoft.Network/virtualNetworks/subnets/join/action",
         "Microsoft.Network/privateEndpoints/read",
         "Microsoft.Network/privateEndpoints/write",
         "Microsoft.Network/locations/availablePrivateEndpointTypes/read"
       ],
       "notActions": [],
       "dataActions": [],
       "notDataActions": []
     }
   ]
 }
}
```

## <a name="private-link-service"></a>Private Link 서비스

이 섹션에는 프라이빗 링크 서비스를 배포하는 데 필요한 세분화된 권한이 나열되어 있습니다.

| 작업 | 설명   |
| --------- | ------------- |
| Microsoft.Resources/deployments/*                                   | 배포를 만들고 관리합니다.                                                |
| Microsoft.Resources/subscriptions/resourcegroups/resources/read     | 리소스 그룹에 대한 리소스를 읽습니다.                                     |
| Microsoft.Network/virtualNetworks/read                              | 가상 네트워크 정의를 읽습니다.                                            |
| Microsoft.Network/virtualNetworks/subnets/read                      | 가상 네트워크 서브넷 정의를 읽습니다.                                      |
| Microsoft.Network/virtualNetworks/subnets/write                     | 가상 네트워크 서브넷을 만들거나 기존 가상 네트워크 서브넷을 업데이트합니다.|
| Microsoft.Network/privateLinkServices/read | 프라이빗 링크 서비스 리소스를 읽습니다.|
| Microsoft.Network/privateLinkServices/write | 새로운 프라이빗 링크 서비스를 만들거나 기존 프라이빗 링크 서비스를 업데이트합니다.|
| Microsoft.Network/privateLinkServices/privateEndpointConnections/read | 프라이빗 엔드포인트 연결 정의를 읽습니다. |
| Microsoft.Network/privateLinkServices/privateEndpointConnections/write | 새 프라이빗 엔드포인트 연결을 만들거나 기존 프라이빗 엔드포인트 연결을 업데이트합니다.|
| Microsoft.Network/networkSecurityGroups/join/action | 네트워크 보안 그룹을 연결합니다. |
| Microsoft.Network/loadBalancers/read | 부하 분산 장치 정의를 읽습니다. |
| Microsoft.Network/loadBalancers/write | 부하 분산 장치를 만들거나 기존 부하 분산 장치를 업데이트합니다. |

```JSON
{
  "properties": {
    "roleName": "Role Name",
    "description": "Description",
    "assignableScopes": [
      "/subscriptions/SubscriptionID/resourceGroups/ResourceGroupName"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Network/virtualNetworks/read",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/write",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/privateLinkServices/read",
          "Microsoft.Network/privateLinkServices/write",
          "Microsoft.Network/privateLinkServices/privateEndpointConnections/read",
          "Microsoft.Network/privateLinkServices/privateEndpointConnections/write",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Network/loadBalancers/read",
          "Microsoft.Network/loadBalancers/write"
        ],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

## <a name="next-steps"></a>다음 단계

Azure Private Link의 프라이빗 엔드포인트 및 프라이빗 링크 서비스에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 프라이빗 엔드포인트란?](private-endpoint-overview.md)
- [Azure Private Link 서비스는 무엇입니까?](private-link-service-overview.md)
