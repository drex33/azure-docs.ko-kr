---
title: Azure 확장 집합 템플릿에서 기존 가상 네트워크 참조
description: 기존 Azure Virtual Machine Scale Set 템플릿에 가상 네트워크를 추가하는 방법 알아보기
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 03/30/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 13e8cf6b95394cd879c1e86e1e0b6dd7dfac7ccd
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692891"
---
# <a name="reference-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Azure 확장 집합 템플릿에서 기존 가상 네트워크 참조

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 단일 확장 집합

이 문서에서는 [기본 확장 집합 템플릿](virtual-machine-scale-sets-mvss-start.md)을 수정하여 새 가상 네트워크를 만드는 대신 기존 가상 네트워크에 배포하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이전 문서에서는 [기본 확장 집합 템플릿](virtual-machine-scale-sets-mvss-start.md)을 만들었습니다. 기존 가상 네트워크에 확장 집합을 배포하는 템플릿을 만들기 위해 수정할 수 있도록 이전 템플릿이 필요합니다.

## <a name="identify-subnet"></a>서브넷 식별

먼저 `subnetId` 매개 변수를 추가합니다. 이 문자열은 확장 집합 구성에 전달되어 확장 집합에서 미리 만든 서브넷을 식별하여 가상 머신을 배포할 수 있게 합니다. 이 문자열은 다음 형식이어야 합니다.

`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`


예를 들어 확장 집합을 이름 `myvnet`, `mysubnet` 서브넷, `myrg` 리소스 그룹 및 `00000000-0000-0000-0000-000000000000` 구독인 기존 가상 네트워크에 배포하려면 subnetId는 다음과 같습니다. 

`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
      },
      "adminPassword": {
        "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
      }
    },
```

## <a name="delete-extra-virtual-network-resource"></a>추가 가상 네트워크 리소스 삭제

다음으로 기존 가상 네트워크를 사용하고 있고 새 가상 네트워크를 배포할 필요가 없기 때문에 `resources` 배열에서 가상 네트워크 리소스를 삭제합니다.

```diff
    "variables": {},
    "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```
## <a name="remove-dependency-clause"></a>종속성 절 제거

템플릿을 배포하기 전에 가상 네트워크가 이미 있으므로 확장 집합에서 가상 네트워크로 `dependsOn` 절을 지정할 필요가 없습니다. 다음 줄을 삭제합니다.

```diff
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets",
        "name": "myScaleSet",
        "location": "[resourceGroup().location]",
        "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
        "sku": {
          "name": "Standard_A1",
          "capacity": 2
```

## <a name="pass-subnet-parameter"></a>전달 서브넷 매개 변수

마지막으로 동일한 배포에서 Vnet의 ID를 얻기 위해 실행 가능한 기본 확장 집합 템플릿에서 수행하는 `resourceId`를 사용하는 대신 사용자가 설정한 `subnetId` 매개 변수를 전달합니다.

```diff
                        "name": "myIpConfig",
                        "properties": {
                          "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                          }
                        }
                      }
```


## <a name="next-steps"></a>다음 단계

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
