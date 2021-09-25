---
title: 공유 이미지를 사용하여 VM 추가
description: Azure DevTest Labs에서 연결된 공유 이미지 갤러리의 이미지를 사용하여 VM(가상 머신)을 추가하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 7107fd22142f00e422e0f302520962537d65b10e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621814"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>연결된 공유 이미지 갤러리의 이미지를 사용하여 VM 추가
Azure DevTest Labs를 사용하면 공유 이미지 갤러리를 랩에 연결한 후 갤러리의 이미지를 랩에서 만드는 VM의 베이스로 사용할 수 있습니다. 공유 이미지 갤러리를 랩에 연결하는 방법을 알아보려면 [공유 이미지 갤러리 구성](configure-shared-image-gallery.md)을 참조하세요. 이 문서에서는 연결된 공유 이미지 갤러리의 이미지를 베이스로 사용하여 VM을 랩에 추가하는 방법을 보여 줍니다. 

## <a name="azure-portal"></a>Azure portal
이 섹션에서는 Azure Portal을 사용하여 연결된 공유 이미지 갤러리의 이미지를 기반으로 VM을 랩에 추가하는 방법을 알아봅니다. 이 섹션은 Azure Portal을 사용하여 VM을 만드는 방법에 대한 자세한 단계별 지침은 제공하지 않습니다. VM을 만드는 방법에 대한 자세한 내용은 [VM 만들기 - Azure Portal](devtest-lab-add-vm.md)을 참조하세요. 이 섹션에는 연결된 공유 이미지 갤러리의 이미지를 선택하고 사용할 이미지의 버전을 선택하는 단계만 강조되어 있습니다. 

VM을 랩에 추가하는 경우 연결된 공유 이미지 갤러리의 이미지를 베이스 이미지로 선택할 수 있습니다. 

![베이스로 사용할 공유 이미지 선택](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

그런 다음, **랩 리소스 만들기** 페이지의 **고급 설정** 탭에서 베이스 이미지로 사용할 이미지의 버전을 선택할 수 있습니다.

![이미지 버전 선택](./media/add-vm-use-shared-image/select-version-shared-image.png)

VM이 만들어진 후에는 다른 버전의 이미지를 사용하도록 전환할 수 있습니다. 

## <a name="resource-manager-template"></a>Resource Manager 템플릿
Azure Resource Manager 템플릿에서 공유 이미지 갤러리의 이미지를 사용하여 가상 머신을 만드는 경우 **속성** 섹션에서 **sharedImageId** 값을 지정합니다. 다음 예제를 참조하십시오. 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

전체 Resource Manager 템플릿 예제는 GitHub 리포지토리에서 [공유 이미지 갤러리의 이미지를 사용하여 가상 머신 만들기](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) 샘플을 참조하세요. 

## <a name="rest-api"></a>REST API

1. 먼저 공유 이미지 갤러리에서 이미지 ID를 가져와야 합니다. 한 가지 방법은 다음 GET 명령을 사용하여 연결된 공유 이미지 갤러리의 모든 이미지를 나열하는 것입니다. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. `properties.SharedImageId`에 대한 이전 호출에서 받은 공유 이미지 ID를 전달하여 가상 머신에서 PUT 메서드를 호출합니다.

## <a name="next-steps"></a>다음 단계
공유 이미지 갤러리를 랩에 연결하고 구성하는 방법을 알아보려면 [공유 이미지 갤러리 구성](configure-shared-image-gallery.md)을 참조하세요.