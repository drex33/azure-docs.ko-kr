---
title: 공유 이미지 갤러리 구성
description: 사용자가 랩 리소스를 만드는 동안 공유 위치에서 이미지에 액세스할 수 있도록 하는 Azure DevTest Labs에서 공유 이미지 갤러리를 구성하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: e6275b8903df7e815e763b04198265060943e124
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634846"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Azure DevTest Labs에서 공유 이미지 갤러리 구성
이제 DevTest Labs는 [Shared Image Gallery](../virtual-machines/shared-image-galleries.md) 기능을 지원합니다. 랩 사용자가 랩 리소스를 만드는 동안 공유 위치에서 이미지에 액세스할 수 있습니다. 또한 사용자 지정 관리 VM 이미지를 중심으로 구조와 조직을 구축할 수도 있습니다. Shared Image Gallery 기능 지원 사항:

- 이미지의 관리형 전체 복제본
- 보다 쉬운 관리를 위한 이미지 버전 관리 및 그룹화
- 가용성 영역을 지원하는 지역의 ZRS(영역 중복 스토리지) 계정으로 이미지의 가용성을 높일 수 있습니다. ZRS는 영역 장애 발생 시 보다 나은 복원력을 제공합니다.
- Azure RBAC(역할 기반 액세스 제어)를 사용하여 구독 간 및 테넌트 간에도 공유합니다.

자세한 내용은 [Shared Image Gallery](../virtual-machines/shared-image-galleries.md)를 참조하세요. 
 
유지 관리해야 하는 관리되는 이미지가 많고, 회사 전체에서 사용 가능하도록 하려면 공유 이미지 갤러리를 이미지를 쉽게 업데이트하고 공유할 수 있게 해주는 리포지토리로 사용할 수 있습니다. 랩 소유자는 기존의 공유 이미지 갤러리를 랩에 연결할 수 있습니다. 이 갤러리가 연결되면 랩 소유자가 이러한 최신 이미지에서 머신을 만들 수 있습니다. 이 기능의 주요 이점은 이제 DevTest Labs에서 랩, 구독 및 지역 간에 공유 이미지의 혜택을 활용할 수 있다는 점입니다. 

> [!NOTE]
> Shared Image Gallery 서비스와의 연결 비용에 대해 알아보려면 [Shared Image Gallery에 대한 청구](../virtual-machines/shared-image-galleries.md#billing)를 참조하세요.

## <a name="considerations"></a>고려 사항
- 한 번에 하나의 공유 이미지 갤러리만 랩에 연결할 수 있습니다. 다른 갤러리를 연결하려면 기존 갤러리를 분리하고 다른 갤러리를 연결해야 합니다. 
- DevTest Labs는 현재 랩을 통해 갤러리에 이미지를 업로드하는 기능을 지원하지 않습니다. 
- 공유 이미지 갤러리 이미지를 사용하여 가상 머신을 만들 때 DevTest Labs는 항상 이 이미지의 최신 게시 버전을 사용합니다. 하지만 이미지에 여러 버전이 있는 경우 사용자는 가상 머신이 만들어지는 동안 고급 설정 탭으로 이동하여 이전 버전에서 머신을 만들도록 선택할 수 있습니다.  
- DevTest Labs는 공유 이미지 갤러리가 랩이 있는 영역에 이미지를 복제하도록 자동으로 시도하지만 항상 가능한 것은 아닙니다. 사용자가 이러한 이미지에서 VM을 생성하는 데 문제가 발생하지 않도록 하려면 이미지가 랩 영역으로 이미 복제되었는지 확인합니다.

## <a name="use-azure-portal"></a>Azure Portal 사용
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **모든 서비스** 를 선택합니다.
1. 목록에서 **DevTest Labs** 를 선택합니다.
1. 랩 목록에서 **랩** 을 선택합니다.
1. 왼쪽 메뉴의 **설정** 섹션에서 **구성 및 정책** 을 선택합니다.
1. 왼쪽 메뉴의 **가상 머신 베이스** 에서 **공유 이미지 갤러리** 를 선택합니다.

    ![공유 이미지 갤러리 메뉴](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. **연결** 단추를 클릭하고 드롭다운 목록에서 갤러리를 선택하여 기존 공유 이미지 갤러리를 랩에 연결합니다.

    ![연결](./media/configure-shared-image-gallery/attach-options.png)
1. 이미지 갤러리가 연결된 후 연결된 갤러리를 선택하여 이동합니다. VM 생성을 위해 공유 이미지를 **사용하거나 사용하지 않도록 설정** 하도록 갤러리를 구성합니다. 목록에서 이미지 갤러리를 선택하여 구성합니다. 

    기본적으로 **모든 이미지를 가상 머신 베이스로 사용하도록 허용** 은 **예** 로 설정되어 있습니다. 즉, 새로운 랩 VM 생성 시 랩 사용자가 연결된 공유 이미지 갤러리에서 사용할 수 있는 모든 이미지를 사용할 수 있습니다. 특정 이미지에 대한 액세스를 제한해야 하는 경우 **모든 이미지를 가상 머신 베이스로 사용하도록 허용** 을 **아니요** 로 변경하고 VM 생성 시 허용하려는 이미지를 선택한 다음 **저장** 단추를 선택합니다.

    :::image type="content" source="./media/configure-shared-image-gallery/enable-disable.png" alt-text="이미지를 사용 또는 사용하지 않도록 설정":::

    > [!NOTE]
    > 공유 이미지 갤러리에서는 일반 및 특수 이미지를 모두 지원합니다. 
1. 따라서 랩 사용자는 **+추가** 를 클릭하고 **베이스 선택** 페이지에서 이미지를 찾아 사용 가능한 이미지를 사용하여 가상 머신을 만들 수 있습니다.

    ![랩 사용자](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>공유 이미지 갤러리를 랩에 연결
Azure Resource Manager 템플릿을 사용하여 공유 이미지 갤러리를 앱에 연결하는 경우, 다음 예제와 같이 Resource Manager 템플릿의 리소스 섹션에서 이를 추가해야 합니다.

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

전체 Resource Manager 템플릿 예제는 당사 공개 GitHub 리포지토리, [랩을 만드는 동안 공유 이미지 갤러리 구성](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)에서 Resource Manager 템플릿을 참조하세요.

## <a name="use-rest-api"></a>REST API 사용

### <a name="get-a-list-of-labs"></a>랩 목록 가져오기 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs?api-version= 2018-10-15-preview
```

### <a name="get-the-list-of-shared-image-galleries-associated-with-a-lab"></a>랩에 연결된 공유 이미지 갤러리 목록 가져오기

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries?api-version= 2018-10-15-preview
   ```

### <a name="create-or-update-shared-image-gallery"></a>공유 이미지 갤러리 만들기 또는 업데이트

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="list-images-in-a-shared-image-gallery"></a>공유 이미지 갤러리에 이미지 나열

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```



## <a name="next-steps"></a>다음 단계
연결된 공유 이미지 갤러리에서 한 이미지를 사용하여 VM 생성 시 다음 문서 참조: [갤러리에서 공유 이미지를 사용하여 VM 만들기](add-vm-use-shared-image.md)