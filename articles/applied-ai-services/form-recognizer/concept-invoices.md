---
title: 청구서 - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Form Recognizer API 사용량 및 제한으로 청구서 분석과 관련된 개념을 알아봅니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: 92f20d9275aad08a47e20202e1daab621d1a4578
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646990"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Form Recognizer의 미리 빌드된 청구서 모델

Azure Form Recognizer는 미리 빌드된 청구서 모델을 사용하여 판매 청구서에서 정보를 분석하고 추출할 수 있습니다. 청구서 API를 사용하면 고객이 다양한 형식으로 청구서를 받고 구조화된 데이터를 반환하여 청구서 처리를 자동화할 수 있습니다. 강력한 [OCR(광학 인식)](../../cognitive-services/computer-vision/overview-ocr.md) 기능을 청구서 해석 딥 러닝 모델과 결합하여 영어로 작성된 청구서에서 주요 정보를 추출합니다. 텍스트, 표 및 고객, 공급업체, 청구서 ID, 청구서 기한, 총액, 청구서 지불액, 세액, 배송지, 청구지, 개별 항목 등의 정보를 추출합니다. 미리 빌드된 청구서 API는 Form Recognizer v2.1에서 공개적으로 사용할 수 있습니다.

## <a name="what-does-the-invoice-service-do"></a>청구서 서비스는 어떤 작업을 수행하나요?

청구서 API는 청구서에서 키 필드와 개별 항목을 추출하여 구성된 형태의 구조화된 JSON 응답으로 반환합니다. 청구서는 휴대폰으로 캡처한 이미지, 스캔한 문서, 디지털 PDF 등 형식과 품질이 다양할 수 있습니다. 청구서 API는 이러한 모든 청구서에서 구조화된 출력을 추출합니다.

![Contoso 청구서 예제](./media/invoice-example-new.jpg)

## <a name="try-it&quot;></a>시도

Form Recognizer 청구서 서비스를 사용해보려면 다음과 같은 온라인 샘플 UI 도구로 이동합니다.

> [!div class=&quot;nextstepaction&quot;]
> [청구서 모델 사용해보기](https://aka.ms/fott-2.1-ga &quot;미리 빌드된 모델로 시작하여 비즈니스 카드에서 데이터를 추출합니다.")

Form Recognizer 청구서 서비스를 사용해보려면 Azure 구독([무료로 하나 생성](https://azure.microsoft.com/free/cognitive-services))과 [Form Recognizer 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 엔드포인트 및 키가 필요합니다.

:::image type="content" source="media/analyze-invoice-new.png" alt-text="분석된 청구서 예제" lightbox="media/analyze-invoice-new.png":::

### <a name="input-requirements"></a>입력 요구 사항

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>지원되는 로캘

**미리 빌드된 청구서 v2.1** 은 **en-us** 로캘의 청구서를 지원합니다.

## <a name="analyze-invoice"></a>송장 분석

[청구서 분석](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291) 작업은 청구서의 이미지 또는 PDF를 입력으로 사용하여 관심 있는 값을 추출합니다. 호출은 `Operation-Location`이라는 응답 헤더 필드를 반환합니다. `Operation-Location` 값은 다음 단계에서 사용할 결과 ID를 포함하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-invoice-result"></a>Get Analyze Invoice Result

두 번째 단계는 [청구서 분석 결과 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9acb78c40a2533aee83) 작업을 호출하는 것입니다. 이 작업은 청구서 분석 작업으로 만들어진 결과 ID를 입력으로 사용합니다. 다음과 같은 가능한 값을 가진 **상태** 필드가 포함된 JSON 응답을 반환합니다. **succeeded** 값이 반환될 때까지 이 작업을 반복적으로 호출합니다. 3~5초의 간격을 사용하여 RPS(초당 요청 수) 속도를 초과하지 않도록 합니다.

|필드| 형식 | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | notStarted: 분석 작업이 시작되지 않았습니다.<br /><br />running: 분석 작업이 진행 중입니다.<br /><br />failed: 분석 작업이 실패했습니다.<br /><br />succeeded: 분석 작업이 성공했습니다.|

**상태** 필드의 값이 **성공** 이면 JSON 응답에는 요청된 경우 청구서 해석 결과, 추출된 테이블 및 선택적 텍스트 인식 결과가 포함됩니다. 청구서 해석 결과는 명명된 필드 값의 사전으로 구성됩니다. 여기서 각 값에는 추출된 텍스트, 정규화된 값, 경계 상자, 신뢰도 및 해당 단어 요소가 포함됩니다. 또한 추출된 개별 항목도 포함되는데, 여기에서 각 개별 항목에는 금액, 설명, 단가, 수량 등이 포함되어 있습니다. 텍스트 인식 결과는 텍스트, 경계 상자 및 신뢰도 정보가 포함된 줄과 단어의 계층 구조로 구성됩니다.

### <a name="sample-json-output"></a>JSON 출력 샘플

청구서 분석 결과 가져오기 작업에 대한 응답은 모든 정보가 추출된 청구서를 구조화된 방식으로 표현한 것입니다.
[샘플 청구서 파일](media/sample-invoice.jpg) 및 구조화된 출력 [샘플 청구서 출력](media/invoice-example-new.jpg)은 여기를 참조하세요.

JSON 출력은 세 부분으로 나뉩니다.
* `"readResults"` 노드에는 인식된 모든 텍스트와 선택 표시가 포함됩니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다.
* `"pageResults"` 노드에는 ‘readResults’의 줄과 단어에 대한 참조, 경계 상자, 신뢰도와 함께 추출된 테이블과 셀이 포함됩니다.
* `"documentResults"` 노드에는 모델이 검색한 청구서 특정 값과 개별 항목이 포함됩니다. 청구서 ID, 배송 대상, 청구서 받는 사람, 고객, 합계, 개별 항목 등 청구서에서 모든 필드를 찾을 수 있습니다.

## <a name="example-output"></a>예제 출력

청구서 서비스는 텍스트, 테이블, 그리고 26개의 청구서 필드를 추출합니다. 다음은 JSON 출력 응답의 청구서에서 추출되는 필드입니다(아래 출력은 이 [샘플 청구서](media/sample-invoice.jpg)를 사용함).

### <a name="key-value-pairs"></a>키 값 쌍 

|속성| 유형 | Description | 텍스트 | 값(표준화된 출력) |
|:-----|:----|:----|:----| :----|
| CustomerName | 문자열 | 청구서를 받는 고객 | Microsoft Corp |  |
| CustomerId | 문자열 | 고객에 대한 참조 ID | CID-12345 |  |
| PurchaseOrder | 문자열 | 구매 주문 참조 번호 | PO-3333 | |
| InvoiceId | 문자열 | 이 특정 청구서의 ID(많은 경우 ‘청구서 번호’) | INV-100 | |
| InvoiceDate | date | 청구서가 발행된 날짜 | 11/15/2019 | 2019-11-15 |
| DueDate | date | 이 청구서의 지불 기일 | 2019/12/15 | 2019-12-15 |
| VendorName | 문자열 | 이 청구서를 만든 공급업체 | CONTOSO LTD. | |
| VendorAddress | 문자열 | 공급업체의 우편 주소 | 123 456th St New York, NY, 10001 | |
| VendorAddressRecipient | 문자열 | VendorAddress와 관련된 이름 | Contoso 본사 | |
| CustomerAddress | 문자열 | 고객의 우편 주소 | 123 Other St, Redmond WA, 98052 | |
| CustomerAddressRecipient | 문자열 | CustomerAddress와 관련된 이름 | Microsoft Corp | |
| BillingAddress | 문자열 | 고객의 명시적 청구 주소 | 123 Bill St, Redmond WA, 98052 | |
| BillingAddressRecipient | 문자열 | BillingAddress와 관련된 이름 | Microsoft Services | |
| ShippingAddress | 문자열 | 고객의 명시적 배송 주소 | 123 Ship St, Redmond WA, 98052 | |
| ShippingAddressRecipient | 문자열 | ShippingAddress와 관련된 이름 | Microsoft Delivery | |
| SubTotal | number | 이 청구서에서 식별된 소계 필드 | $100.00 | 100 |
| TotalTax | number | 이 청구서에서 식별된 세금 총액 필드 | $10.00 | 10 |
| InvoiceTotal | number | 이 청구서와 관련된 새 요금 총액 | $110.00 | 110 |
| AmountDue |  number | 공급업체에 지불할 총액 | $610.00 | 610 |
| ServiceAddress | 문자열 | 고객의 명시적 서비스 주소 또는 건물 주소 | 123 Service St, Redmond WA, 98052 | |
| ServiceAddressRecipient | 문자열 | ServiceAddress와 관련된 이름 | Microsoft Services | |
| RemittanceAddress | 문자열 | 고객의 명시적 송금 주소 또는 지불 주소 | 123 Remit St New York, NY, 10001 |  |
| RemittanceAddressRecipient | 문자열 | RemittanceAddress와 관련된 이름 | Contoso 청구 |  |
| ServiceStartDate | date | 서비스 기간의 시작 날짜(예: 유틸리티 청구 서비스 기간) | 2019/10/14 | 2019-10-14 |
| ServiceEndDate | date | 서비스 기간의 종료 날짜(예: 유틸리티 청구 서비스 기간) | 2019/11/14 | 2019-11-14 |
| PreviousUnpaidBalance | number | 이전에 미지불된 명시적 잔액 | $500.00 | 500 |

### <a name="line-items"></a>품목

다음은 JSON 출력 응답의 청구서에서 추출되는 개별 항목입니다(아래 출력은 이 [샘플 청구서](./media/sample-invoice.jpg)를 사용함).

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

추출된 청구서 키 값 쌍 및 품목은 JSON 출력의 documentResults 섹션에 있습니다. 

## <a name="next-steps"></a>다음 단계

- 사용자 고유의 청구서와 샘플을 [Form Recognizer 샘플 UI](https://aka.ms/fott-2.1-ga)에서 사용해 보세요.
- 사용자가 선택한 개발 언어로 Form Recognizer를 사용하여 청구서 처리 앱 작성을 시작하려면 [Form Recognizer 빠른 시작](quickstarts/client-library.md)을 완료하세요.

## <a name="see-also"></a>참조

* [Form Recognizer란?](./overview.md)
* [REST API 참조 문서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
