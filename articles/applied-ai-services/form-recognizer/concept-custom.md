---
title: 사용자 지정 및 구성된 모델 Form Recognizer
titleSuffix: Azure Applied AI Services
description: 사용자 지정 및 구성된 모델을 만들고, 사용하고, Form Recognizer 관리하는 방법을 알아봅니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/16/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: b347a7fca61ce6ec0e9cbc960f6b1811702b4154
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265716"
---
# <a name="form-recognizer-custom-and-composed-models"></a>Form Recognizer 사용자 지정 모델 및 구성형 모델

Form Recognizer는 고급 기계 학습 기술을 사용하여 문서 이미지에서 정보를 탐지하여 추출하고, 추출된 데이터를 정형 JSON 출력으로 반환합니다. Form Recognizer 사용하면 독립 실행형 사용자 지정 모델을 학습하거나 사용자 지정 모델을 결합하여 구성된 모델을 만들 수 있습니다.

* **사용자 지정 모델**. Form Recognizer 사용자 지정 모델을 사용하면 비즈니스와 관련된 양식 및 문서의 데이터를 분석하고 추출할 수 있습니다. 사용자 지정 모델은 고객의 고유한 데이터 및 사용 사례를 학습합니다.

* **구성형 모델**. 구성형 모델은 사용자 지정 모델 컬렉션을 가져와서 사용자의 양식 유형을 포함하는 단일 모델에 할당하여 만듭니다. 구성형 모델에 문서가 제출되면 이 서비스에서는 분류 단계를 수행하여 분석용으로 제공된 양식을 정확히 나타내는 사용자 지정 모델을 결정합니다.

:::image type="content" source="media/analyze-studio.png" alt-text="스크린샷: Form Recognizer 도구 사용자 지정 양식 분석 창":::

## <a name="what-is-a-custom-model"></a>사용자 지정 모델이란?

사용자 지정 모델은 고객의 고유한 콘텐츠 내의 양식 필드를 인식하고 키-값 쌍 및 테이블 데이터를 추출하도록 학습된 기계 학습 프로그램입니다. 동일한 양식 유형 예제 5가지만 있으면 시작할 수 있으며, 사용자 지정 모델은 레이블이 지정된 데이터 세트를 사용하여 또는 사용하지 않고 학습시킬 수 있습니다.

## <a name="what-is-a-composed-model"></a>구성형 모델이란?

구성형 모델을 선택하면 단일 모델 ID로 호출된 구성형 모델에 여러 사용자 지정 모델을 할당할 수 있습니다. 이 방법은 여러 모델을 학습시키고 비슷한 양식 유형을 분석하도록 그룹화하려는 경우에 유용합니다. 예를 들어 구성된 모델에는 공급, 장비 및 구매자 구매 주문을 분석하도록 학습된 사용자 지정 모델이 포함될 수 있습니다. 적절한 모델을 수동으로 선택하는 대신, 구성형 모델을 사용하여 각 분석 및 추출에 적합한 사용자 지정 모델을 결정할 수 있습니다.

## <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio(미리 보기)

* Form Recognizer Studio는 미리 보기(v3.0) API와 함께 사용할 수 있습니다.

* Form Recognizer Studio 사용자 지정 양식 기능을 사용하여 특정 또는 고유한 형식의 양식을 분석합니다.

> [!div class="nextstepaction"]
> [Form Recognizer Studio 사용해 보세요.](https://formrecognizer.appliedai.azure.com/studio/customform/projects)

## <a name="sample-labeling-tool"></a>샘플 레이블 지정 도구

샘플 레이블 지정 도구를 사용하여 사용자 지정 양식에서 데이터를 추출하는 방법을 확인할 수 있습니다. 다음이 필요합니다.

* Azure 구독 - [체험 구독을 만들](https://azure.microsoft.com/free/cognitive-services/) 수 있습니다.

* [Azure Portal Form Recognizer 인스턴스입니다.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 무료 가격 책정 계층(`F0`)을 사용하여 서비스를 시도할 수 있습니다. 리소스가 배포된 후 **리소스로 이동을** 클릭하여 API 키와 엔드포인트를 얻습니다.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

> [!div class="nextstepaction"]
> [사용해 보세요.](https://fott-2-1.azurewebsites.net/projects/create)

Form Recognizer UI에서 다음을 수행합니다.

1. 사용자 지정 사용을 선택하여 **레이블이 있는 모델을 학습시키고 키 값 쌍을 얻습니다.**

      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="스크린샷: 사용자 지정 옵션의 FOTTtool 선택":::

1. 다음 창에서 **새 프로젝트** 를 선택합니다.

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="스크린샷: FOTTtools에서 새 프로젝트를 선택합니다.":::

## <a name="input-requirements"></a>입력 요구 사항

* 최상의 결과를 위해 문서당 하나의 명확한 사진 또는 고품질 스캔을 제공합니다.
* 지원되는 파일 형식: JPEG, PNG, BMP, TIFF 및 PDF(텍스트 포함 또는 검사). 텍스트 포함 PDF는 문자 추출 및 위치에 오류가 발생할 가능성을 제거하는 데 가장 좋습니다.
* PDF 및 TIFF의 경우 최대 2000페이지를 처리할 수 있습니다(무료 계층 구독의 경우 처음 2페이지만 처리됨).
* 파일 크기는 50MB 미만이어야 합니다.
* 이미지 크기는 50x50 픽셀에서 10,000x10,000 픽셀 사이여야 합니다.
* PDF 크기는 Legal 또는 A3 용지 크기 이하에 해당하는 최대 17 x 17인치입니다.
* 학습 데이터 세트의 총 크기는 500페이지 이하입니다.
* PDF가 암호로 잠긴 경우에는 제출하기 전에 잠금을 해제해야 합니다.
* 자율 학습(레이블이 지정된 데이터 제외)의 경우:
  * 데이터는 키와 값을 포함해야 합니다.
  * 키는 값의 위 또는 왼쪽에 나타나야 합니다. 아래 또는 오른쪽에 표시할 수 없습니다.

  > [!TIP]
  > **학습 데이터**
  >
  >* 가능하면 이미지 기반 문서 대신 텍스트 기반 PDF 문서를 사용합니다. 스캔한 PDF는 이미지로 처리됩니다.
  > * 채워진 양식의 경우 모든 필드가 채워진 예제를 사용합니다.
  > * 각 필드에 서로 다른 값이 있는 양식을 사용합니다.
  >* 양식 이미지의 품질이 낮은 경우 더 큰 데이터 세트(예: 10~15개 이미지)를 사용합니다.

> [!NOTE]
> [샘플 레이블 지정 도구는](https://fott-2-1.azurewebsites.net/) BMP 파일 형식을 지원하지 않습니다. 이는 Form Recognizer 서비스가 아닌 도구의 제한 사항입니다.

## <a name="supported-languages-and-locales"></a>지원되는 언어 및 로케일

 Form Recognizer 미리 보기 버전에는 사용자 지정 모델에 대한 추가 언어 지원이 도입되었습니다. 지원되는 필기 및 인쇄 텍스트의 전체 목록은 [언어 지원을](language-support.md#layout-and-custom-model) *참조하세요.*

## <a name="form-recognizer-preview-v30"></a>Form Recognizer 미리 보기 v3.0

 Form Recognizer v3.0(미리 보기)에는 다음과 같은 몇 가지 새로운 기능이 도입되었습니다.

* **사용자 지정 모델 API(v3.0)는** 사용자 지정 양식에 대한 서명 검색을 지원합니다. 사용자 지정 모델을 학습할 때 특정 필드를 서명으로 지정할 수 있습니다.  사용자 지정 모델을 통해 문서를 분석하면 서명이 검색되었는지 여부를 나타냅니다.

* Form Recognizer [**v3.0 마이그레이션 가이드에**](v3-migration-guide.md) 따라 애플리케이션 및 워크플로에서 미리 보기 버전을 사용하는 방법을 알아봅니다.

* 미리 보기 버전 및 새로운 기능에 대해 자세히 알아보려면 [**REST API(미리 보기)를**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) 살펴보세요.

### <a name="try-signature-detection"></a>서명 검색 시도

1. 학습 데이터 집합을 빌드합니다.

1. [Form Recognizer Studio로](https://formrecognizer.appliedai.azure.com/studio) 이동하고 사용자 지정 모델에서 **사용자 지정 양식을** 선택합니다.

    :::image type="content" source="media/label-tool/select-custom-form.png" alt-text="스크린샷: Form Recognizer Studio에서 사용자 지정 양식을 선택합니다.":::

1. 워크플로에 따라 새 프로젝트를 만듭니다.

1. 사용자 지정 모델 입력 요구 사항을 따릅니다.

1. **Signature** 형식의 레이블을 만듭니다.

1. 문서에 레이블을 지정합니다. 서명 필드의 경우 정확도를 높이기 위해 지역 레이블 지정을 사용하는 것이 좋습니다.

    :::image type="content" source="media/label-tool/signature-label-region-too.png" alt-text="스크린샷: 레이블 서명 필드.":::

학습 세트에 레이블이 지정되면 사용자 지정 모델을 학습하고 이를 사용하여 문서를 분석할 수 있습니다. 서명 필드는 서명이 검색되었는지 여부를 지정합니다.

## <a name="next-steps"></a>다음 단계

* Form Recognizer 빠른 시작 완료:

  > [!div class="nextstepaction"]
  > [Form Recognizer 빠른 시작](quickstarts/try-sdk-rest-api.md)

* REST API 살펴보세요.

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
