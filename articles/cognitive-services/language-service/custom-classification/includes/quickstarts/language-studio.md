---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: bcba387ce5fd6e5265ae1deed35b962bbdce3b56
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132875431"
---
## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)

## <a name="create-a-new-azure-resource-and-azure-blob-storage-account"></a>새 Azure 리소스 및 Azure Blob Storage 계정 만들기

사용자 지정 텍스트 분류를 사용하려면 먼저 프로젝트를 만들고 모델 교육을 시작하는 데 필요한 자격 증명을 제공하는 Azure 언어 리소스를 만들어야 합니다. 또한 모델을 빌드하는 데 사용할 데이터 세트를 업로드할 수 있는 Azure 스토리지 계정이 필요합니다.

> [!IMPORTANT]
> 빠르게 시작하려면 아래 제공된 단계를 사용하여 새 Azure 언어 리소스를 만드는 것이 좋습니다. 그러면 리소스를 만들고 동시에 스토리지 계정을 구성할 수 있습니다. 이는 나중에 수행하는 것보다 쉽습니다.
>
> 사용하려는 기존 리소스가 있는 경우 해당 리소스와 스토리지 계정을 별도로 구성해야 합니다. 자세한 내용은 [**프로젝트 요구 사항**](../../how-to/create-project.md#using-a-pre-existing-azure-resource)을 참조하세요.

1. [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)로 이동하여 새 Azure 언어 리소스를 만듭니다. 추가 기능을 선택하라는 메시지가 표시되면 **사용자 지정 텍스트 분류 및 사용자 지정 NER** 를 선택합니다. 리소스를 만들 때 다음 매개 변수가 있는지 확인합니다.

    |Azure 리소스 요구 사항  |필수 값  |
    |---------|---------|
    |위치 | "West US 2" 또는 "West Europe"         |
    |가격 책정 계층     | 표준(**S**) 가격 책정 계층        |

2. **Custom NER(명명된 엔터티 인식) 및 사용자 지정 분류 (미리 보기)** 섹션에서 기존 스토리지 계정을 선택하거나 **새 스토리지 계정 만들기** 를 선택합니다. 이러한 값은 이 빠른 시작을 위한 것이며 프로덕션 환경에서 사용하려는 [스토리지 계정 값](../../../../../storage/common/storage-account-overview.md)이 반드시 필요한 것은 아닙니다.

    |스토리지 계정 값  |권장되는 값  |
    |---------|---------|
    | Name | 임의의 이름 |
    | 성능 | 표준 |
    | 계정 종류| 스토리지(범용 v1) |
    | 복제 | LRS(로컬 중복 스토리지)
    |위치 | 최고의 대기 시간을 위해 가장 가까운 모든 위치.        |


## <a name="upload-sample-data-to-blob-container"></a>Blob 컨테이너에 샘플 데이터 업로드

Azure 스토리지 계정을 만들고 언어 리소스에 연결한 후에는 이 빠른 시작을 위한 예제 파일을 업로드해야 합니다. 이 파일은 나중에 모델을 학습하는 데 사용됩니다.

1. GitHub에서 이 빠른 시작을 위한 [샘플 데이터를 다운로드](https://go.microsoft.com/fwlink/?linkid=2175083)합니다.

2. [Azure Portal](https://ms.portal.azure.com)에서 Azure 스토리지 계정으로 이동합니다. 계정으로 이동하여 샘플 데이터를 업로드합니다.

제공된 샘플 데이터 세트에는 "미스터리", "드라마", "스릴러", "코미디", "액션" 클래스 중 하나 이상에 속하는 약 200개의 영화 요약이 포함되어 있습니다.

## <a name="create-a-custom-classification-project"></a>사용자 지정 분류 프로젝트 만들기

1. [Language Studio](https://aka.ms/languageStudio)에 로그인합니다. 구독 및 언어 리소스를 선택할 수 있는 창이 표시됩니다. 위의 단계에서 만든 리소스를 선택합니다.

2. Language Studio의 **텍스트 분류** 섹션 아래의 사용 가능한 서비스에서 **사용자 지정 텍스트 분류** 를 선택하여 리소스를 선택합니다.
    
    :::image type="content" source="../../media/select-custom-text-classification.png" alt-text="Language Studio 방문 페이지에서 사용자 지정 분류 위치를 보여주는 스크린샷." lightbox="../../media/select-custom-text-classification.png":::
    
3. 프로젝트 페이지의 상단 메뉴에서 **새 프로젝트 만들기** 를 선택합니다. 프로젝트를 만들면 태그를 데이터에 지정하고, 모델을 학습시키고, 평가하고, 향상시키고, 배포할 수 있습니다. 

    :::image type="content" source="../../media/create-project.png" alt-text="프로젝트 만들기 페이지의 스크린샷" lightbox="../../media/create-project.png":::
<!--
4. If you have created your resource using the steps above, the **Connect storage** step will be completed already. You only need to do this step once for each resource you use and it is irreversible. If you connect a storage account to your resource, you cannot disconnect it later.

    :::image type="content" source="../../../custom-named-entity-recognition/media/connect-storage.png" alt-text="A screenshot showing the storage connection screen." lightbox="../../../custom-named-entity-recognition/media/connect-storage.png":::
-->
4. 위의 단계를 사용하여 리소스를 만든 경우 **커넥트 스토리지** 단계가 이미 완료되었을 것입니다. 그렇지 않으면 리소스에 연결하기 전에 [스토리지 계정에 대한 역할](../../how-to/create-project.md#roles-for-your-storage-account)을 할당해야 합니다.

5. 프로젝트에 있는 파일의 이름, 설명 및 언어를 포함한 프로젝트 정보를 입력합니다. 프로젝트 이름은 나중에 변경할 수 없습니다.
    >[!TIP]
    > 데이터 세트는 완전히 동일한 언어로 되어 있지 않아도 됩니다. 지원되는 언어가 각각 다른 여러 파일을 사용할 수 있습니다. 데이터 세트에 다른 언어의 파일이 포함되어 있거나 런타임 중에 다른 언어를 사용하는 경우 프로젝트에 대한 기본 정보를 입력할 때 **다국어 데이터 세트 사용** 을 선택합니다.

6. 프로젝트 형식을 선택합니다. 이 빠른 시작에서는 여러 클래스를 동일한 파일에 할당할 수 있는 다중 레이블 분류 프로젝트를 만듭니다. 그런 후 **Next** 를 클릭합니다. [프로젝트 형식](../../glossary.md#project-types)에 대한 자세한 정보

7. 데이터를 업로드한 컨테이너를 선택합니다. 이 빠른 시작에서는 컨테이너에서 사용할 수 있는 기존 태그 파일을 사용합니다. 그런 후 **Next** 를 클릭합니다.

8. 입력한 데이터를 검토하고, **프로젝트 만들기** 를 선택합니다.
    
## <a name="train-your-model"></a>모델 학습

일반적으로 프로젝트를 만든 후에는 데이터를 가져와서 분류 모델을 학습시키기 위해 데이터 내의 [엔터티에 태그를 지정](../../how-to/tag-data.md)하기 시작합니다. 이 빠른 시작에서는 이전에 다운로드하고 Azure 스토리지 계정에 저장한 태그가 지정된 데이터 파일 예제를 사용합니다.

모델은 텍스트를 분류하도록 학습될 기계 학습 개체입니다. 모델은 예제 데이터에서 학습하고 나중에 기술 지원 티켓을 분류할 수 있습니다.

모델 학습을 시작하려면:

1. 왼쪽 메뉴에서 **학습** 을 선택합니다.

2. **새 모델 학습** 을 선택하고 아래 텍스트 상자에 모델 이름을 입력합니다.

    :::image type="content" source="../../media/train-model.png" alt-text="학습용 모델 선택 페이지를 보여 주는 스크린샷" lightbox="../../media/train-model.png":::

3. 페이지 하단의 **학습** 단추를 클릭합니다.

    > [!NOTE]
    > * 학습하는 동안 데이터는 학습용 80%, 테스트용 20%의 두 세트로 나뉩니다. [여기](../../how-to/train-model.md#data-splits)에서 데이터 분할에 대해 자세히 알아볼 수 있습니다.
    > * 학습에는 최대 몇 시간이 소요될 수 있습니다.

## <a name="deploy-your-model"></a>모델 배포


일반적으로 모델을 학습시킨 후 [평가 세부 정보](../../how-to/view-model-evaluation.md)를 검토하고 필요한 경우 [개선](../../how-to/improve-model.md)합니다. 이 빠른 시작에서는 모델을 배포하고 사용할 수 있도록 합니다.

모델을 학습한 후 배포할 수 있습니다. 모델을 배포하면 [Analyze API](https://aka.ms/ct-runtime-swagger)를 사용하여 텍스트를 분류하는 데 사용할 수 있습니다.

1. 왼쪽 메뉴에서 **모델 배포** 를 선택합니다.

2. 배포할 모델을 선택한 다음 **모델 배포** 를 선택합니다.

## <a name="test-your-model"></a>모델 테스트

모델을 배포한 후 텍스트 분류에 사용할 수 있습니다. 다음 단계를 사용하여 첫 번째 텍스트 분류 요청을 보냅니다. 

1. 왼쪽 메뉴에서 **테스트 모델** 을 선택합니다.

2. 테스트할 모델을 선택합니다.

3. 텍스트 상자에 텍스트를 추가하고 `.txt` 파일을 업로드할 수도 있습니다. 

4. **테스트 실행** 을 클릭합니다.

5. **결과** 탭에서 텍스트의 예측 클래스를 볼 수 있습니다. **JSON** 탭에서 JSON 응답을 볼 수도 있습니다. 

    :::image type="content" source="../../media/test-model-results.png" alt-text="모델 테스트 결과를 보여 주는 스크린샷. 예는 Microsoft에서 수정한 CMU Movie Summary, CC BY-SA 3.0에서 가져온 것입니다." lightbox="../../media/test-model-results.png":::

## <a name="clean-up-projects"></a>프로젝트 정리

프로젝트가 더 이상 필요하지 않으면 [Language Studio의 프로젝트 페이지](https://aka.ms/custom-classification
)에서 삭제할 수 있습니다. 삭제할 프로젝트를 선택하고 **삭제** 를 클릭합니다.