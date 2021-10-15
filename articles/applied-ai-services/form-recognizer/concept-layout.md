---
title: 레이아웃 - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Form Recognizer API 사용 및 제한으로 레이아웃 분석과 관련된 개념을 알아봅니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 913eb155820832936295a6cc9df6a22175a9bec5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041978"
---
# <a name="form-recognizer-layout-model"></a>폼 인식기 레이아웃 모델

Azure 양식 인식기 레이아웃 API는 문서 (PDF, TIFF) 및 이미지 (JPG, PNG, BMP)에서 텍스트, 테이블, 선택 표시 및 구조 정보를 추출 합니다. 이를 통해 고객은 다양 한 형식으로 문서를 가져와서 문서의 구조화 된 데이터 표현을 반환할 수 있습니다. 이 버전은 강력한 [OCR(광학 문자 인식)](../../cognitive-services/computer-vision/overview-ocr.md) 기능을 딥 러닝 모델과 결합하여 텍스트, 테이블, 선택 표시 및 문서 구조를 추출합니다.

##### <a name="sample-form-processed-with-form-recognizer-sample-labeling-tool--layout-feature"></a>[양식 인식기 샘플 레이블 도구](https://fott-2-1.azurewebsites.net/) 레이아웃 기능으로 처리 된 샘플 양식:

:::image type="content" source="media/layout-demo.gif" alt-text="{대체 텍스트}":::

**데이터 추출 기능**

| **레이아웃 모델**   | **텍스트 추출**   | **선택 표시**   | **테이블**  |
| --- | --- | --- | --- |
| Layout  | ✓  | ✓  | ✓  |

## <a name="try-form-recognizer-studio-preview"></a>양식 인식기 스튜디오 체험 (미리 보기)

* 양식 인식기 스튜디오는 preview (v 3.0) API에서 사용할 수 있습니다.

* 양식 인식기 스튜디오 레이아웃 기능을 사용 하 여 폼과 문서에서 테이블, 확인란 및 텍스트를 추출 합니다.

> [!div class="nextstepaction"]
> [폼 인식기 스튜디오 체험](https://formrecognizer.appliedai.azure.com/studio/layout)

## <a name="try-form-recognizer-sample-labeling-tool"></a>폼 인식기 샘플 레이블 지정 도구 시도

샘플 레이블 지정 도구를 사용해 서 레이아웃 데이터를 추출 하는 방법을 볼 수 있습니다. 다음이 필요 합니다.

* Azure 구독- [무료로 하나를 만들](https://azure.microsoft.com/free/cognitive-services/) 수 있습니다.

* Azure Portal의 [폼 인식기 인스턴스입니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) . 무료 가격 책정 계층(`F0`)을 사용하여 서비스를 시도할 수 있습니다. 리소스를 배포한 후 **리소스로 이동** 을 클릭 하 여 API 키 및 끝점을 가져옵니다.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 끝점 위치입니다.":::

* 양식 문서입니다. [샘플 양식 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)를 사용할 수 있습니다.

> [!div class="nextstepaction"]
  > [사용해 보기](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  양식 인식기 UI에서:

  1. **레이아웃 사용을 선택 하 여 텍스트, 테이블 및 선택 표시를 가져옵니다**.
  1. 드롭다운 메뉴에서 **로컬 파일** 을 선택 합니다.
  1. 파일 업로드 하 고 **레이아웃 실행** 선택

  :::image type="content" source="media/try-layout.png" alt-text="스크린샷: 스크린샷: 샘플 레이블 지정 도구 드롭다운 레이아웃 파일 원본 선택 메뉴.":::

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
  * 데이터에는 키와 값이 포함되어야 합니다.
  * 키는 값의 위쪽 또는 왼쪽에 표시되어야 하며, 아래쪽 또는 오른쪽에는 표시될 수 없습니다.

> [!NOTE]
> [샘플 레이블 지정 도구](https://fott-2-1.azurewebsites.net/) 는 BMP 파일 형식을 지원 하지 않습니다. 이는 폼 인식기 서비스가 아닌 도구의 제한 사항입니다.

## <a name="supported-languages-and-locales"></a>지원 되는 언어 및 로캘

 폼 인식기 미리 보기 버전에는 레이아웃 모델에 대 한 추가 언어 지원이 도입 되었습니다. 지원 되는 필기 및 인쇄 된 텍스트의 전체 목록은 [언어 지원](language-support.md#layout-and-custom-model) 을 *참조 하세요* .

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

## <a name="form-recognizer-preview-v30"></a>양식 인식기 미리 보기 v 3.0

 양식 인식기 미리 보기에는 몇 가지 새로운 기능 및 기능이 도입 되었습니다.

* 응용 프로그램 및 워크플로에서 미리 보기 버전을 사용 하는 방법에 대 한 자세한 내용은 [**인식기 v 3.0 마이그레이션 가이드를 참조**](v3-migration-guide.md) 하세요.

* 미리 보기 버전 및 새로운 기능에 대 한 자세한 내용은 [**REST API (미리 보기)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) 를 살펴보세요.

## <a name="next-steps"></a>다음 단계

* 양식 인식기 빠른 시작을 완료 합니다.

  > [!div class="nextstepaction"]
  > [Form Recognizer 빠른 시작](quickstarts/try-sdk-rest-api.md)

* REST API를 살펴보세요.

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)