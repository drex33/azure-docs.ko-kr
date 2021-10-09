---
title: Form Recognizer 청구서 모델
titleSuffix: Azure Applied AI Services
description: 미리빌드 청구서 모델을 사용한 데이터 추출 및 분석을 포함하는 개념
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 4b660d464a8615886be9b466fd2e9de808ef3bd9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129717734"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-invoice-model"></a>Form Recognizer 청구서 모델

 청구서 모델은 강력한 OCR(광학 문자 인식) 기능을 딥 러닝 모델과 결합하여 판매 청구서에서 주요 필드와 품목을 분석하고 추출합니다.  청구서는 휴대폰 캡처 이미지, 스캔한 문서 및 디지털 PDF를 비롯한 다양한 형식과 품질일 수 있습니다. API는 청구서 텍스트를 분석합니다. 는 고객 이름, 청구 주소, 기한 및 기한과 같은 주요 정보를 추출합니다. 및 는 구조적 JSON 데이터 표현을 반환합니다.

##### <a name="sample-invoice-processed-with-form-recognizer-sample-labeling-tool"></a>[Form Recognizer 샘플 레이블 지정 도구를 사용하여](https://fott-2-1.azurewebsites.net/)처리된 샘플 청구서:

:::image type="content" source="media/overview-invoices.jpg" alt-text="샘플 송장" lightbox="media/overview-invoices-big.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Form Recognizer Studio(미리 보기) 사용해 보기

* Form Recognizer Studio는 미리 보기(v3.0) API와 함께 사용할 수 있습니다.

* Form Recognizer Studio 청구서 기능을 통해 고객 및 공급업체 세부 정보, 품목 등을 추출합니다.

> [!div class="nextstepaction"]
> [Form Recognizer Studio 사용해 보세요.](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)

## <a name="try-it-sample-labeling-tool"></a>사용해 보세요. 샘플 레이블 지정 도구

샘플 레이블 지정 도구를 사용하여 청구서 데이터를 추출하는 방법을 확인할 수 있습니다. 다음이 필요합니다.

* Azure 구독 - [체험 구독을 만들](https://azure.microsoft.com/free/cognitive-services/) 수 있습니다.

* [Azure Portal Form Recognizer 인스턴스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) )입니다. 무료 가격 책정 계층(`F0`)을 사용하여 서비스를 시도할 수 있습니다. 리소스가 배포된 후 **리소스로 이동을** 클릭하여 API 키와 엔드포인트를 얻습니다.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal 키 및 엔드포인트 위치.":::

* 청구서 문서입니다. 샘플 청구서 [문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)를 사용할 수 있습니다.

> [!div class="nextstepaction"]
  > [사용해 보세요.](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  Form Recognizer UI에서 다음을 수행합니다.

  1. **미리 디버전된 모델을 사용하여 데이터 받기를** 선택합니다.
  1. **양식 유형** 드롭다운 메뉴에서 **청구서를** 선택합니다.

  :::image type="content" source="media/try-invoice.png" alt-text="스크린샷: 샘플 레이블 지정 도구 드롭다운 미리 구성된 모델 선택 메뉴.":::

## <a name="input-requirements"></a>입력 요구 사항

* 최상의 결과를 위해 문서당 하나의 명확한 사진 또는 고품질 스캔을 제공합니다.
* 지원되는 파일 형식: JPEG, PNG, BMP, TIFF 및 PDF(텍스트 포함 또는 검사). 텍스트 포함 PDF는 문자 추출 및 위치에 오류가 발생할 가능성을 제거하는 데 가장 좋습니다.
* PDF 및 TIFF의 경우 최대 2000페이지를 처리할 수 있습니다(무료 계층 구독을 사용하면 처음 두 페이지만 처리).
* 파일 크기는 50MB 미만이어야 합니다.
* 이미지 크기는 50x50 픽셀에서 10,000x10,000 픽셀 사이여야 합니다.
* PDF 크기는 Legal 또는 A3 용지 크기에 해당하는 최대 17 x 17인치 또는 더 작은 크기입니다.
* 학습 데이터의 총 크기는 500페이지 이하입니다.
* PDF가 암호로 잠겨 있는 경우 제출하기 전에 잠금을 제거해야 합니다.
* 감독되지 않은 학습의 경우(레이블이 지정되지 않은 데이터 제외):
  * 데이터에는 키와 값이 포함되어야 합니다.
  * 키는 값의 위 또는 왼쪽에 나타나야 합니다. 아래 또는 오른쪽에 표시할 수 없습니다.

> [!NOTE]
> [샘플 레이블 지정 도구는](https://fott-2-1.azurewebsites.net/) BMP 파일 형식을 지원하지 않습니다. 이는 Form Recognizer 서비스가 아닌 도구의 제한 사항입니다.

## <a name="supported-languages-and-locales"></a>지원되는 언어 및 로케일

| 모델 | 언어 - 로케일 코드 | 기본값 |
|--------|:----------------------|:---------|
|청구서| <ul><li>영어(미국)-en-US</li></ul>| 영어(미국)-en-US|

## <a name="key-value-pair-extraction"></a>키-값 쌍 추출

|Name| Type | 설명 | 표준화된 출력 |
|:-----|:----|:----|:---:|
| CustomerName | 문자열 | 송장 발부된 고객| |
| CustomerId | 문자열 | 고객 참조 ID | |
| PurchaseOrder | 문자열 | 구매 주문 참조 번호 | |
| InvoiceId | 문자열 | 이 특정 청구서의 ID(많은 경우 ‘청구서 번호’) | |
| InvoiceDate | date | 청구서가 발행된 날짜 | yyyy-mm-dd|
| DueDate | date | 이 청구서의 지불 기일 | yyyy-mm-dd|
| VendorName | 문자열 | 공급업체 이름 |  |
| VendorAddress | 문자열 |  공급업체 메일 주소|  |
| VendorAddressRecipient | 문자열 | VendorAddress와 관련된 이름 |  |
| CustomerAddress | 문자열 | 고객의 우편 주소 | |
| CustomerAddressRecipient | 문자열 | CustomerAddress와 관련된 이름 | |
| BillingAddress | 문자열 | 고객의 명시적 청구 주소 |  |
| BillingAddressRecipient | 문자열 | BillingAddress와 관련된 이름 | |
| ShippingAddress | 문자열 | 고객의 명시적 배송 주소 | |
| ShippingAddressRecipient | 문자열 | ShippingAddress와 관련된 이름 |  |
| SubTotal | number | 이 청구서에서 식별된 소계 필드 | integer |
| TotalTax | number | 이 청구서에서 식별된 세금 총액 필드 | integer |
| InvoiceTotal | 숫자 (USD) | 이 청구서와 관련된 새 요금 총액 | integer |
| AmountDue |  숫자 (USD) | 공급업체에 지불할 총액 | integer |
| ServiceAddress | 문자열 | 고객의 명시적 서비스 주소 또는 건물 주소 | |
| ServiceAddressRecipient | 문자열 | ServiceAddress와 관련된 이름 |  |
| RemittanceAddress | 문자열 | 고객의 명시적 송금 주소 또는 지불 주소 |   |
| RemittanceAddressRecipient | 문자열 | RemittanceAddress와 관련된 이름 |  |
| ServiceStartDate | date | 서비스 기간의 시작 날짜(예: 유틸리티 청구 서비스 기간) | yyyy-mm-dd |
| ServiceEndDate | date | 서비스 기간의 종료 날짜(예: 유틸리티 청구 서비스 기간) | yyyy-mm-dd|
| PreviousUnpaidBalance | number | 이전에 미지불된 명시적 잔액 | integer |

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
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
