---
title: 폼 인식기 모델
titleSuffix: Azure Applied AI Services
description: 미리 작성 한 모델을 사용 하 여 데이터 추출 및 분석을 포괄 하는 개념
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: f2e2ef59d4c3608065edab4ffd1d1ec55122f2ad
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754618"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-models"></a>폼 인식기 모델

 Azure 양식 인식기 미리 빌드된 모델을 사용 하면 사용자 고유의 모델을 학습 하 고 빌드하지 않고도 앱 및 흐름에 지능형 양식 처리를 추가할 수 있습니다. 미리 작성 된 모델은 심층 학습 모델과 함께 사용 되는 OCR (광학 문자 인식)을 사용 하 여 특정 양식 및 문서 유형에 공통 된 미리 정의 된 텍스트 및 데이터 필드를 식별 하 고 추출 합니다. 폼 인식기 추출은 폼과 문서 데이터를 분석 한 다음 구성 된 구조적 JSON 응답을 반환 합니다. 양식 인식기 v 2.1은 송장, 수신, ID 문서 및 비즈니스 카드 모델을 지원 합니다.

## <a name="model-overview"></a>모델 개요

| **모델**   | **설명**   |
| --- | --- |
| 🆕[일반 문서 (미리 보기)](#general-document-preview) | 텍스트, 테이블, 구조, 키-값 쌍 및 명명 된 엔터티를 추출 합니다.  |
| [레이아웃](#layout)  | 문서에서 텍스트 및 레이아웃 정보를 추출 합니다.  |
| [청구서](#invoice)  | 영어 청구서에서 키 정보를 추출 합니다.  |
| [Receipt](#receipt)  | 영어 영수증에서 키 정보를 추출 합니다.  |
| [ID 문서](#id-document)  | 미국 드라이버 라이선스 및 국제 여권에서 핵심 정보를 추출 합니다.  |
| [명함](#business-card)  | 영어 명함에서 키 정보를 추출 합니다.  |
| [사용자 지정](#custom) |  비즈니스에 특정 한 양식 및 문서에서 데이터를 추출 합니다. 사용자 지정 모델은 고객의 고유한 데이터 및 사용 사례를 학습합니다. |

### <a name="general-document-preview"></a>일반 문서 (미리 보기)

* 일반 문서 API는 대부분의 폼 형식을 지원 하며 문서를 분석 하 고 키와 항목에 값을 연결 하 여 검색 된 테이블에 연결 합니다. 문서에서 일반적인 키-값 쌍을 추출 하는 데 적합 합니다. [레이블 없이 사용자 지정 모델을 학습](compose-custom-models.md#train-without-labels)하는 대신 일반 문서 모델을 사용할 수 있습니다.

* 일반 문서는 미리 학습 된 모델 이며 REST API를 통해 직접 호출할 수 있습니다.

* 일반 문서 모델은 여러 엔터티 범주에 대해 명명 된 엔터티 인식 (NER)을 지원 합니다. NER은 텍스트에서 다양 한 엔터티를 식별 하 여 사용자, 위치, 이벤트, 제품, 조직 등의 미리 정의 된 클래스 또는 형식으로 분류 하는 기능입니다. 엔터티 추출은 추출 된 값의 유효성을 검사 하려는 시나리오에서 유용할 수 있습니다. 엔터티는 전체 콘텐츠에서 추출 됩니다.

##### <a name="sample-document-processed-in-the-form-recognizer-studio"></a>[인식기 Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)에서 처리 되는 샘플 문서는 다음과 같습니다.

:::image type="content" source="media/general-document-analyze.png" alt-text="스크린샷: 폼 인식기 스튜디오의 일반 문서 분석":::

> [!div class="nextstepaction"]
> [자세한 정보: 일반 문서 모델](concept-general-document.md)

### <a name="layout"></a>Layout

레이아웃 API는 문서에서 텍스트, 테이블 및 머리글, 선택 표시 및 구조 정보를 분석 하 고 추출 하며 구조화 된 JSON 데이터 표현을 반환 합니다.

##### <a name="sample-form-processed-with-form-recognizer-sample-labeling-tool--layout-feature"></a>[양식 인식기 샘플 레이블 도구](https://fott-2-1.azurewebsites.net/) 레이아웃 기능으로 처리 된 샘플 양식:

:::image type="content" source="media/overview-layout.png" alt-text="{대체 텍스트}":::

> [!div class="nextstepaction"]
> [자세한 정보: 모델 레이아웃](concept-layout.md)

### <a name="invoice"></a>청구서

송장 모델은 판매 청구서에서 주요 정보를 분석 하 고 추출 합니다. API는 다양 한 형식으로 송장을 분석 합니다. 고객 이름, 청구 주소, 기한 및 금액 만료와 같은 주요 정보를 추출 합니다. 및는 구조화 된 JSON 데이터 표현을 반환 합니다.

##### <a name="sample-invoice-processed-with-form-recognizer-sample-labeling-tool"></a>[양식 인식기 샘플 레이블 도구](https://fott-2-1.azurewebsites.net/)를 사용 하 여 처리 된 샘플 송장:

:::image type="content" source="./media/overview-invoices.jpg" alt-text="샘플 송장" lightbox="./media/overview-invoices.jpg":::

> [!div class="nextstepaction"]
> [자세한 정보: 송장 모델](concept-invoice.md)

### <a name="receipt"></a>Receipt

수신 모델은 판매 영수증에서 주요 정보를 분석 하 고 추출 합니다. API는 인쇄 및 필기 확인을 분석 합니다. 판매자 이름, 판매자 전화 번호, 거래 날짜, 세금 및 트랜잭션 합계와 같은 주요 정보를 추출 합니다. 및는 구조화 된 JSON 데이터 표현을 반환 합니다.

##### <a name="sample-receipt-processed-with-form-recognizer-sample-labeling-tool"></a>[양식 인식기 샘플 레이블 도구](https://fott-2-1.azurewebsites.net/)를 사용 하 여 처리 된 샘플 확인:

:::image type="content" source="./media/overview-receipt.jpg" alt-text="샘플 영수증" lightbox="./media/overview-receipt.jpg":::

> [!div class="nextstepaction"]
> [자세한 정보: 수신 모델](concept-receipt.md)

### <a name="id-document"></a>ID 문서

ID 문서 모델은 미국 드라이버 라이선스 (특별구의 모든 50 상태 및 지역) 및 국제 passport biographical 페이지 (상대 및 기타 여행 문서 제외)에서 주요 정보를 분석 하 고 추출 합니다. API는 id 문서를 분석 합니다. 이름, 성, 주소, 생년월일 등의 주요 정보를 추출 합니다. 및는 구조화 된 JSON 데이터 표현을 반환 합니다.

##### <a name="sample-us-drivers-license-processed-with-form-recognizer-sample-labeling-tool"></a>[양식 인식기 샘플 레이블 도구](https://fott-2-1.azurewebsites.net/)를 사용 하 여 처리 되는 샘플 미국 드라이버 라이선스:

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="샘플 식별 카드" lightbox="./media/overview-id.jpg":::

> [!div class="nextstepaction"]
> [자세한 정보: id 문서 모델](concept-id-document.md)

### <a name="business-card"></a>명함

비즈니스 카드 모델은 비즈니스 카드 이미지에서 키 정보를 분석 하 고 추출 합니다. API는 인쇄 된 명함을 분석 합니다. 이름, 성, 회사 이름, 전자 메일 주소 및 전화 번호와 같은 주요 정보를 추출 합니다.  및는 구조화 된 JSON 데이터 표현을 반환 합니다.

##### <a name="sample-business-card-processed-with-form-recognizer-sample-labeling-tool"></a>[양식 인식기 샘플 레이블 도구](https://fott-2-1.azurewebsites.net/)를 사용 하 여 처리 되는 샘플 비즈니스 카드:

:::image type="content" source="./media/overview-business-card.jpg" alt-text="샘플 명함" lightbox="./media/overview-business-card.jpg":::

> [!div class="nextstepaction"]
> [자세한 정보: 비즈니스 카드 모델](concept-business-card.md)

### <a name="custom"></a>사용자 지정

사용자 지정 모델은 사용자의 비즈니스와 관련 된 양식과 문서에서 데이터를 분석 하 고 추출 합니다. API는 고유한 내용 내의 양식 필드를 인식 하 고 키-값 쌍 및 테이블 데이터를 추출 하도록 학습 된 기계 학습 프로그램입니다. 동일한 양식 유형 예제 5가지만 있으면 시작할 수 있으며, 사용자 지정 모델은 레이블이 지정된 데이터 세트를 사용하여 또는 사용하지 않고 학습시킬 수 있습니다.

##### <a name="sample-custom-form-processed-with-form-recognizer-sample-labeling-tool"></a>[양식 인식기 샘플 레이블 도구](https://fott-2-1.azurewebsites.net/)를 사용 하 여 처리 된 샘플 사용자 지정 양식:

:::image type="content" source="media/analyze.png" alt-text="스크린샷: Form Recognizer 도구 사용자 지정 양식 분석 창":::

> [!div class="nextstepaction"]
> [자세한 정보: 사용자 지정 모델](concept-custom.md)

## <a name="data-extraction"></a>데이터 추출

 | **모델**   | **텍스트 추출** |**키-값 쌍** |**선택 표시**   | **테이블**   |**엔터티** |
  | --- | :---: |:---:| :---: | :---: |:---: |
  |🆕 일반 문서  | ✓  |  ✓ | ✓  | ✓  | ✓  |
  | Layout  | ✓  |   | ✓  | ✓  |   |
  | 청구서  | ✓ | ✓  | ✓  | ✓ ||
  |Receipt  | ✓  |   ✓ |   |  ||
  | ID 문서 | ✓  |   ✓  |   |   ||
  | 명함    | ✓  |   ✓ |   |   ||
  | 사용자 지정             |✓  |  ✓ | ✓  | ✓  | ✓  |

## <a name="input-requirements"></a>입력 요구 사항

* 최상의 결과를 위해 문서 마다 하나의 명확한 사진 또는 고품질 스캔을 제공 합니다.
* 지원되는 파일 형식: JPEG, PNG, BMP, TIFF 및 PDF(텍스트 포함 또는 검사). 텍스트 포함 PDF는 문자 추출 및 위치에 오류가 발생할 가능성을 제거하는 데 가장 좋습니다.
* PDF 및 TIFF의 경우 최대 2000 페이지를 처리할 수 있습니다 (무료 계층 구독이 있는 경우 처음 두 페이지만 처리 됨).
* 파일 크기는 50 MB 미만 이어야 합니다.
* 이미지 크기는 50x50 픽셀에서 10,000x10,000 픽셀 사이여야 합니다.
* PDF 차원은 Legal 또는 A3 용지 크기에 해당 하는 최대 17 x 17 인치입니다.
* 학습 데이터의 총 크기는 500 페이지이 하 여야 합니다.
* Pdf가 암호로 잠겨 있는 경우 제출 하기 전에 잠금을 제거 해야 합니다.
* 자율 learning (레이블이 지정 되지 않은 데이터):
  * 데이터에는 키와 값이 포함 되어야 합니다.
  * 키는 값의 위쪽 이나 왼쪽에 표시 되어야 합니다. 이는 아래 또는 오른쪽에 표시 되지 않습니다.

> [!NOTE]
> [샘플 레이블 지정 도구](https://fott-2-1.azurewebsites.net/) 는 BMP 파일 형식을 지원 하지 않습니다. 이는 폼 인식기 서비스가 아닌 도구의 제한 사항입니다.

## <a name="form-recognizer-preview-v30"></a>양식 인식기 미리 보기 v 3.0

  양식 인식기 v 3.0 (미리 보기)에는 몇 가지 새로운 기능 및 기능이 도입 되었습니다.

* [**일반 문서 (미리 보기)**](concept-general-document.md) 모델은 미리 학습 된 모델을 사용 하 여 폼과 문서에서 텍스트, 테이블, 구조, 키-값 쌍 및 명명 된 엔터티를 추출 하는 새로운 API입니다.
* [**수신 (미리 보기)**](concept-receipt.md) 모델은 단일 페이지 호텔 수령 처리를 지원 합니다.
* [**ID 문서 (미리 보기)**](concept-id-document.md) 모델은 미국 드라이버 라이선스에서 인증, 제한 및 차량 분류 추출을 지원 합니다.
* [**사용자 지정 모델 API (미리 보기)**](concept-custom.md) 는 사용자 지정 폼에 대 한 서명 검색을 지원 합니다.

### <a name="version-migration"></a>버전 마이그레이션

[ **폼 인식기 v 3.0 마이그레이션 가이드** 에 따라 응용 프로그램에서 폼 인식기 v 3.0을 사용 하는 방법에 대해 알아봅니다.](v3-migration-guide.md)

## <a name="next-steps"></a>다음 단계

* [양식 인식기 샘플 도구](https://fott-2-1.azurewebsites.net/) 를 사용 하 여 [사용자 고유의 양식과 문서를 처리 하는 방법을 알아봅니다](quickstarts/try-sample-label-tool.md) .

* [폼 인식기 퀵 스타트](quickstarts/try-sdk-rest-api.md) 를 완료 하 고 원하는 개발 언어로 양식 처리 앱을 만드는 작업을 시작 합니다.
