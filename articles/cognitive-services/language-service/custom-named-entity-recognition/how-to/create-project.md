---
title: 사용자 지정 NER에서 Azure 리소스 사용
titleSuffix: Azure Cognitive Services
description: 사용자 지정 NER를 사용 하 여 Azure 리소스를 사용 하는 단계에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, references_regions, ignite-fall-2021
ms.openlocfilehash: c7dfe80162ab406c6dde32ad54ff251e001f5416
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027682"
---
# <a name="how-to-create-custom-ner-projects"></a>사용자 지정 NER 프로젝트를 만드는 방법

사용자 지정 NER 사용을 시작 하기 전에 다음과 같은 몇 가지 항목이 필요 합니다.

* Azure 언어 리소스 
* `.txt`텍스트를 분류 하기 위해 AI 모델을 학습 하는 데 사용 되는 파일을 업로드 하는 Azure storage 계정

이 문서에서는 사용자 지정 NER 사용을 위한 요구 사항을 준비 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독- [무료로 무료로 만드세요](https://azure.microsoft.com/free/cognitive-services).
데이터에 사용 하는 [프로젝트 스키마](design-schema.md) 를 이해 해야 합니다.

이 문서에서는 사용자 지정 텍스트 분류를 사용 하기 위한 요구 사항을 준비 하는 방법을 알아봅니다.

## <a name="azure-resources"></a>: Azure 리소스

사용자 지정 NER 사용을 시작 하기 전에 Azure 언어 리소스가 필요 합니다. Azure Portal에서 새로 만들기 위해 [빠른](../quickstart.md) 시작의 단계를 수행 하는 것이 좋습니다. Azure Portal에서 리소스를 만들면 Azure storage 계정을 동시에 만들 수 있으며 필요한 모든 권한이 미리 구성 되어 있습니다. 기존 리소스를 사용 하는 방법에 대해 알아보고, 사용자 지정 NER에서 작동 하도록 구성 하는 방법에 대해서도 문서에서 자세히 알아볼 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

[!INCLUDE [create a new resource from the Azure portal](../../custom-classification/includes/resource-creation-azure-portal.md)]

# <a name="language-studio"></a>[Language Studio](#tab/studio)

### <a name="create-a-new-resource-from-language-studio"></a>언어 스튜디오에서 새 리소스 만들기

처음으로 로그인 하는 경우 언어 [스튜디오](https://aka.ms/languageStudio) 에서 언어 리소스를 선택 하거나 새로 만들 수 있는 창이 표시 됩니다. 오른쪽 위 모서리에서 설정 아이콘을 클릭 하 고 **리소스** 를 선택한 다음 **새 리소스 만들기** 를 클릭 하 여 리소스를 만들 수도 있습니다.

> [!IMPORTANT]
> * 사용자 지정 NER를 사용 하려면 **미국 서 부 2** 또는 표준 가격 책정 계층을 사용 하는 **유럽 서부** 언어리소스가 필요 합니다.
> * 리소스를 만들 때 **관리 id** 를 선택 해야 합니다. 

:::image type="content" source="../../media/create-new-resource-small.png" alt-text="언어 스튜디오에서 리소스 만들기 화면을 보여 주는 스크린샷" lightbox="../../media/create-new-resource.png":::

사용자 지정 NER을 사용 하려면 [Azure 저장소 계정이](/azure/storage/common/storage-account-create) 아직 없는 경우 만들어야 합니다. 

다음으로, 언어 리소스에 연결 하기 위해 저장소 계정에 대 한 [올바른 역할](#roles-for-your-storage-account) 을 할당 해야 합니다. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

### <a name="create-a-new-resource-with-the-azure-powershell"></a>Azure PowerShell를 사용 하 여 새 리소스 만들기

GitHub에서 호스트 되는 다음 CLI [템플릿](https://github.com/Azure-Samples/cognitive-services-sample-data-files) 및 [매개 변수](https://github.com/Azure-Samples/cognitive-services-sample-data-files) 파일을 사용 하 여 새 리소스 및 저장소 계정을 만들 수 있습니다.

매개 변수 파일에서 다음 값을 편집 합니다.

| 매개 변수 이름 | 값 설명 |
|--|--|
|`name`| 언어 리소스의 이름입니다.|
|`location`| 리소스가 호스트 되는 지역입니다. 사용자 지정 NER는 **미국 서 부 2** 및 **유럽 서부** 에서만 사용할 수 있습니다.|
|`sku`| 리소스의 가격 책정 계층입니다. 이 기능은 **S** 계층 에서만 작동 합니다.|
|`storageResourceName`| 저장소 계정의 이름|
|`storageLocation`| 저장소 계정이 호스트 되는 지역입니다.|
|`storageSkuType`| [저장소 계정의](/rest/api/storagerp/srp_sku_types)SKU입니다.|
|`storageResourceGroupName`| 저장소 계정의 리소스 그룹|
<!-- |builtInRoleType| 이 역할을 **"참가자"** 로 설정 합니다.| -->

다음 PowerShell 명령을 사용 하 여 사용자가 편집한 파일을 사용 하 여 Azure Resource Manager (ARM) 템플릿을 배포 합니다.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-arm-template> `
  -TemplateParameterFile <path-to-parameters-file>
```

템플릿 및 [매개 변수 파일](/azure/azure-resource-manager/templates/parameter-files?tabs=json) [배포](/azure/azure-resource-manager/templates/deploy-powershell#parameter-files) 에 대 한 자세한 내용은 ARM 템플릿 설명서를 참조 하세요.

--- 

## <a name="using-a-pre-existing-azure-resource"></a>기존 Azure 리소스 사용

이 리소스가 아래 요구 사항을 충족 하는 경우 기존 언어 리소스를 사용 하 여 사용자 지정 NER을 시작할 수 있습니다.

|요구 사항  |Description  |
|---------|---------|
|영역     | 기존 리소스가 두 개의 지원 되는 지역, **미국 서 부 2** 또는 **유럽 서부** 중 하나로 프로 비전 되었는지 확인 합니다. 그렇지 않은 경우에는 이러한 지역에서 새 리소스를 만들어야 합니다.        |
|가격 책정 계층     | 기존 리소스가 표준 가격 책정 계층에 있는지 확인 합니다. 이 가격 책정 계층만 지원 됩니다. 리소스에서이 가격 책정 계층을 사용 하지 않는 경우에는 새 리소스를 만들어야 합니다.        |
|관리 ID     | 리소스 관리 id 설정이 사용 하도록 설정 되어 있는지 확인 합니다. 그렇지 않으면 다음 섹션을 참조 하세요. |

사용자 지정 NER을 사용 하려면 [Azure 저장소 계정이](/azure/storage/common/storage-account-create) 아직 없는 경우 만들어야 합니다. 

다음으로, 언어 리소스에 연결 하기 위해 저장소 계정에 대 한 [올바른 역할](#roles-for-your-storage-account) 을 할당 해야 합니다. 

## <a name="roles-for-your-azure-language-resource"></a>Azure 언어 리소스에 대 한 역할

Azure 언어 리소스에 **소유자** 또는 **참가자** 역할이 할당 되어 있어야 합니다.

## <a name="enable-identity-management-for-your-resource"></a>리소스에 대해 id 관리를 사용 하도록 설정

언어 리소스에는 Azure Portal를 사용 하거나 언어 스튜디오에서 사용할 수 있는 id 관리가 있어야 합니다. [Language Studio](https://aka.ms/languageStudio)를 사용 하 여 활성화 하려면:
1. 화면의 오른쪽 위 모퉁이에 있는 설정 아이콘을 클릭 합니다.
2. **리소스** 를 선택합니다
3. Azure 리소스에 대 한 **관리 id** 를 선택 합니다.

## <a name="roles-for-your-storage-account"></a>저장소 계정에 대 한 역할

Azure blob storage 계정의 역할은 다음과 같아야 합니다.

* 리소스에는 저장소 계정에 대 한 **소유자** 또는 **참가자** 역할이 있습니다.
* 리소스에는 저장소 계정에 대 한 **Storage blob 데이터 소유자** 또는 **Storage blob 데이터 참가자** 역할이 있습니다.
* 리소스에는 저장소 계정에 대 한 **읽기 권한자** 역할이 있습니다.

저장소 계정에 적절 한 역할을 설정 하려면:

1. [Azure Portal](https://ms.portal.azure.com/)의 저장소 계정 페이지로 이동 합니다.
2. 왼쪽 탐색 메뉴에서 **Access Control (IAM)** 을 선택 합니다.
3. **추가** 를 선택 하 여 **역할 할당을 추가** 하 고 **소유자** 또는 **참가자** 역할을 선택 합니다. **Select** 필드에서 사용자 이름을 검색할 수 있습니다.

[!INCLUDE [Storage connection note](../../custom-classification/includes/storage-account-note.md)]

## <a name="prepare-training-data"></a>학습 데이터 준비

* 사용자 지정 NER 프로젝트를 만들기 위한 필수 구성 요소로, 학습 데이터를 저장소 계정의 blob 컨테이너에 업로드 해야 합니다. Azure에서 직접 또는 Azure Storage Explorer 도구를 사용 하 여 학습 파일을 만들고 업로드할 수 있습니다. Azure Storage Explorer 도구를 사용 하 여 더 짧은 시간에 더 많은 데이터를 업로드할 수 있습니다.

  * [Azure에서 파일 만들기 및 업로드](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)
  * [Azure Storage Explorer를 사용하여 파일 만들기 및 업로드](/azure/vs-azure-tools-storage-explorer-blobs)

* `.txt`만 사용할 수 있습니다. 사용자 지정 NER 파일입니다. 데이터 형식이 다른 경우 [Cognitive Services 언어 유틸리티 도구](https://aka.ms/CognitiveServicesLanguageUtilities) 를 사용 하 여 파일을 형식으로 구문 분석할 수 있습니다 `.txt` .

* 태그가 지정 된 데이터를 업로드 하거나 언어 스튜디오에서 데이터에 태그를 지정할 수 있습니다. 태그가 지정 된 데이터는 [태그 파일 형식을](../concepts/data-formats.md)따라야 합니다. 

>[!TIP]
> 데이터 준비 및 데이터 선택 및 준비에 대 한 정보에 대 한 [스키마 정의](../how-to/design-schema.md) 를 검토 합니다.

## <a name="create-a-custom-named-entity-recognition-project"></a>사용자 지정 명명된 엔터티 인식 프로젝트 만들기

>[!NOTE]
> 데이터 집합에 다른 언어의 파일이 포함 되어 있거나 런타임 중에 다른 언어를 사용 하는 경우 다국어 옵션을 사용 하도록 설정할 수 있습니다.

[!INCLUDE [Create custom NER project](../includes/create-project.md)]

## <a name="next-steps"></a>다음 단계

프로젝트를 만든 후에는 [데이터 태그](tag-data.md)를 지정 하 여 엔터티 추출 모델에 텍스트를 해석 하는 방법을 알리고 학습 및 평가에 사용 됩니다.
