---
title: 레이아웃 - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Form Recognizer API 사용 및 제한으로 레이아웃 분석과 관련된 개념을 알아봅니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: af45cf3d3f9ae534be52ae617b13c76eb0d2eae8
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567978"
---
# <a name="form-recognizer-layout-service"></a>Form Recognizer 레이아웃 서비스

Azure Form Recognizer의 레이아웃 API는 문서(PDF, TIFF) 및 이미지(JPG, PNG, BMP)에서 텍스트, 테이블, 선택 표시 및 구조 정보를 추출합니다. 이를 통해 고객은 다양한 형식으로 문서를 가져와 문서의 정형 데이터 표현을 반환할 수 있습니다. 이 버전은 강력한 [OCR(광학 문자 인식)](../../cognitive-services/computer-vision/overview-ocr.md) 기능을 딥 러닝 모델과 결합하여 텍스트, 테이블, 선택 표시 및 문서 구조를 추출합니다.

## <a name="what-does-the-layout-service-do"></a>레이아웃 서비스는 어떤 작업을 수행하나요?

레이아웃 API는 문서에서 텍스트, 테이블 머리글이 포함된 테이블, 선택 표시 및 구조 정보를 뛰어난 정확도로 추출하고 구성되고 구조화된 JSON 응답을 반환합니다. 문서는 전화를 캡처한 이미지, 스캔한 문서, 디지털 PDF 등 형식과 품질이 다양할 수 있습니다. 레이아웃 API는 이러한 모든 문서로부터 구조적 출력을 정확하게 추출합니다.

![레이아웃 예제](./media/layout-demo.gif)

## <a name="try-it&quot;></a>시도

Form Recognizer 레이아웃 서비스를 사용해보려면 다음과 같은 온라인 샘플 UI 도구로 이동합니다.

> [!div class=&quot;nextstepaction&quot;]
> [레이아웃 모델 사용해 보기](https://aka.ms/fott-2.1-ga &quot;미리 빌드된 모델 레이아웃부터 시작하여 양식에서 데이터를 추출합니다.")

Form Recognizer 레이아웃 API를 사용해보려면 Azure 구독([무료로 하나 생성](https://azure.microsoft.com/free/cognitive-services))과 [Form Recognizer 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 엔드포인트 및 키가 필요합니다.

![샘플 UI 스크린샷. 문서의 텍스트, 테이블 및 선택 표시가 분석됩니다.](./media/analyze-layout.png)

## <a name="input-requirements"></a>입력 요구 사항

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="analyze-layout"></a>레이아웃 분석

먼저 [레이아웃 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync) 작업을 호출합니다. 레이아웃 분석은 문서(이미지, TIFF 또는 PDF 파일)를 입력으로 사용하고 문서의 텍스트, 테이블, 선택 표시 및 구조를 추출합니다. 호출은 `Operation-Location`이라는 응답 헤더 필드를 반환합니다. `Operation-Location` 값은 다음 단계에서 사용할 결과 ID를 포함하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1/layout/analyzeResults/{resultId}' |

## <a name="get-analyze-layout-result"></a>레이아웃 분석 결과 가져오기

두 번째 단계는 [레이아웃 분석 결과 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeLayoutResult) 작업을 호출하는 것입니다. 이 작업은 레이아웃 분석 작업으로 만들어진 결과 ID를 입력으로 사용합니다. 다음과 같은 가능한 값을 가진 **상태** 필드가 포함된 JSON 응답을 반환합니다.

|필드| 형식 | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | `notStarted`: 분석 작업이 시작되지 않았습니다.<br /><br />`running`: 분석 작업이 진행 중입니다.<br /><br />`failed`: 분석 작업이 실패했습니다.<br /><br />`succeeded`: 분석 작업이 성공했습니다.|

`succeeded` 값이 반환될 때까지 이 작업을 반복적으로 호출합니다. 3~5초의 간격을 사용하여 RPS(초당 요청 수) 속도를 초과하지 않도록 합니다.

**상태** 필드에 `succeeded` 값이 있는 경우 JSON 응답은 추출된 레이아웃, 텍스트, 테이블 및 선택 표시를 포함합니다. 추출된 데이터에는 추출된 텍스트 줄과 단어, 경계 상자, 필기 표시가 있는 텍스트 모양, 테이블 및 선택/선택 취소를 나타내는 선택 표시가 포함됩니다.

## <a name="sample-json-output"></a>JSON 출력 샘플

*레이아웃 분석 결과 가져오기* 작업에 대한 응답은 모든 정보가 추출된 문서를 구조화된 방식으로 표현한 것입니다.
[샘플 문서 파일](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) 및 해당 구조화된 출력 [샘플 레이아웃 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)은 여기를 참조하세요.

JSON 출력에는 다음 두 부분이 있습니다.

* `readResults` 노드에는 인식된 모든 텍스트와 선택 표시가 포함됩니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다.
* `pageResults` 노드에는 ‘readResults’의 줄과 단어에 대한 참조, 경계 상자, 신뢰도와 함께 추출된 테이블과 셀이 포함됩니다.

## <a name="features"></a>기능

### <a name="tables-and-table-headers"></a>테이블 및 테이블 헤더

레이아웃 API는 JSON 출력의 `pageResults` 섹션에서 테이블을 추출합니다. 문서를 스캔, 사진화 또는 디지털화할 수 있습니다. 테이블은 병합된 셀 또는 열, 테두리가 있거나 없는 상태, 홀수 각도를 사용하여 복잡하게 지정할 수 있습니다. 추출된 테이블 정보에는 열 및 행 수, 행 범위 및 열 범위가 포함됩니다. 경계 상자가 있는 각 셀은 머리글의 일부로 인식되는지 여부에 관계없이 정보와 함께 출력됩니다. 모델 예측 머리글 셀은 여러 행에 걸쳐 있으며 테이블의 첫 번째 행이 아닐 수도 있습니다. 회전된 테이블에서도 작동합니다. 각 테이블 셀에는 `readResults` 섹션의 개별 단어에 대한 참조가 포함된 전체 텍스트도 포함되어 있습니다.

:::image type="content" source="./media/layout-table-headers-example.png" alt-text="레이아웃 테이블 헤더 출력":::

### <a name="selection-marks"></a>선택 표시

또한 레이아웃 API는 문서에서 선택 표시를 추출합니다. 추출된 선택 표시에는 경계 상자, 신뢰도 및 상태(선택/선택 취소)가 포함됩니다. 선택 표시 정보는 JSON 출력의 `readResults` 섹션에서 추출됩니다.

:::image type="content" source="./media/layout-selection-marks.png" alt-text="레이아웃 선택 표시 출력":::

### <a name="text-lines-and-words"></a>텍스트 줄 및 단어

레이아웃 API는 문서 및 이미지에서 여러 텍스트 각도 및 색이 지정된 텍스트를 추출합니다. 문서, 팩스, 인쇄 및/또는 필기(영어 전용) 텍스트 및 혼합 모드의 사진을 허용합니다. 텍스트는 줄, 단어, 경계 상자, 신뢰도 점수 및 스타일(필기 또는 기타)에 대해 제공된 정보와 함께 추출됩니다. 모든 텍스트 정보는 JSON 출력의 `readResults` 섹션에 포함됩니다.

:::image type="content" source="./media/layout-text-extraction.png" alt-text="레이아웃 텍스트 추출 출력":::

### <a name="natural-reading-order-for-text-lines-latin-only"></a>텍스트 줄의 자연스러운 읽기 순서(라티어만 해당)

`readingOrder` 쿼리 매개 변수를 사용하여 텍스트 줄이 출력되는 순서를 지정할 수 있습니다. 다음 예제와 같이 인간 친화적인 읽기 순서 출력을 위해 `natural`을 사용합니다. 이 기능은 라틴어에 대해서만 지원됩니다.

:::image type="content" source="./media/layout-reading-order-example.png" alt-text="레이아웃 읽기 순서 예제" lightbox="../../cognitive-services/Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="handwritten-classification-for-text-lines-latin-only"></a>텍스트 줄에 대한 필기 분류(라틴어에만 해당)

응답에는 신뢰도 점수와 함께 각 텍스트 줄이 필기 스타일인지 여부를 분류하는 것이 포함됩니다. 이 기능은 라틴어에 대해서만 지원됩니다. 다음 예제에서는 이미지의 텍스트에 대한 필기 분류를 보여 줍니다.

:::image type="content" source="./media/layout-handwriting-classification.png" alt-text="필기 분류 예제":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>텍스트 추출을 위한 페이지 번호 또는 범위 선택

대규모의 다중 페이지 문서의 경우 `pages` 쿼리 매개 변수를 사용하여 텍스트 추출을 위한 특정 페이지 번호 또는 페이지 범위를 지정합니다. 다음 예제에서는 모든 페이지(1-10) 및 선택한 페이지(3-6) 모두에 대한 텍스트를 포함하는 10개의 페이지가 있는 문서를 보여 줍니다.

:::image type="content" source="./media/layout-select-pages-for-text.png" alt-text="선택한 페이지 출력 레이아웃":::

## <a name="next-steps"></a>다음 단계

* [Form Recognizer 샘플 UI 도구](https://aka.ms/fott-2.1-ga)를 사용하여 사용자 고유의 레이아웃 추출 시도
* 사용자가 선택한 개발 언어로 레이아웃 추출을 시작하려면 [Form Recognizer 빠른 시작](quickstarts/client-library.md#analyze-layout)을 완료하세요.

## <a name="see-also"></a>참조

* [Form Recognizer란?](./overview.md)
* [REST API 참조 문서](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)
