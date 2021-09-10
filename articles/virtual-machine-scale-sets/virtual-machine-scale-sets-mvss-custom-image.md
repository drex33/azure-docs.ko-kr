---
title: Azure 확장 집합 템플릿에서 사용자 지정 이미지 참조
description: 기존 Azure Virtual Machine Scale Set 템플릿에 사용자 지정 이미지를 추가하는 방법 알아보기
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.date: 04/26/2018
ms.reviewer: mimckitt
ms.openlocfilehash: 6508935cc104f2eac46f8e61b50850c45e6856cc
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697396"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Azure 확장 집합 템플릿에 사용자 지정 이미지 추가

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 단일 확장 집합

이 문서에서는 사용자 지정 이미지에서 배포할 [기본 확장 집합 템플릿](virtual-machine-scale-sets-mvss-start.md)을 수정하는 방법을 보여 줍니다.

## <a name="change-the-template-definition"></a>템플릿 정의 변경
[이전 문서](virtual-machine-scale-sets-mvss-start.md)에서 기본 확장 집합 템플릿을 만들었습니다. 이제 이전 템플릿을 사용하고 수정하여 사용자 지정 이미지에서 확장 집합을 배포하는 템플릿을 만듭니다.  

### <a name="creating-a-managed-disk-image"></a>Managed Disk 이미지 만들기

사용자 지정 Managed Disk 이미지가 이미 있는 경우(`Microsoft.Compute/images` 형식 리소스) 이 섹션을 건너뛰어도 됩니다.

먼저 배포를 시작할 사용자 지정 이미지를 포함하는 Azure Storage의 일반화된 Blob의 URI에 해당하는 `sourceImageVhdUri` 매개 변수를 추가합니다.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

다음으로 `sourceImageVhdUri` URI에 있는 일반화된 Blob을 기준으로 하는 Managed Disk 이미지에 해당하는 `Microsoft.Compute/images` 형식의 리소스를 추가합니다. 이 이미지는 사용되는 확장 집합과 동일한 하위 지역에 있어야 합니다. 이미지의 속성에서 OS 운영 체제, blob의 위치(`sourceImageVhdUri` 매개 변수) 및 스토리지 계정 유형을 지정합니다.

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

확장 집합 리소스에서 사용자 지정 이미지를 참조하는 추가 `dependsOn` 절을 추가하여 확장 집합이 해당 이미지에서의 배포를 시도하기 전에 이미지가 만들어지도록 합니다.

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Managed Disk 이미지를 사용하도록 확장 집합 속성 설정

확장 집합 `storageProfile`의 `imageReference`에서, 플랫폼 이미지의 게시자, 제품, SKU 및 버전을 지정하는 대신, `Microsoft.Compute/images` 리소스의 `id`를 지정합니다.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

이 예제에서는 `resourceId` 함수를 사용하여 같은 템플릿에서 만들어진 이미지의 리소스 ID를 가져옵니다. Managed Disk 이미지를 미리 만든 경우 대신 해당 이미지의 ID를 제공해야 합니다. 이 ID는 `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>` 형식이어야 합니다.


## <a name="next-steps"></a>다음 단계

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
