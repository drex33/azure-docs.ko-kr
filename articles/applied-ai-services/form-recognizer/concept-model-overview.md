---
title: Form Recognize 모델
titleSuffix: Azure Applied AI Services
description: 미리 붙는 모델을 사용한 데이터 추출 및 분석을 포함하는 개념
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ac003f812078f2bb3b27710068b7350468ad8fb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027196"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-models"></a>Form Recognize 모델

 Azure Form Recognizer 미리 빌드된 모델을 사용하면 사용자 고유의 모델을 학습시키고 빌드할 필요 없이 앱 및 흐름에 지능형 양식 처리를 추가할 수 있습니다. 미리 작성된 모델은 딥 러닝 모델과 결합된 OCR(광학 문자 인식)을 사용하여 특정 양식 및 문서 형식에 공통된 미리 정의된 텍스트 및 데이터 필드를 식별하고 추출합니다. Form Recognizer 추출은 양식 및 문서 데이터를 분석한 다음, 구성되고 구조적인 JSON 응답을 반환합니다. Form Recognizer v2.1은 청구서, 영수증, ID 문서 및 명함 모델을 지원합니다.

## <a name="model-overview"></a>모델 개요

| **모델**   | **설명**   |
| --- | --- |
| 🆕 일반[문서(미리 보기)](#general-document-preview) | 텍스트, 테이블, 구조체, 키-값 쌍 및 명명된 엔터티를 추출합니다.  |
| [레이아웃](#layout)  | 문서에서 텍스트 및 레이아웃 정보를 추출합니다.  |
| [청구서](#invoice)  | 영어 청구서에서 주요 정보를 추출합니다.  |
| [Receipt](#receipt)  | 영어 영수증에서 키 정보를 추출합니다.  |
| [ID 문서](#id-document)  | 미국 운전 라이선스 및 국제 Passport에서 주요 정보를 추출합니다.  |
| [명함](#business-card)  | 영어 명함에서 키 정보를 추출합니다.  |
| [사용자 지정](#custom) |  비즈니스와 관련한 양식 및 문서에서 데이터를 추출합니다. 사용자 지정 모델은 고객의 고유한 데이터 및 사용 사례를 학습합니다. |

### <a name="general-document-preview"></a>일반 문서(미리 보기)

:::image type="content" source="media/studio/general-document.png" alt-text="스크린샷: Studio 일반 문서 아이콘.":::

* 일반 문서 API는 대부분의 양식 유형을 지원하며 문서를 분석하고 검색된 테이블에 키 및 항목에 값을 연결합니다. 문서에서 일반적인 키-값 쌍을 추출하는 데 적합합니다. 레이블 없이 사용자 지정 모델을 학습하는 대신 일반 문서 모델을 사용할 수 [있습니다.](compose-custom-models.md#train-without-labels)

* 일반 문서는 미리 학습된 모델이며 REST API 통해 직접 호출할 수 있습니다.

* 일반 문서 모델은 여러 엔터티 범주에 대해 명명된 엔터티 인식(NER)을 지원합니다. NER는 텍스트의 다른 엔터티를 식별하고 사용자, 위치, 이벤트, 제품 및 조직과 같은 미리 정의된 클래스 또는 형식으로 분류하는 기능입니다. 엔터티 추출은 추출된 값의 유효성을 검사하려는 시나리오에서 유용할 수 있습니다. 엔터티는 전체 콘텐츠에서 추출됩니다.

***[Form Recognizer Studio에서](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)처리된 샘플 문서:***

:::image type="content" source="media/studio/general-document-analyze.png" alt-text="스크린샷: Form Recognizer Studio의 일반 문서 분석":::

> [!div class="nextstepaction"]
> [자세한 정보: 일반 문서 모델](concept-general-document.md)

### <a name="layout"></a>Layout

:::image type="content" source="media/studio/layout.png" alt-text="스크린샷: Studio 레이아웃 아이콘.":::

레이아웃 API는 양식 및 문서에서 텍스트, 테이블 및 헤더, 선택 표시 및 구조 정보를 분석하고 추출합니다.

***[Form Recognizer 샘플 레이블 지정 도구](https://fott-2-1.azurewebsites.net/) 레이아웃 기능으로 처리된 샘플 양식:***

:::image type="content" source="media/overview-layout.png" alt-text="스크린샷: Form Recognizer Studio에서 처리된 샘플 문서 분석":::

> [!div class="nextstepaction"]
> [자세한 정보: 레이아웃 모델](concept-layout.md)

### <a name="invoice"></a>청구서

:::image type="content" source="media/studio/invoice.png" alt-text="스크린샷: Studio 청구서 아이콘.":::

청구서 모델은 판매 청구서에서 주요 정보를 분석하고 추출합니다. API는 다양한 형식으로 청구서를 분석하고 고객 이름, 청구 주소, 기한 및 기한과 같은 주요 정보를 추출합니다.

***[Form Recognizer 샘플 레이블 지정 도구를 사용하여](https://fott-2-1.azurewebsites.net/)처리된 샘플 청구서:***

:::image type="content" source="./media/overview-invoices.jpg" alt-text="샘플 송장" lightbox="./media/overview-invoices.jpg":::

> [!div class="nextstepaction"]
> [자세한 정보: 청구서 모델](concept-invoice.md)

### <a name="receipt"></a>Receipt

:::image type="content" source="media/studio/receipt.png" alt-text="스크린샷: Studio 영수증 아이콘.":::

영수증 모델은 판매 영수증에서 주요 정보를 분석하고 추출합니다. API는 인쇄 및 필기 영수증을 분석하고 가맹점 이름, 가맹점 전화 번호, 거래 날짜, 세금 및 거래 합계와 같은 주요 정보를 추출합니다. 

***Form Recognizer 샘플 레이블 지정 도구를 사용하여 처리된 [샘플 영수증:](https://fott-2-1.azurewebsites.net/)***

:::image type="content" source="./media/overview-receipt.jpg" alt-text="샘플 영수증" lightbox="./media/overview-receipt.jpg":::

> [!div class="nextstepaction"]
> [자세한 정보: 영수증 모델](concept-receipt.md)

### <a name="id-document"></a>ID 문서

:::image type="content" source="media/studio/id-document.png" alt-text="스크린샷: Studio ID 문서 아이콘.":::

ID 문서 모델은 미국 운전면허증(모두 50개 주 및 로스앤젤레스 특별구) 및 국제 Passport 구문 페이지(경유지 및 기타 여행 문서 제외)에서 주요 정보를 분석하고 추출합니다. API는 ID 문서를 분석하고 이름, 성, 주소 및 생년월일과 같은 주요 정보를 추출합니다.

***[Form Recognizer 샘플 레이블 지정 도구를](https://fott-2-1.azurewebsites.net/)사용하여 처리된 샘플 미국 드라이버 라이선스:***

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="샘플 식별 카드" lightbox="./media/overview-id.jpg":::

> [!div class="nextstepaction"]
> [자세한 정보: ID 문서 모델](concept-id-document.md)

### <a name="business-card"></a>명함

:::image type="content" source="media/studio/business-card.png" alt-text="스크린샷: Studio 명함 아이콘.":::

비즈니스 카드 모델은 비즈니스 카드 이미지에서 주요 정보를 분석하고 추출합니다. API는 인쇄된 명함 이미지를 분석하고 이름, 성, 회사 이름, 이메일 주소 및 전화 번호와 같은 주요 정보를 추출합니다.

***Form Recognizer 샘플 레이블 [지정 도구를 사용하여](https://fott-2-1.azurewebsites.net/)처리된 샘플 명함:***

:::image type="content" source="./media/overview-business-card.jpg" alt-text="샘플 명함" lightbox="./media/overview-business-card.jpg":::

> [!div class="nextstepaction"]
> [자세한 정보: 명함 모델](concept-business-card.md)

### <a name="custom"></a>사용자 지정

 :::image type="content" source="media/studio/custom.png" alt-text="스크린샷: Studio사상자 아이콘.":::

사용자 지정 모델은 비즈니스에 특정한 양식 및 문서에서 데이터를 분석하고 추출합니다. API는 고객의 고유한 콘텐츠 내의 양식 필드를 인식하고 키-값 쌍 및 테이블 데이터를 추출하도록 학습된 기계 학습 프로그램입니다. 동일한 양식 유형 예제 5가지만 있으면 시작할 수 있으며, 사용자 지정 모델은 레이블이 지정된 데이터 세트를 사용하여 또는 사용하지 않고 학습시킬 수 있습니다.

***Form Recognizer 샘플 레이블 [지정 도구를](https://fott-2-1.azurewebsites.net/)사용하여 처리된 샘플 사용자 지정 양식:***

:::image type="content" source="media/analyze.png" alt-text="스크린샷: Form Recognizer 도구 사용자 지정 양식 분석 창":::

> [!div class="nextstepaction"]
> [자세한 정보: 사용자 지정 모델](concept-custom.md)

## <a name="data-extraction"></a>데이터 추출

 | **모델**   | **텍스트 추출** |**키-값 쌍** |**필드**|**선택 표시**   | **테이블**   |**엔터티** |
  | --- | :---: |:---:| :---: | :---: |:---: |:---: |
  |🆕일원 문서  | ✓  |  ✓ || ✓  | ✓  | ✓  |
  | Layout  | ✓  |   || ✓  | ✓  |   |
  | 청구서  | ✓ | ✓  |✓| ✓  | ✓ ||
  |Receipt  | ✓  |   ✓ |✓|   |  ||
  | ID 문서 | ✓  |   ✓  |✓|   |   ||
  | 명함    | ✓  |   ✓ | ✓|  |   ||
  | 사용자 지정             |✓  |  ✓ || ✓  | ✓  | ✓  |

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
