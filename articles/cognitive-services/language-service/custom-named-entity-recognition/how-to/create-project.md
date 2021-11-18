---
title: 사용자 지정 NER에서 Azure 리소스 사용
titleSuffix: Azure Cognitive Services
description: 사용자 지정 NER에서 Azure 리소스를 사용하는 단계에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, references_regions, ignite-fall-2021
ms.openlocfilehash: 97b4a527fe049f4a36839ed38b1498307ea30d36
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132705078"
---
# <a name="how-to-create-custom-ner-projects"></a>사용자 지정 NER 프로젝트를 만드는 방법

사용자 지정 NER 사용을 시작하기 전에 몇 가지 사항이 필요합니다.

* Azure 언어 리소스 
* `.txt`텍스트를 분류하는 AI 모델을 학습하는 데 사용할 파일을 업로드할 Azure Storage 계정

이 문서에서는 사용자 지정 NER를 사용하기 위한 요구 사항을 준비하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독 - [체험 구독을 만듭니다.](https://azure.microsoft.com/free/cognitive-services)
데이터에 사용할 [프로젝트 스키마에](design-schema.md) 대한 아이디어가 있어야 합니다.

이 문서에서는 사용자 지정 텍스트 분류를 사용하기 위한 요구 사항을 준비하는 방법을 알아봅니다.

## <a name="azure-resources"></a>: Azure 리소스

사용자 지정 NER 사용을 시작하기 전에 Azure 언어 리소스가 필요합니다. Azure Portal 만드는 빠른 [시작의](../quickstart.md) 단계를 권장합니다. Azure Portal 리소스를 만들면 필요한 모든 권한이 미리 구성된 Azure Storage 계정을 동시에 만들 수 있습니다. 또한 이 문서에서 기존 리소스를 사용하는 방법을 알아보고 사용자 지정 NER에서 작동하도록 구성하는 방법을 자세히 알아볼 수도 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

[!INCLUDE [create a new resource from the Azure portal](../../custom-classification/includes/resource-creation-azure-portal.md)]

# <a name="language-studio"></a>[Language Studio](#tab/studio)

### <a name="create-a-new-resource-from-language-studio"></a>Language Studio에서 새 리소스 만들기

처음 로그인하는 경우 언어 리소스를 선택하거나 새 리소스를 만들 수 있는 창이 [Language Studio에](https://aka.ms/languageStudio) 표시됩니다. 오른쪽 위 모서리에서 설정 아이콘을 클릭하고 리소스 를 선택한 다음 새 리소스 만들기를 클릭하여 **리소스를 만들** 수도 있습니다.

> [!IMPORTANT]
> * 사용자 지정 NER를 사용하려면 표준(**S)** 가격 책정 계층이 있는 **미국 서부 2** 또는 **서유럽의** 언어 리소스가 필요합니다.
> * 리소스를 만들 때 **관리 ID를** 선택해야 합니다. 

:::image type="content" source="../../media/create-new-resource-small.png" alt-text="Language Studio의 리소스 만들기 화면을 보여주는 스크린샷." lightbox="../../media/create-new-resource.png":::

사용자 지정 NER를 사용하려면 [Azure Storage 계정이](../../../../storage/common/storage-account-create.md) 없는 경우 만들어야 합니다. 

다음으로, 스토리지 계정에 [대한 올바른 역할을](#roles-for-your-storage-account) 할당하여 언어 리소스에 연결해야 합니다. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

### <a name="create-a-new-resource-with-the-azure-powershell"></a>Azure PowerShell 사용하여 새 리소스 만들기

GitHub 호스트되는 다음 CLI [템플릿](https://github.com/Azure-Samples/cognitive-services-sample-data-files) 및 [매개 변수](https://github.com/Azure-Samples/cognitive-services-sample-data-files) 파일을 사용하여 새 리소스 및 스토리지 계정을 만들 수 있습니다.

매개 변수 파일에서 다음 값을 편집합니다.

| 매개 변수 이름 | 값 설명 |
|--|--|
|`name`| 언어 리소스의 이름|
|`location`| 리소스가 호스트되는 지역입니다. 사용자 지정 NER는 **미국 서부 2** 및 **서유럽** 에서만 사용할 수 있습니다.|
|`sku`| 리소스의 가격 책정 계층입니다. 이 기능은 **S** 계층에서만 작동합니다.|
|`storageResourceName`| 스토리지 계정의 이름|
|`storageLocation`| 스토리지 계정이 호스트되는 지역입니다.|
|`storageSkuType`| 스토리지 계정 의 [SKU입니다.](/rest/api/storagerp/srp_sku_types)|
|`storageResourceGroupName`| 스토리지 계정의 리소스 그룹|
<!-- |builtInRoleType| 이 역할을 **"기여자"로** 설정| -->

다음 PowerShell 명령을 사용하여 편집한 파일과 함께 ARM(Azure Resource Manager) 템플릿을 배포합니다.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-arm-template> `
  -TemplateParameterFile <path-to-parameters-file>
```

템플릿 및 [매개 변수 파일](../../../../azure-resource-manager/templates/parameter-files.md?tabs=json) [배포에](../../../../azure-resource-manager/templates/deploy-powershell.md#parameter-files) 대한 자세한 내용은 ARM 템플릿 설명서를 참조하세요.

--- 

## <a name="using-a-pre-existing-azure-resource"></a>기존 Azure 리소스 사용

이 리소스가 아래 요구 사항을 충족하는 한 기존 언어 리소스를 사용하여 사용자 지정 NER를 시작할 수 있습니다.

|요구 사항  |Description  |
|---------|---------|
|영역     | 기존 리소스가 지원되는 두 지역인 **미국 서부 2** 또는 **서유럽** 중 하나에 프로비전되었는지 확인합니다. 그렇지 않은 경우 이러한 지역에 새 리소스를 만들어야 합니다.        |
|가격 책정 계층     | 기존 리소스가 표준(**S)** 가격 책정 계층에 있는지 확인합니다. 이 가격 책정 계층만 지원됩니다. 리소스에서 이 가격 책정 계층을 사용하지 않는 경우 새 리소스를 만들어야 합니다.        |
|관리 ID     | 리소스 관리 ID 설정이 사용하도록 설정되어 있는지 확인합니다. 그렇지 않으면 다음 섹션을 읽습니다. |

사용자 지정 NER를 사용하려면 [Azure Storage 계정이](../../../../storage/common/storage-account-create.md) 없는 경우 만들어야 합니다. 

다음으로, 스토리지 계정에 [대한 올바른 역할을](#roles-for-your-storage-account) 할당하여 언어 리소스에 연결해야 합니다. 

## <a name="roles-for-your-azure-language-resource"></a>Azure 언어 리소스에 대한 역할

Azure 언어 리소스에 **소유자** 또는 **기여자** 역할이 할당되어 있어야 합니다.

## <a name="enable-identity-management-for-your-resource"></a>리소스에 대한 ID 관리 사용

언어 리소스에는 Azure Portal 사용하거나 Language Studio에서 사용하도록 설정할 수 있는 ID 관리가 있어야 합니다. [Language Studio를](https://aka.ms/languageStudio)사용하여 사용하도록 설정하려면:
1. 화면의 오른쪽 위 모서리에서 설정 아이콘을 클릭합니다.
2. **리소스** 를 선택합니다
3. Azure 리소스에 대한 **관리 ID를** 선택합니다.

## <a name="roles-for-your-storage-account"></a>스토리지 계정에 대한 역할

Azure Blob Storage 계정에는 아래 역할이 있어야 합니다.

* 리소스에는 스토리지 계정에 대한 **소유자** 또는 **기여자** 역할이 있습니다.
* 리소스에는 스토리지 계정에 **대한 Storage Blob 데이터 소유자** 또는 Storage **Blob 데이터 기여자** 역할이 있습니다.
* 리소스는 스토리지 계정에 대한 **읽기** 역할입니다.

스토리지 계정에 적절한 역할을 설정하려면 다음을 수행합니다.

1. [Azure Portal](https://ms.portal.azure.com/)스토리지 계정 페이지로 이동합니다.
2. 왼쪽 탐색 메뉴에서 **Access Control(IAM)를** 선택합니다.
3. 역할 **할당 추가에** **추가를** 선택하고 **소유자** 또는 **기여자** 역할을 선택합니다. **선택** 필드에서 사용자 이름을 검색할 수 있습니다.

[!INCLUDE [Storage connection note](../../custom-classification/includes/storage-account-note.md)]

## <a name="prepare-training-data"></a>학습 데이터 준비

* 사용자 지정 NER 프로젝트를 만들기 위한 필수 조건으로 학습 데이터를 스토리지 계정의 Blob 컨테이너에 업로드해야 합니다. Azure에서 직접 또는 Azure Storage Explorer 도구를 사용하여 학습 파일을 만들고 업로드할 수 있습니다. Azure Storage Explorer 도구를 사용하면 더 적은 시간 안에 더 많은 데이터를 업로드할 수 있습니다.

  * [Azure에서 파일 만들기 및 업로드](../../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
  * [Azure Storage Explorer를 사용하여 파일 만들기 및 업로드](../../../../vs-azure-tools-storage-explorer-blobs.md)

* `.txt`만 사용할 수 있습니다. 사용자 지정 NER용 파일입니다. 데이터가 다른 형식인 경우 Cognitive Services [언어 유틸리티 도구를](https://aka.ms/CognitiveServicesLanguageUtilities) 사용하여 파일을 형식으로 구문 분석할 수 `.txt` 있습니다.

* 태그가 지정된 데이터를 업로드하거나 Language Studio에서 데이터에 태그를 지정할 수 있습니다. 태그가 지정된 데이터는 [태그 파일 형식 을](../concepts/data-formats.md)따라야 합니다. 

>[!TIP]
> 데이터 준비를 검토하고 데이터 선택 및 준비에 대한 정보를 위한 [스키마를 정의합니다.](../how-to/design-schema.md)

## <a name="create-a-custom-named-entity-recognition-project"></a>사용자 지정 명명된 엔터티 인식 프로젝트 만들기

>[!NOTE]
> 데이터 세트에 다른 언어의 파일이 포함되어 있거나 런타임 중에 다른 언어가 예상되는 경우 다국어 옵션을 사용하도록 설정할 수 있습니다.

[!INCLUDE [Create custom NER project](../includes/create-project.md)]

## <a name="next-steps"></a>다음 단계

프로젝트를 만든 [후에는 엔터티](tag-data.md)추출 모델에 텍스트를 해석하는 방법을 알려주고 학습 및 평가에 사용되는 데이터 태그 지정을 시작할 수 있습니다.