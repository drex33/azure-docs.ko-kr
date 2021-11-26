---
title: 여러 지역에서 서비스 리소스를 이동하는 방법
titleSuffix: Azure Cognitive Search
description: 이 문서에서는 Azure Cognitive Search 리소스를 Azure 클라우드에 있는 한 지역에서 다른 지역으로 이동하는 방법을 보여 줍니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 10/06/2021
ms.openlocfilehash: a5cab1dc5420cd7d06c53199a7d522978315b1d3
ms.sourcegitcommit: fc912bf0540585f44c09c6d63728c05c5dda558b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133128515"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Azure Cognitive Search 서비스를 다른 Azure 지역으로 이동

때때로 고객이 검색 서비스를 다른 지역으로 이동하는 것에 대해 문의합니다. 현재는 해당 작업에 도움이 되는 기본 제공 메커니즘 또는 도구가 없지만이 문서는 다른 지역의 새 검색 서비스에서 인덱스 및 기타 개체를 다시 만드는 수동 단계를 이해 하는 데 도움이 될 수 있습니다.

> [!NOTE]
> Azure Portal에서 모든 서비스에는 **템플릿 내보내기** 명령이 있습니다. Azure Cognitive Search의 경우 이 명령은 서비스의 기본 정의(이름, 위치, 계층, 복제본 및 파티션 수)를 생성하지만 서비스의 콘텐츠를 인식하지 않으며 키, 역할 또는 로그를 전달하지 않습니다. 명령이 존재하지만 검색 서비스를 이동하는 데 사용하지 않는 것이 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항

+ 계정에서 사용하는 서비스 및 기능이 대상 지역에서 지원되는지 확인합니다.

+ 미리 보기 기능의 경우 구독이 대상 지역에 승인되어 있는지 확인합니다.

## <a name="prepare-and-move"></a>준비 및 이동

1. Azure Cognitive Search 이상의 서비스를 이동해야 하는 경우 종속성 및 관련 서비스를 식별하여 서비스 재배치의 전체 영향을 이해합니다.

   Azure Storage는 로깅, 지식 저장소 만들기에 사용되며 AI 보강 및 인덱싱에 일반적으로 사용되는 외부 데이터 원본입니다. Cognitive Services는 AI 보강의 종속성입니다. AI 보강을 사용하는 경우 Cognitive Services와 검색 서비스가 모두 동일한 지역에 있어야 합니다.

1. 이동할 항목(인덱스, 동의어 맵, 인덱서, 데이터 원본, 기술 세트)을 알 수 있도록 서비스에 있는 모든 개체의 인벤토리를 만듭니다. 로깅을 사용하도록 설정한 경우 기록 레코드에 필요할 수 있는 모든 보고서를 만들고 보관합니다.

1. 새 지역의 가격 책정 및 가용성을 확인하여 새 지역의 Azure Cognitive Search 및 관련 서비스의 가용성을 확인합니다. 대부분의 기능은 모든 지역에서 사용할 수 있지만 일부 미리 보기 기능은 가용성이 제한되어 있습니다.

1. 새 지역에서 서비스를 만들고 소스 코드에서 기존 인덱스, 동의어 맵, 인덱서, 데이터 원본, 기술 세트를 다시 게시합니다. 서비스 이름은 고유해야 하므로 기존 이름을 다시 사용할 수 없습니다. Cognitive Services에 대한 연결이 동일한 지역 요구 사항과 관련하여 여전히 유효한지 각 기술 세트를 확인합니다. 또한 지식 저장소를 만든 경우 다른 서비스를 사용하면 Azure Storage에 대한 연결 문자열을 확인합니다.

1. 인덱스와 지식 저장소(해당하는 경우)를 다시 로드합니다. 애플리케이션 코드를 사용하여 JSON 데이터를 인덱스로 푸시하거나 인덱서를 다시 실행하여 외부 원본에서 문서를 풀합니다. 

1. 로깅을 사용하고 이를 사용하는 경우 보안 역할을 다시 만듭니다.

1. 새 서비스 이름 및 API 키를 사용하고 모든 애플리케이션을 테스트하도록 클라이언트 애플리케이션 및 테스트 도구 모음을 업데이트합니다.

## <a name="discard-or-clean-up"></a>삭제 또는 정리

새 서비스가 완전히 테스트되고 작동하면 이전 서비스를 삭제합니다. 서비스를 삭제하면 이 서비스와 연결된 모든 콘텐츠가 자동으로 삭제됩니다.

## <a name="next-steps"></a>다음 단계

다음 링크는 위에 설명된 단계를 완료할 때 자세한 정보를 찾는 데 도움이 됩니다.

+ [Azure Cognitive Search 가격 책정 및 지역](https://azure.microsoft.com/pricing/details/search/)
+ [계층 선택](search-sku-tier.md)
+ [검색 서비스 만들기](search-create-service-portal.md)
+ [검색 문서 로드](search-what-is-data-import.md)
+ [로깅 사용](monitor-azure-cognitive-search.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is allowlisted for the target region. For more information about preview features, see [knowledge stores](./knowledge-store-concept-intro.md), [incremental enrichment](./cognitive-search-incremental-indexing-conceptual.md), and [private endpoint](./service-create-private-endpoint.md).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](../storage/common/storage-account-move.md?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](../storage/common/storage-account-move.md?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](./search-get-started-portal.md)

[Create a skillset](./cognitive-search-quickstart-blob.md)

[Create a knowledge store](./knowledge-store-create-portal.md) -->