---
title: 폼 인식기 송장 모델
titleSuffix: Azure Applied AI Services
description: 미리 빌드된 송장 모델을 사용 하 여 데이터 추출 및 분석을 포괄 하는 개념
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0f9c2f1603a87e30b0db32041f7d7aeff259600e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027364"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-invoice-model"></a>폼 인식기 송장 모델

 송장 모델은 강력한 OCR (광학 인식) 기능을 심층 학습 모델과 결합 하 여 판매 청구서에서 핵심 필드와 품목을 분석 하 고 추출 합니다.  청구서는 전화를 캡처한 이미지, 스캔 한 문서 및 디지털 Pdf를 비롯 한 다양 한 형식 및 품질 일 수 있습니다. API는 송장 텍스트를 분석 합니다. 고객 이름, 청구 주소, 기한 및 금액 만료와 같은 주요 정보를 추출 합니다. 및는 구조화 된 JSON 데이터 표현을 반환 합니다.

**[양식 인식기 스튜디오](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)를 사용 하 여 처리 되는 샘플 송장**:

:::image type="content" source="media/studio/overview-invoices.png" alt-text="샘플 송장" lightbox="media/overview-invoices-big.jpg":::

## <a name="development-options"></a>개발 옵션

다음 리소스는 폼 인식기 v 2.1에서 지원 됩니다.

| 기능 | 리소스 |
|----------|-------------------------|
|**청구서 모델**| <ul><li>[**Form Recognizer 레이블 지정 도구**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-invoices)</li><li>[**클라이언트 라이브러리 SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker 컨테이너**](containers/form-recognizer-container-install-run.md?tabs=invoice#run-the-container-with-the-docker-compose-up-command)</li></ul>|

다음 리소스는 폼 인식기 v 3.0에서 지원 됩니다.

| 기능 | 리소스 | 모델 ID |
|----------|-------------|-----------|
|**청구서 모델** | <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript SDK**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**미리 작성-송장**|

### <a name="try-form-recognizer"></a>Form Recognizer 사용해보기

양식 인식기 스튜디오 또는 샘플 레이블 도구를 사용 하 여 송장에서 고객 정보, 공급 업체 세부 정보 및 품목을 포함 한 데이터를 추출 하는 방법을 참조 하세요. 다음이 필요 합니다.

* Azure 구독 – [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)

* Azure Portal의 [폼 인식기 인스턴스입니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) . 무료 가격 책정 계층(`F0`)을 사용하여 서비스를 시도할 수 있습니다. 리소스를 배포한 후 **리소스로 이동** 을 선택 하 여 API 키 및 끝점을 가져옵니다.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio(미리 보기)

1. 폼 인식기 스튜디오 홈 페이지에서 **송장** 을 선택 합니다.

1. 샘플 송장을 분석 하거나 **+ 추가** 단추를 선택 하 여 사용자 고유의 샘플을 업로드할 수 있습니다.

1. **분석** 단추를 선택 합니다.

    :::image type="content" source="media/studio/invoice-analyze.png" alt-text="스크린샷: 송장 분석 메뉴.":::

> [!div class="nextstepaction"]
> [폼 인식기 스튜디오 체험](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)

#### <a name="sample-labeling-tool"></a>샘플 레이블 지정 도구

송장 문서가 필요 합니다. [샘플 청구서 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)를 사용할 수 있습니다.

1. 샘플 레이블 지정 도구 홈 페이지에서 미리 작성 된 **모델을 사용 하 여 데이터 가져오기를** 선택 합니다.

1. **양식 유형** 드롭다운 메뉴에서 **송장** 을 선택 합니다.

    :::image type="content" source="media/try-invoice.png" alt-text="스크린샷: 샘플 레이블 지정 도구 드롭다운 미리 작성 한 모델 선택 메뉴입니다.":::

    > [!div class="nextstepaction"]
    > [샘플 레이블 지정 도구 시도](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

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

## <a name="supported-languages-and-locales"></a>지원 되는 언어 및 로캘

| 모델 | 언어-로캘 코드 | 기본값 |
|--------|:----------------------|:---------|
|청구서| <ul><li>영어 (미국) — en-us</li></ul>| 영어 (미국) — en-us|

## <a name="field-extraction"></a>필드 추출

|이름| Type | 설명 | 표준화 된 출력 |
|:-----|:----|:----|:---:|
| CustomerName | String | 송장이 발부 한 고객| |
| CustomerId | String | 고객 참조 ID | |
| PurchaseOrder | String | 구매 주문 참조 번호 | |
| InvoiceId | String | 이 특정 청구서의 ID(많은 경우 ‘청구서 번호’) | |
| InvoiceDate | Date | 청구서가 발행된 날짜 | yyyy-mm-dd|
| DueDate | Date | 이 청구서의 지불 기일 | yyyy-mm-dd|
| VendorName | String | 공급업체 이름 |  |
| VendorAddress | String |  공급업체 메일 주소|  |
| VendorAddressRecipient | String | VendorAddress와 관련된 이름 |  |
| CustomerAddress | String | 고객의 우편 주소 | |
| CustomerAddressRecipient | String | CustomerAddress와 관련된 이름 | |
| BillingAddress | String | 고객의 명시적 청구 주소 |  |
| BillingAddressRecipient | String | BillingAddress와 관련된 이름 | |
| ShippingAddress | String | 고객의 명시적 배송 주소 | |
| ShippingAddressRecipient | String | ShippingAddress와 관련된 이름 |  |
| SubTotal | 숫자 | 이 청구서에서 식별된 소계 필드 | 정수 |
| TotalTax | 숫자 | 이 청구서에서 식별된 세금 총액 필드 | 정수 |
| InvoiceTotal | Number(USD) | 이 청구서와 관련된 새 요금 총액 | 정수 |
| AmountDue |  Number(USD) | 공급업체에 지불할 총액 | 정수 |
| ServiceAddress | String | 고객의 명시적 서비스 주소 또는 건물 주소 | |
| ServiceAddressRecipient | String | ServiceAddress와 관련된 이름 |  |
| RemittanceAddress | String | 고객의 명시적 송금 주소 또는 지불 주소 |   |
| RemittanceAddressRecipient | String | RemittanceAddress와 관련된 이름 |  |
| ServiceStartDate | Date | 서비스 기간의 시작 날짜(예: 유틸리티 청구 서비스 기간) | yyyy-mm-dd |
| ServiceEndDate | Date | 서비스 기간의 종료 날짜(예: 유틸리티 청구 서비스 기간) | yyyy-mm-dd|
| PreviousUnpaidBalance | 숫자 | 이전에 미지불된 명시적 잔액 | 정수 |

### <a name="line-items"></a>품목

다음은 JSON 출력 응답의 청구서에서 추출되는 개별 항목입니다(아래 출력은 이 [샘플 청구서](media/sample-invoice.jpg)를 사용함).

|이름| Type | 설명 | 텍스트(개별 항목 1) | 값(표준화된 출력) |
|:-----|:----|:----|:----| :----|
| 항목 | String | 개별 항목의 전체 문자열 텍스트 줄 | 2021/3/4 A123 컨설팅 서비스 2시간 $30.00 10% $60.00 | |
| Amount | 숫자 | 개별 항목의 금액 | $60.00 | 100 |
| Description | String | 청구서 개별 항목의 텍스트 설명 | 컨설팅 서비스 | 컨설팅 서비스 |
| 수량 | 숫자 | 이 청구서 개별 항목의 수량 | 2 | 2 |
| 단가 | 숫자 | 이 항목의 1개 단위의 정가 및 총가(청구서의 청구서 총액 설정에 따라 다름) | $30.00 | 30 |
| ProductCode | String| 특정 개별 항목과 관련된 제품 코드, 제품 번호 또는 SKU | A123 | |
| 단위 | String| 개별 항목의 단위(예: kg, lb 등) | 시간 | |
| Date | Date| 각 개별 항목에 해당하는 날짜이며, 개별 항목이 배송된 날짜인 경우가 많음 | 2021/3/4| 2021-03-04 |
| 세금 | 숫자 | 각 개별 항목과 관련된 세금이며, 가능한 값으로는 세액, 세율, 세금 Y/N이 있음 | 10% | |

청구서 키-값 쌍 및 추출 된 품목은 `documentResults` JSON 출력의 섹션에 있습니다. 

## <a name="form-recognizer-preview-v30"></a>양식 인식기 미리 보기 v 3.0

 양식 인식기 미리 보기에는 몇 가지 새로운 기능 및 기능이 도입 되었습니다.

* 응용 프로그램 및 워크플로에서 미리 보기 버전을 사용 하는 방법에 대 한 자세한 내용은 [**인식기 v 3.0 마이그레이션 가이드**](v3-migration-guide.md) 를 참조 하세요.

* 미리 보기 버전 및 새로운 기능에 대 한 자세한 내용은 [**REST API (미리 보기)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) 를 살펴보세요.

## <a name="next-steps"></a>다음 단계

* 양식 인식기 빠른 시작을 완료 합니다.

  > [!div class="nextstepaction"]
  > [Form Recognizer 빠른 시작](quickstarts/try-sdk-rest-api.md)

* REST API를 살펴보세요.

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
