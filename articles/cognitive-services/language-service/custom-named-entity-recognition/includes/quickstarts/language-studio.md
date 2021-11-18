---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0f6258d1f851fc860c2e9e2b2d302545d9233f89
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132590586"
---
## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)

## <a name="create-a-new-azure-resource-and-azure-blob-storage-account"></a>새 Azure 리소스 및 Azure Blob Storage 계정 만들기

사용자 지정 NER을 사용하려면 먼저 프로젝트를 만들고 모델 학습을 시작하는 데 필요한 자격 증명을 제공하는 Azure 언어 리소스를 만들어야 합니다. 또한 모델을 빌드하는 데 사용할 데이터 세트를 업로드할 수 있는 Azure 스토리지 계정이 필요합니다.

> [!IMPORTANT]
> 빠르게 시작하려면 아래 제공된 단계를 사용하여 새 Azure 언어 리소스를 만드는 것이 좋습니다. 그러면 리소스를 만들고 동시에 스토리지 계정을 구성할 수 있습니다. 이는 나중에 수행하는 것보다 쉽습니다.
>
> 사용하려는 기존 리소스가 있는 경우 해당 리소스와 스토리지 계정을 별도로 구성해야 합니다. 자세한 내용은 [프로젝트 만들기](../../how-to/create-project.md#using-a-pre-existing-azure-resource)를 참조하세요.

1. [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)로 이동하여 새 Azure 언어 리소스를 만듭니다. 추가 기능을 선택하라는 메시지가 표시되면 **사용자 지정 텍스트 분류 및 사용자 지정 NER** 를 선택합니다. 리소스를 만들 때 다음 매개 변수가 있는지 확인합니다.

    |Azure 리소스 요구 사항  |필수 값  |
    |---------|---------|
    |위치 | "West US 2" 또는 "West Europe"         |
    |가격 책정 계층     | 표준(**S**) 가격 책정 계층        |

2. **Custom NER(명명된 엔터티 인식) 및 사용자 지정 분류 (미리 보기)** 섹션에서 기존 스토리지 계정을 선택하거나 **새 스토리지 계정 만들기** 를 선택합니다. 이러한 값은 이 빠른 시작을 위한 것이며 프로덕션 환경에서 사용하려는 [스토리지 계정 값](/azure/storage/common/storage-account-overview)이 반드시 필요한 것은 아닙니다.

    |스토리지 계정 값  |권장되는 값  |
    |---------|---------|
    | Name | 임의의 이름 |
    | 성능 | 표준 |
    | 계정 종류| 스토리지(범용 v1) |
    | 복제 | LRS(로컬 중복 스토리지)
    |위치 | 최고의 대기 시간을 위해 가장 가까운 모든 위치.        |

## <a name="upload-sample-data-to-blob-container"></a>Blob 컨테이너에 샘플 데이터 업로드

Azure 스토리지 계정을 만들고 언어 리소스에 연결한 후에는 이 빠른 시작을 위한 예제 파일을 업로드해야 합니다. 이러한 파일은 나중에 모델을 학습시키는 데 사용됩니다.

1. GitHub에서 이 빠른 시작을 위한 [예제 데이터를 다운로드](https://go.microsoft.com/fwlink/?linkid=2175226)합니다.

2. [Azure Portal](https://ms.portal.azure.com)에서 Azure 스토리지 계정으로 이동합니다. 계정으로 이동하여 샘플 데이터를 업로드합니다.

제공된 샘플 데이터 세트에는 20개의 대출 계약이 포함되어 있고, 각 계약에는 대출 기관과 대출자라는 두 당사자가 포함되어 있습니다. 제공된 샘플 파일을 사용하여 두 당사자, 계약 날짜, 대출 금액 및 이자율에 대한 관련 정보를 추출할 수 있습니다.

## <a name="create-a-custom-named-entity-recognition-project"></a>사용자 지정 명명된 엔터티 인식 프로젝트 만들기

리소스 및 스토리지 컨테이너가 구성되면 새 대화형 NER 프로젝트를 만듭니다. 프로젝트는 데이터를 기반으로 하는 사용자 지정 AI 모델을 빌드하기 위한 작업 영역입니다. 사용자 및 사용 중인 Azure 리소스에 대한 기여자 액세스 권한이 있는 다른 사용자만 프로젝트에 액세스할 수 있습니다.

1. [언어 스튜디오 포털](https://aka.ms/languageStudio)에 로그인합니다. 구독 및 언어 리소스를 선택할 수 있는 창이 표시됩니다. 위의 단계에서 만든 리소스를 선택합니다.

2. **엔터티 추출** 섹션을 찾고, 사용 가능한 서비스에서 **사용자 지정 명명된 엔터티 인식** 을 선택합니다.
    
    :::image type="content" source="../../media/select-custom-ner.png" alt-text="Language Studio 방문 페이지에서 사용자 지정 NER의 위치를 보여주는 스크린샷." lightbox="../../media/select-custom-ner.png":::

3. 프로젝트 페이지의 상단 메뉴에서 **새 프로젝트 만들기** 를 선택합니다. 프로젝트를 만들면 태그를 데이터에 지정하고, 모델을 학습시키고, 평가하고, 향상시키고, 배포할 수 있습니다. 

    
    :::image type="content" source="../../media/create-project.png" alt-text="프로젝트 만들기 페이지의 스크린샷" lightbox="../../media/create-project.png":::


4.  **새 프로젝트 만들기** 를 클릭하면 스토리지 계정을 연결할 수 있는 화면이 표시됩니다. 스토리지 계정을 찾을 수 없는 경우 위의 단계를 사용하여 리소스를 만들었는지 확인합니다. 

    >[!NOTE]
    > * 사용하는 각 새 리소스에 대해 이 단계를 한 번만 수행하면 됩니다. 
    > * 이 프로세스는 되돌릴 수 없으며, 스토리지 계정을 리소스에 연결하는 경우 나중에 해당 연결을 끊을 수 없습니다.
    > * 리소스는 하나의 스토리지 계정에만 연결할 수 있습니다.
    > * 이미 스토리지 계정을 연결한 경우 **기본 정보 입력** 화면이 대신 표시됩니다. 다음 단계를 참조하세요.
    
    :::image type="content" source="../../media/connect-storage.png" alt-text="스토리지 연결 화면을 보여 주는 스크린샷" lightbox="../../media/connect-storage.png":::

<!--If you're using a preexisting resource, see [creating Azure resources](../concepts/use-azure-resources.md). When you are done, select **Next**.--> 

5. 프로젝트에 있는 파일의 이름, 설명 및 언어를 포함한 프로젝트 정보를 입력합니다. 프로젝트 이름은 나중에 변경할 수 없습니다. 

6. 데이터를 업로드한 컨테이너를 선택합니다. 이 빠른 시작에서는 컨테이너에서 사용할 수 있는 기존 태그 파일을 사용합니다. 그런 후 **Next** 를 클릭합니다.

7. 입력한 데이터를 검토하고, **프로젝트 만들기** 를 선택합니다.

## <a name="train-your-model"></a>모델 학습

일반적으로 프로젝트를 만든 후에는 데이터를 가져와서 분류 모델을 학습시키기 위해 데이터 내의 [엔터티에 태그를 지정](../../how-to/tag-data.md)하기 시작합니다. 이 빠른 시작에서는 이전에 다운로드하고 Azure 스토리지 계정에 저장한 태그가 지정된 데이터 파일 예제를 사용합니다.

모델은 텍스트를 분류하도록 학습될 기계 학습 개체입니다. 모델은 예제 데이터에서 학습하고 나중에 기술 지원 티켓을 분류할 수 있습니다.

모델 학습을 시작하려면:

1. 왼쪽 메뉴에서 **학습** 을 선택합니다.

2. **새 모델 학습** 을 선택하고 아래 텍스트 상자에 모델 이름을 입력합니다.

    :::image type="content" source="../../media/train-model.png" alt-text="학습용 모델 선택 페이지를 보여 주는 스크린샷" lightbox="../../media/train-model.png":::

3. 페이지 하단의 **학습** 단추를 클릭합니다.

    > [!NOTE]
    > * 학습하는 동안 데이터는 학습용 80%, 테스트용 20%의 두 세트로 나뉩니다. [여기](../../how-to/train-model.md#data-split)에서 데이터 분할에 대해 자세히 알아볼 수 있습니다.
    > * 학습에는 최대 몇 시간이 소요될 수 있습니다.

## <a name="deploy-your-model"></a>모델 배포

일반적으로 모델을 학습시킨 후 [평가 세부 정보](../../how-to/view-model-evaluation.md)를 검토하고 필요한 경우 [개선](../../how-to/improve-model.md)합니다. 이 빠른 시작에서는 모델을 배포하고 사용할 수 있도록 합니다.

모델이 학습되면 해당 모델을 배포할 수 있습니다. 모델을 배포하면 [분석 API](https://aka.ms/ct-runtime-swagger)를 사용하여 명명된 엔터티를 추출하는 데 사용할 수 있습니다.

1. 왼쪽 메뉴에서 **모델 배포** 를 선택합니다.

2. 배포할 모델을 선택한 다음 **모델 배포** 를 선택합니다.

## <a name="test-your-model"></a>모델 테스트

모델이 배포되면 이를 사용하여 엔터티 추출을 시작할 수 있습니다. 다음 단계를 사용하여 첫 번째 엔터티 추출 요청을 보냅니다.

1. 왼쪽 메뉴에서 **모델 테스트** 를 선택합니다.

2. 테스트할 모델을 선택합니다.

3. 텍스트 상자에 텍스트를 추가하고 `.txt` 파일을 업로드할 수도 있습니다. 

4. **테스트 실행** 을 클릭합니다.

5. **결과** 탭에 텍스트에서 추출된 엔터티와 해당 형식이 표시됩니다. 또한 **JSON** 탭 아래에는 JSON 응답이 표시됩니다.

    :::image type="content" source="../../media/test-model-results.png" alt-text="테스트 결과 보기" lightbox="../../media/test-model-results.png":::


## <a name="clean-up-resources"></a>리소스 정리

프로젝트가 더 이상 필요하지 않은 경우 [언어 스튜디오](https://aka.ms/custom-extraction)를 사용하여 프로젝트를 삭제할 수 있습니다. 왼쪽 탐색 메뉴에서 **사용자 지정 NER(명명된 엔터티 인식)** 을 선택하고, 삭제하려는 프로젝트를 선택한 다음, **삭제** 를 클릭합니다.
