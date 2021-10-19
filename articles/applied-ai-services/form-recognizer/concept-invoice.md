---
title: 폼 인식기 송장 모델
titleSuffix: Azure Applied AI Services
description: 미리 빌드된 송장 모델을 사용 하 여 데이터 추출 및 분석을 포괄 하는 개념
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 458e6d9b9de91dcf9f9214f2021fb780841ff7b4
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164349"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-invoice-model"></a>폼 인식기 송장 모델

 송장 모델은 강력한 OCR (광학 인식) 기능을 심층 학습 모델과 결합 하 여 판매 청구서에서 핵심 필드와 품목을 분석 하 고 추출 합니다.  청구서는 전화를 캡처한 이미지, 스캔 한 문서 및 디지털 Pdf를 비롯 한 다양 한 형식 및 품질 일 수 있습니다. API는 송장 텍스트를 분석 합니다. 고객 이름, 청구 주소, 기한 및 금액 만료와 같은 주요 정보를 추출 합니다. 및는 구조화 된 JSON 데이터 표현을 반환 합니다.

##### <a name="sample-invoice-processed-with-form-recognizer-sample-labeling-tool"></a>[양식 인식기 샘플 레이블 도구](https://fott-2-1.azurewebsites.net/)를 사용 하 여 처리 된 샘플 송장:

:::image type="content" source="media/overview-invoices.jpg" alt-text="샘플 송장" lightbox="media/overview-invoices-big.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>양식 인식기 스튜디오 체험 (미리 보기)

* 양식 인식기 스튜디오는 preview (v 3.0) API에서 사용할 수 있습니다.

* 양식 인식기 스튜디오 청구서 기능을 사용 하 여 고객 및 공급 업체 세부 정보, 품목 등을 추출 합니다.

> [!div class="nextstepaction"]
> [폼 인식기 스튜디오 체험](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)

## <a name="try-it-sample-labeling-tool"></a>사용해 보기: 샘플 레이블 지정 도구

샘플 레이블 지정 도구를 사용해 서 청구서 데이터를 추출 하는 방법을 확인할 수 있습니다. 다음이 필요 합니다.

* Azure 구독- [무료로 하나를 만들](https://azure.microsoft.com/free/cognitive-services/) 수 있습니다.

* Azure Portal의 [폼 인식기 인스턴스입니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) . 무료 가격 책정 계층(`F0`)을 사용하여 서비스를 시도할 수 있습니다. 리소스를 배포한 후 **리소스로 이동** 을 클릭 하 여 API 키 및 끝점을 가져옵니다.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

* 청구서 문서입니다. [샘플 청구서 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)를 사용할 수 있습니다.

> [!div class="nextstepaction"]
  > [사용해 보기](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  양식 인식기 UI에서:

  1. **미리 작성 한 모델을 사용 하 여 데이터 가져오기를** 선택 합니다.
  1. **양식 유형** 드롭다운 메뉴에서 **송장** 을 선택 합니다.

  :::image type="content" source="media/try-invoice.png" alt-text="스크린샷: 샘플 레이블 지정 도구 드롭다운 미리 작성 한 모델 선택 메뉴입니다.":::

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

| 모델 | 언어-로캘 코드 | 기본값 |
|--------|:----------------------|:---------|
|청구서| <ul><li>영어 (미국) — en-us</li></ul>| 영어 (미국) — en-us|

## <a name="key-value-pair-extraction"></a>키-값 쌍 추출

|Name| 유형 | Description | 표준화 된 출력 |
|:-----|:----|:----|:---:|
| CustomerName | 문자열 | 송장이 발부 한 고객| |
| CustomerId | 문자열 | 고객 참조 ID | |
| PurchaseOrder | 문자열 | 구매 주문 참조 번호 | |
| InvoiceId | 문자열 | 이 특정 청구서의 ID(많은 경우 ‘청구서 번호’) | |
| InvoiceDate | date | 청구서가 발행된 날짜 | yyyy-mm-dd|
| DueDate | date | 이 청구서의 지불 기일 | yyyy-mm-dd|
| VendorName | 문자열 | 공급업체 이름 |  |
| VendorAddress | 문자열 |  공급 업체 우편 주소|  |
| VendorAddressRecipient | 문자열 | VendorAddress와 관련된 이름 |  |
| CustomerAddress | 문자열 | 고객의 우편 주소 | |
| CustomerAddressRecipient | 문자열 | CustomerAddress와 관련된 이름 | |
| BillingAddress | 문자열 | 고객의 명시적 청구 주소 |  |
| BillingAddressRecipient | 문자열 | BillingAddress와 관련된 이름 | |
| ShippingAddress | 문자열 | 고객의 명시적 배송 주소 | |
| ShippingAddressRecipient | 문자열 | ShippingAddress와 관련된 이름 |  |
| SubTotal | number | 이 청구서에서 식별된 소계 필드 | integer |
| TotalTax | number | 이 청구서에서 식별된 세금 총액 필드 | integer |
| InvoiceTotal | number(USD) | 이 청구서와 관련된 새 요금 총액 | integer |
| AmountDue |  number(USD) | 공급업체에 지불할 총액 | integer |
| ServiceAddress | 문자열 | 고객의 명시적 서비스 주소 또는 건물 주소 | |
| ServiceAddressRecipient | 문자열 | ServiceAddress와 관련된 이름 |  |
| RemittanceAddress | 문자열 | 고객의 명시적 송금 주소 또는 지불 주소 |   |
| RemittanceAddressRecipient | 문자열 | RemittanceAddress와 관련된 이름 |  |
| ServiceStartDate | date | 서비스 기간의 시작 날짜(예: 유틸리티 청구 서비스 기간) | yyyy-mm-dd |
| ServiceEndDate | date | 서비스 기간의 종료 날짜(예: 유틸리티 청구 서비스 기간) | yyyy-mm-dd|
| PreviousUnpaidBalance | number | 이전에 미지불된 명시적 잔액 | integer |

### <a name="line-items"></a>품목

다음은 JSON 출력 응답의 청구서에서 추출되는 개별 항목입니다(아래 출력은 이 [샘플 청구서](media/sample-invoice.jpg)를 사용함).

|이름| 유형 | 설명 | 텍스트(개별 항목 1) | 값(표준화된 출력) |
|:-----|:----|:----|:----| :----|
| 항목 | 문자열 | 개별 항목의 전체 문자열 텍스트 줄 | 2021/3/4 A123 컨설팅 서비스 2시간 $30.00 10% $60.00 | |
| Amount | number | 개별 항목의 금액 | $60.00 | 100 |
| Description | 문자열 | 청구서 개별 항목의 텍스트 설명 | 컨설팅 서비스 | 컨설팅 서비스 |
| 수량 | number | 이 청구서 개별 항목의 수량 | 2 | 2 |
| 단가 | number | 이 항목의 1개 단위의 정가 및 총가(청구서의 청구서 총액 설정에 따라 다름) | $30.00 | 30 |
| ProductCode | 문자열| 특정 개별 항목과 관련된 제품 코드, 제품 번호 또는 SKU | A123 | |
| 단위 | 문자열| 개별 항목의 단위(예: kg, lb 등) | 시간 | |
| 날짜 | 날짜| 각 개별 항목에 해당하는 날짜이며, 개별 항목이 배송된 날짜인 경우가 많음 | 2021/3/4| 2021-03-04 |
| 세금 | number | 각 개별 항목과 관련된 세금이며, 가능한 값으로는 세액, 세율, 세금 Y/N이 있음 | 10% | |

추출된 청구서 키-값 쌍 및 품목은 `documentResults` JSON 출력의 섹션에 있습니다. 

## <a name="form-recognizer-preview-v30"></a>Form Recognizer 미리 보기 v3.0

 Form Recognizer 미리 보기에는 몇 가지 새로운 기능과 기능이 도입되었습니다.

* Form Recognizer [**v3.0 마이그레이션 가이드에**](v3-migration-guide.md) 따라 애플리케이션 및 워크플로에서 미리 보기 버전을 사용하는 방법을 알아봅니다.

* 미리 보기 버전 및 새로운 기능에 대해 자세히 알아보려면 [**REST API(미리 보기)를**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) 살펴보세요.

## <a name="next-steps"></a>다음 단계

* Form Recognizer 빠른 시작 완료:

  > [!div class="nextstepaction"]
  > [Form Recognizer 빠른 시작](quickstarts/try-sdk-rest-api.md)

* REST API 살펴보기:

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
