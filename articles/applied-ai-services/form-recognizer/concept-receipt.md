---
title: Form Recognizer 영수증 모델
titleSuffix: Azure Applied AI Services
description: 미리 된 영수증 모델을 사용 하 여 데이터 추출 및 분석을 포함 하는 개념
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 111dcc2ab07c83e164e054395b0804f46c07d748
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027075"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-receipt-model"></a>Form Recognizer 영수증 모델

영수증 모델은 강력한 OCR(광학 인식) 기능을 딥 러닝 모델과 결합하여 판매 영수증에서 주요 정보를 분석하고 추출합니다. 영수증은 인쇄 및 필기 영수증을 포함하여 다양한 형식과 품질일 수 있습니다. API는 가맹점 이름, 가맹점 전화 번호, 거래 날짜, 세금 및 트랜잭션 합계와 같은 주요 정보를 추출하고 구조화 된 JSON 데이터 표현을 반환합니다.

***Form Recognizer 샘플 레이블 지정 도구를 사용하여 처리된 [샘플 영수증:](https://fott-2-1.azurewebsites.net/)***

:::image type="content" source="media/studio/overview-receipt.png" alt-text="샘플 영수증" lightbox="media/overview-receipt.jpg":::

## <a name="development-options"></a>개발 옵션

Form Recognizer v2.1에서 지원되는 리소스는 다음과 같습니다.

| 기능 | 리소스 |
|----------|-------------------------|
|**영수증 모델**| <ul><li>[**Form Recognizer 레이블 지정 도구**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-receipts)</li><li>[**클라이언트 라이브러리 SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker 컨테이너**](containers/form-recognizer-container-install-run.md?tabs=receipt#run-the-container-with-the-docker-compose-up-command)</li></ul>|

다음 리소스는 Form Recognizer v3.0에서 지원됩니다.

| 기능 | 리소스 | 모델 ID |
|----------|-------------|-----------|
|**영수증 모델**| <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|**prebuilt-receipt**|

### <a name="try-form-recognizer"></a>Form Recognizer 사용해보기

Form Recognizer Studio 또는 샘플 레이블 지정 도구를 사용하여 트랜잭션 시간 및 날짜, 가맹점 정보 및 총액을 포함한 데이터를 영수증에서 추출하는 방법을 확인합니다. 다음이 필요합니다.

* Azure 구독 – [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)

* [Azure Portal Form Recognizer 인스턴스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) )입니다. 무료 가격 책정 계층(`F0`)을 사용하여 서비스를 시도할 수 있습니다. 리소스가 배포된 후 **리소스로 이동을** 선택하여 API 키 및 엔드포인트를 얻습니다.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio(미리 보기)

> [!NOTE]
> Form Recognizer Studio는 미리 보기(v3.0) API와 함께 사용할 수 있습니다.

1. Form Recognizer Studio 홈페이지에서 **영수증을 선택합니다.**

1. 샘플 영수증을 분석하거나 **+ 추가** 단추를 선택하여 사용자 고유의 샘플을 업로드할 수 있습니다.

1. **분석** 단추를 선택합니다.

    :::image type="content" source="media/studio/receipt-analyze.png" alt-text="스크린샷: 영수증 분석 메뉴.":::

    > [!div class="nextstepaction"]
    > [Form Recognizer Studio 사용해 보세요.](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)

#### <a name="sample-labeling-tool"></a>샘플 레이블 지정 도구

영수증 문서가 필요합니다. 샘플 영수증 [문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-receipt.png)를 사용할 수 있습니다.

1. 샘플 레이블 지정 도구 홈페이지에서 **미리 디버깅된 모델을 사용하여 데이터 받기를** 선택합니다.

1. **양식 유형** 드롭다운 메뉴에서 **영수증을** 선택합니다.

      :::image type="content" source="media/try-receipt.png" alt-text="스크린샷: 샘플 레이블 지정 도구 드롭다운 미리 구성된 모델 선택 메뉴.":::

      > [!div class="nextstepaction"]
      > [샘플 레이블 지정 도구 사용해 보세요.](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

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

## <a name="supported-languages-and-locales-v21"></a>지원되는 언어 및 로케일 v2.1

>[!NOTE]
 > 로캘을 지정할 필요가 없습니다. 선택적 매개 변수입니다. Form Recognizer 딥 러닝 기술은 이미지의 텍스트에 사용된 언어를 자동으로 검색합니다.

| 모델 | 언어 - 로케일 코드 | 기본값 |
|--------|:----------------------|:---------|
|Receipt| <ul><li>영어(미국)-en-US</li><li> 영어(오스트레일리아)-en-AU</li><li>영어(캐나다)-en-CA</li><li>영어(영국)-en-GB</li><li>영어(인도)-en-IN</li></ul>  | 자동 검색 |

## <a name="field-extraction"></a>필드 추출

|이름| Type | 설명 | 표준화된 출력 |
|:-----|:----|:----|:----|
| ReceiptType | String | 판매 영수증의 유형 |  항목화 |
| MerchantName | String | 영수증을 발급한 판매자의 이름 |  |
| MerchantPhoneNumber | phoneNumber | 판매자 전화 번호 나열 | +1 xxx xxx xxxx |
| MerchantAddress | String | 판매자의 주소 나열 |   |
| TransactionDate | Date | 영수증이 발급된 날짜 | yyyy-mm-dd |
| TransactionTime | 시간 | 영수증이 발급된 시간 | hh-mm-ss (24 시간)  |
| 합계 | 숫자 (USD)| 영수증의 총 트랜잭션 합계 | 두 개의 10 진수 float|
| 소계 | 숫자 (USD) | 영수증의 소계(보통 세금이 적용되기 전) | 두 개의 10 진수 float|
| 세금 | 숫자 (USD) | 수령에 대 한 세금 (자주 판매 세금 또는 동급) | 두 개의 10 진수 float |
| 팁 | 숫자 (USD) | 구매자에 의해 포함된 팁 | 두 개의 10 진수 float|
| 항목 | 개체의 배열 | 추출된 품목(이름, 수량, 단가 및 총 가격) | |
| Name | String | 항목 이름 | |
| 수량 | 숫자 | 각 항목의 수량 | 정수 |
| 가격 | 숫자 | 각 항목 단위의 개별 가격| 두 개의 10 진수 float |
| 총 가격 | 숫자 | 품목에 대한 총 가격 | 두 개의 10 진수 float |

## <a name="form-recognizer-preview-v30"></a>양식 인식기 미리 보기 v 3.0

 양식 인식기 미리 보기에는 몇 가지 새로운 기능 및 기능이 도입 되었습니다. **수신** 모델은 단일 페이지 호텔 수령 처리를 지원 합니다.

### <a name="hotel-receipt-field-extraction"></a>호텔 수신 필드 추출

|Name| Type | 설명 | 표준화 된 출력 |
|:-----|:----|:----|:----|
| Arrivaldate.text | Date | 도착 날짜 | yyyy-mm-dd |
| 통화 | 통화 | 수령 금액의 통화 단위입니다. 예를 들어, 여러 값을 찾은 경우 USD, EUR 또는 MIXED입니다. ||
| DepartureDate | Date | 출발 날짜 | yyyy-mm-dd |
| 항목 | Array | | |
| 항목. *. 범주 | String | 항목 범주 (예: 방, 세금 등) |  |
| 항목. *. 날 | Date | 항목 날짜 | yyyy-mm-dd |
| 항목. *. 한 | String | 항목 설명 | |
| 항목. *. TotalPrice |  숫자 | 항목 총 가격 | 정수 |
| Locale | String | 확인 로캘의 로캘 (예: en-us) | ISO 언어-관할지 코드   |
| MerchantAddress | String | 판매자의 주소 나열 | |
| MerchantAliases | Array| | |
| MerchantAliases.* | String | 판매자의 대체 이름 |  |
| MerchantName | String | 영수증을 발급한 판매자의 이름 | |
| MerchantPhoneNumber | phoneNumber | 판매자 전화 번호 나열 | + 1 xxx xxx xxxx|
| ReceiptType | String | 수신 유형 (예: 호텔, 항목별) | |
| 합계 | 숫자 | 영수증의 총 트랜잭션 합계 | 두 개의 10 진수 float |

### <a name="hotel-receipt-supported-languages-and-locales"></a>호텔 수령 지원 언어 및 로캘

| 모델 | 언어 - 로케일 코드 | 기본값 |
|--------|:----------------------|:---------|
|영수증(호텔) | <ul><li>영어(미국)-en-US</li></ul>| 영어(미국)-en-US|

### <a name="migration-guide-and-rest-api-v30"></a>마이그레이션 가이드 및 REST API v3.0

* Form Recognizer [**v3.0 마이그레이션 가이드에**](v3-migration-guide.md) 따라 애플리케이션 및 워크플로에서 미리 보기 버전을 사용하는 방법을 알아봅니다.

* 미리 보기 버전 및 새로운 기능에 대해 자세히 알아보려면 [**REST API(미리 보기)를**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) 살펴보세요.

## <a name="next-steps"></a>다음 단계

* Form Recognizer 빠른 시작 완료:

  > [!div class="nextstepaction"]
  > [Form Recognizer 빠른 시작](quickstarts/try-sdk-rest-api.md)

* REST API 살펴보세요.

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)
