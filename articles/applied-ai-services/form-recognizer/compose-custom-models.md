---
title: 방법-사용자 지정 및 구성 된 모델
titleSuffix: Azure Applied AI Services
description: 양식 인식기 사용자 지정 및 구성 된 모델을 만들고 사용 하 고 관리 하는 방법을 알아봅니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: ef5514b44ad2d35870674a85958c28f5f780c308
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233699"
---
# <a name="how-to-use-custom-and-composed-models"></a>방법: 사용자 지정 및 구성 된 모델 사용

Form Recognizer는 고급 기계 학습 기술을 사용하여 문서 이미지에서 정보를 탐지하여 추출하고, 추출된 데이터를 정형 JSON 출력으로 반환합니다. 양식 인식기를 사용 하면 독립 실행형 사용자 지정 모델을 학습 하거나 사용자 지정 모델을 결합 하 여 구성 된 모델을 만들 수 있습니다.

* **사용자 지정 모델**. Form Recognizer 사용자 지정 모델을 사용하면 비즈니스와 관련된 양식 및 문서의 데이터를 분석하고 추출할 수 있습니다. 사용자 지정 모델은 고객의 고유한 데이터 및 사용 사례를 학습합니다.

* **구성형 모델**. 구성형 모델은 사용자 지정 모델 컬렉션을 가져와서 사용자의 양식 유형을 포함하는 단일 모델에 할당하여 만듭니다. 구성형 모델에 문서가 제출되면 이 서비스에서는 분류 단계를 수행하여 분석용으로 제공된 양식을 정확히 나타내는 사용자 지정 모델을 결정합니다.

이 문서에서는 [폼 인식기 샘플 레이블 도구](label-tool.md), [REST api](./quickstarts/try-sdk-rest-api.md?branch=main&pivots=programming-language-rest-api#train-a-custom-model)또는 [클라이언트 라이브러리 Sdk](./quickstarts/try-sdk-rest-api.md?branch=main&pivots=programming-language-csharp#train-a-custom-model)를 사용 하 여 양식 인식기 사용자 지정 및 구성 된 모델을 만드는 방법을 살펴보겠습니다.

## <a name="try-it-sample-labeling-tool"></a>사용해 보기: 샘플 레이블 지정 도구

샘플 레이블 지정 도구를 사용해 서 사용자 지정 양식에서 데이터를 추출 하는 방법을 확인할 수 있습니다. 다음이 필요 합니다.

* Azure 구독- [무료로 하나를 만들](https://azure.microsoft.com/free/cognitive-services/) 수 있습니다.

* Azure Portal의 [폼 인식기 인스턴스입니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) . 무료 가격 책정 계층(`F0`)을 사용하여 서비스를 시도할 수 있습니다. 리소스를 배포한 후 **리소스로 이동** 을 클릭 하 여 API 키 및 끝점을 가져옵니다.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

> [!div class="nextstepaction"]
> [사용해 보기](https://fott-2-1.azurewebsites.net/projects/create)

양식 인식기 UI에서:

1. **사용자 지정 사용을 선택 하 여 레이블을 사용 하 여 모델을 학습 하 고 키 값 쌍을 가져옵니다**.
  
      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="스크린샷: 사용자 지정 옵션의 fott 도구 선택":::

1. 다음 창에서 **새 프로젝트** 를 선택 합니다.

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="스크린샷: 새 프로젝트의 fott 도구 선택"::: 

## <a name="create-your-models"></a>모델 만들기

사용자 지정 모델 및 구성형 모델을 작성, 학습 및 사용하는 단계는 다음과 같습니다.

* [**학습 데이터 세트 조합**](#assemble-your-training-dataset)
* [**Azure Blob 스토리지에 학습 세트 업로드**](#upload-your-training-dataset)
* [**사용자 지정 모델 학습**](#train-your-custom-model)
* [**사용자 지정 모델 작성**](#create-a-composed-model)
* [**문서 분석**](#analyze-documents-with-your-custom-or-composed-model)
* [**사용자 지정 모델 관리**](#manage-your-custom-models)

## <a name="assemble-your-training-dataset"></a>학습 데이터 세트 조합

사용자 지정 모델 빌드는 학습 데이터 세트를 설정하는 것으로 시작합니다. 샘플 데이터 세트에 대해 동일한 유형으로 완성된 5개 이상의 양식이 필요합니다. 파일 형식이 다를 수 있으며(jpg, png, pdf, tiff) 텍스트와 필기를 모두 포함할 수 있습니다. 양식은 Form Recognizer의 [입력 요구 사항](build-training-data-set.md#custom-model-input-requirements)을 따라야 합니다.

## <a name="upload-your-training-dataset"></a>학습 데이터 세트 업로드

[학습 데이터를 Azure Blob 스토리지 컨테이너에 업로드](build-training-data-set.md#upload-your-training-data)해야 합니다. 컨테이너를 사용하여 Azure 스토리지 계정을 만드는 방법을 모르는 경우 [Azure Portal의 Azure Storage 빠른 시작](../../storage/blobs/storage-quickstart-blobs-portal.md)을 *참조하세요*. 체험 가격 책정 계층(F0)을 통해 서비스를 사용해 보고, 나중에 프로덕션을 위한 유료 계층으로 업그레이드할 수 있습니다.

## <a name="train-your-custom-model"></a>사용자 지정 모델 학습

레이블이 지정된 데이터 세트를 사용하거나 사용하지 않고 [모델을 학습시킬 수](./quickstarts/try-sdk-rest-api.md#train-a-custom-model) 있습니다. 레이블이 지정되지 않은 데이터 세트는 사용자 입력을 추가하지 않고 키 정보를 검색하여 식별하는 [레이아웃 API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)만 사용합니다. 레이블이 지정된 데이터 세트도 레이아웃 API를 사용하지만 사용자의 특정 레이블 및 필드 위치와 같은 추가 사용자 입력이 포함됩니다. 레이블이 지정된 데이터와 레이블이 지정되지 않은 데이터를 모두 사용하려면 레이블이 지정된 학습 데이터에 대해 동일한 유형으로 완성된 5개 이상의 양식으로 시작한 다음, 레이블이 지정되지 않은 데이터를 필요한 데이터 세트에 추가합니다.

### <a name="train-without-labels"></a>레이블 없이 학습

Form Recognizer는 자율 학습을 사용하여 양식의 필드와 항목 간의 레이아웃 및 관계를 이해합니다. 입력 양식을 제출하면 알고리즘에서 형식별로 양식을 클러스터링하고, 존재하는 키와 테이블을 검색하고, 값을 키에 연결하고, 항목을 테이블에 연결합니다. 레이블이 없는 학습에는 수동 데이터 레이블을 지정하거나 과도한 코딩 및 유지 관리가 필요 없으므로 이 방법을 먼저 시도하는 것이 좋습니다.

학습 문서를 수집하는 방법에 대한 팁은 [학습 데이터 세트 빌드](./build-training-data-set.md)를 참조하세요.

### <a name="train-with-labels"></a>레이블을 사용하여 학습

레이블이 지정된 데이터로 학습하는 경우 모델은 감독 학습을 통해 사용자가 제공하는 레이블 지정 양식을 사용하여 관심 있는 값을 추출합니다. 레이블이 지정된 데이터는 모델 성능이 향상되며, 복잡한 양식 또는 키 없는 값을 포함하는 양식과 함께 작동하는 모델을 생성할 수 있습니다.

Form Recognizer는 [레이아웃 API](concept-layout.md)를 사용하여 인쇄 및 필기된 텍스트 요소의 예상 크기와 위치를 학습하고 테이블을 추출합니다. 그런 다음, 사용자 지정 레이블을 사용하여 문서의 키/값 연결 및 테이블을 학습합니다. 새 모델을 학습시킬 때 동일한 유형(동일한 구조)의 수동 레이블 지정 양식 5개를 사용하여 시작한 후 필요에 따라 레이블 지정 데이터를 추가하여 모델 정확도를 개선하는 것이 좋습니다. Form Recognizer를 통해 감독 학습 기능을 사용하여 키 값 쌍과 테이블을 추출하도록 모델을 학습할 수 있습니다.

[레이블로 학습 시작](label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="create-a-composed-model"></a>구성형 모델 만들기

> [!NOTE]
> **모델 만들기는 레이블을 _사용하여_ 학습된 사용자 지정 모델에만 사용할 수 있습니다.** 레이블이 없는 모델을 작성하려고 하면 오류가 발생합니다.

모델 작성 작업을 사용하면 최대 100개의 학습된 모델을 단일 모델 ID에 할당할 수 있습니다. 작성된 모델 ID를 사용하여 분석을 호출하면 Form Recognizer는 먼저 사용자가 제출한 양식을 분류하고, 할당된 모델 중 가장 정확하게 일치하는 모델을 선택하고, 해당 모델에 대한 결과를 반환합니다. 이 작업은 들어오는 양식이 여러 템플릿 중 하나에 속할 수 있을 때 유용합니다.

Form Recognizer 샘플 레이블 지정 도구, REST API 또는 클라이언트 라이브러리 SDK를 사용하는 경우 다음 단계에 따라 구성형 모델을 설정합니다.

1. [**사용자 지정 모델 ID 수집**](#gather-your-custom-model-ids)
1. [**사용자 지정 모델 작성**](#compose-your-custom-models)

#### <a name="gather-your-custom-model-ids"></a>사용자 지정 모델 ID 수집

학습 프로세스가 성공적으로 완료되면 사용자 지정 모델에 모델 ID가 할당됩니다. 다음과 같이 모델 ID를 검색할 수 있습니다.

### <a name="form-recognizer-sample-labeling-tool"></a>[**Form Recognizer 샘플 레이블 지정 도구**](#tab/fott)

[**Form Recognizer 샘플 레이블 지정 도구**](https://fott-2-1.azurewebsites.net/)를 사용하여 모델을 학습시키는 경우 모델 ID는 학습 결과 창에 있습니다.

:::image type="content" source="media/fott-training-results.png" alt-text="스크린샷: 학습 결과 창":::

### <a name="rest-api"></a>[**REST API**](#tab/rest-api)

[**REST API**](./quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#train-a-custom-model)는 **위치** 헤더가 포함된 `201 (Success)` 응답을 받게 됩니다. 이 헤더의 마지막 매개 변수 값은 새로 학습된 모델의 모델 ID입니다.

:::image type="content" source="media/model-id.png" alt-text="스크린샷: 모델 ID를 포함하고 있는 반환된 위치 헤더":::

### <a name="client-library-sdks"></a>[**클라이언트 라이브러리 SDK**](#tab/sdks)

 [**클라이언트 라이브러리 SDK**](./quickstarts/try-sdk-rest-api.md?pivots=programming-language-csharp#train-a-custom-model)는 학습된 모델 ID를 반환하도록 쿼리할 수 있는 모델 개체를 반환합니다.

* C\#  | [CustomFormModel 클래스](/dotnet/api/azure.ai.formrecognizer.training.customformmodel?view=azure-dotnet&preserve-view=true#properties "Azure SDK for .NET")

* Java | [CustomFormModelInfo 클래스](/java/api/com.azure.ai.formrecognizer.training.models.customformmodelinfo?view=azure-java-stable&preserve-view=true#methods "Java용 Azure SDK")

* JavaScript | [CustomFormModelInfo 인스턴스](/javascript/api/@azure/ai-form-recognizer/customformmodelinfo?view=azure-node-latest&preserve-view=true&branch=main#properties "JavaScript용 Azure SDK")

* Python | [CustomFormModelInfo 클래스](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.customformmodelinfo?view=azure-python&preserve-view=true&branch=main#variables "Python용 Azure SDK")

---

#### <a name="compose-your-custom-models"></a>사용자 지정 모델 작성

단일 양식 유형에 해당하는 사용자 지정 모델을 수집한 후에는 사용자 지정 모델을 단일 모델로 구성할 수 있습니다.

### <a name="form-recognizer-sample-labeling-tool"></a>[**Form Recognizer 샘플 레이블 지정 도구**](#tab/fott)

**샘플 레이블 지정 도구** 를 사용하면 신속하게 모델 학습을 시작하여 단일 모델 ID로 작성할 수 있습니다.

학습을 완료한 후에는 다음과 같이 모델을 작성합니다.

1. 왼쪽 레일 메뉴에서 **모델 작성 아이콘**(병합 화살표)을 선택합니다.

1. 주 창에서 단일 모델 ID에 할당하려는 모델을 선택합니다. 화살표 아이콘이 있는 모델은 이미 작성된 모델입니다.

1. 왼쪽 위 모서리에서 **작성 단추** 를 선택합니다.

1. 팝업 창에서 새로 작성된 모델의 이름을 지정하고 **작성** 을 선택합니다.

작업이 완료되면 새로 작성된 모델이 목록에 표시됩니다.

  :::image type="content" source="media/custom-model-compose.png" alt-text="스크린샷: 모델 작성 창" lightbox="media/custom-model-compose-expanded.png":::

### <a name="rest-api"></a>[**REST API**](#tab/rest-api)

**REST API** 를 사용하면 [**사용자 지정 모델 작성**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/Compose) 요청을 만들어 기존 모델에서 단일 구성형 모델을 만들 수 있습니다. 요청 본문에는 작성하려는 `modelIds`의 문자열 배열이 필요하며, 필요에 따라 `modelName`을 정의할 수 있습니다.  [비동기 모델 작성](/rest/api/formrecognizer/2.1preview2/compose-custom-models-async/compose-custom-models-async)을 *참조* 하세요.

### <a name="client-library-sdks"></a>[**클라이언트 라이브러리 SDK**](#tab/sdks)

원하는 프로그래밍 언어 코드를 사용하여 단일 모델 ID로 호출되는 구성형 모델을 만듭니다. 아래는 기존 사용자 지정 모델에서 구성형 모델을 만드는 방법을 보여주는 코드 샘플의 링크입니다.

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample_ModelCompose.md)

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/administration/CreateComposedModel.java)

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/createComposedModel.js)

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/v3.2-beta/sample_create_composed_model.py)

---

## <a name="analyze-documents-with-your-custom-or-composed-model"></a>사용자 지정 모델 또는 구성형 모델로 문서 분석

 사용자 지정 양식 **분석** 작업을 수행하려면 Form Recognizer 호출에서 를 제공해야 `modelID`  합니다. `modelID` 매개 변수에 대한 단일 사용자 지정 모델 ID 또는 구성형 모델 ID를 제공하면 됩니다.

### <a name="form-recognizer-sample-labeling-tool"></a>[**Form Recognizer 샘플 레이블 지정 도구**](#tab/fott)

1. 도구의 왼쪽 창 메뉴에서 **분석 아이콘**(전구)을 선택합니다.

1. 분석할 로컬 파일 또는 이미지 URL을 선택합니다.

1. **분석 실행** 단추를 선택합니다.

1. 이 도구는 경계 상자에 태그를 적용하고 각 태그의 신뢰도를 보고합니다.

:::image type="content" source="media/analyze.png" alt-text="스크린샷: Form Recognizer 도구 사용자 지정 양식 분석 창":::

### <a name="rest-api"></a>[**REST API**](#tab/rest-api)

REST API를 사용하면 문서를 분석하고 키-값 쌍 및 테이블 데이터를 추출하는 [양식 분석](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) 요청을 수행할 수 있습니다.

### <a name="client-library-sdks"></a>[**클라이언트 라이브러리 SDK**](#tab/sdks)

원하는 프로그래밍 언어를 사용하여 사용자 지정 모델 또는 구성형 모델로 양식이나 문서를 분석합니다. Form Recognizer 엔드포인트, API 키 및 모델 ID가 필요합니다.

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample_ModelCompose.md)

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/AnalyzeCustomDocumentFromUrl.java)

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/recognizeCustomForm.js)

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/v3.1/sample_recognize_custom_forms.py)

---

학습 데이터 세트에 포함되지 않은 [분석 양식](./quickstarts/try-sdk-rest-api.md#analyze-forms-with-a-custom-model)을 사용하여 새로 학습된 모델을 테스트합니다. 보고된 정확도에 따라 모델을 향상시키기 위해 추가 학습을 수행하는 것이 좋습니다. 추가 학습을 계속 진행하여 [결과를 개선](label-tool.md#improve-results)할 수 있습니다.

## <a name="manage-your-custom-models"></a>사용자 지정 모델 관리

사용자 지정 모델의 수명 주기 내에는 언제든지 구독 중인 [모든 사용자 지정 모델의 목록](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModels)을 보거나, [특정 사용자 지정 모델](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel)에 대한 정보를 검색하거나, 계정에서 [사용자 지정 모델](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/DeleteCustomModel)을 삭제하여 [사용자 지정 모델을 관리](./quickstarts/try-sdk-rest-api.md#manage-custom-models)할 수 있습니다.

좋습니다! 사용자 지정 모델 및 구성형 모델을 만들어 Form Recognizer 프로젝트 및 애플리케이션에 사용하는 단계를 배웠습니다.

## <a name="next-steps"></a>다음 단계

API 참조 문서를 검색하여 Form Recognizer 클라이언트 라이브러리에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Form Recognizer API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
>