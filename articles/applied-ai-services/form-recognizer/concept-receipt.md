---
title: Form Recognizer 영수증 모델
titleSuffix: Azure Applied AI Services
description: 미리 된 영수증 모델을 사용 하 여 데이터 추출 및 분석을 포함 하는 개념
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 48ebbb4975e57db34fe080db2a61328cef65ca80
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129717703"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-receipt-model"></a>Form Recognizer 영수증 모델

영수증 모델은 강력한 OCR(광학 인식) 기능을 딥 러닝 모델과 결합하여 판매 영수증에서 주요 정보를 분석하고 추출합니다. 영수증은 인쇄 및 필기 영수증을 포함하여 다양한 형식과 품질일 수 있습니다. API는 가맹점 이름, 가맹점 전화 번호, 거래 날짜, 세금 및 트랜잭션 합계와 같은 주요 정보를 추출하고 구조화 된 JSON 데이터 표현을 반환합니다.

##### <a name="sample-receipt-processed-with-form-recognizer-sample-labeling-tool"></a>Form Recognizer 샘플 레이블 지정 도구를 사용하여 처리된 [샘플 영수증:](https://fott-2-1.azurewebsites.net/)

:::image type="content" source="./media/overview-receipt.jpg" alt-text="샘플 영수증" lightbox="./media/overview-receipt.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Form Recognizer Studio(미리 보기) 사용해 보기

* Form Recognizer Studio는 미리 보기(v3.0) API와 함께 사용할 수 있습니다.

* Form Recognizer Studio 영수증 기능을 통해 트랜잭션 시간 및 날짜, 가맹점 정보, 금액 합계 등을 추출합니다.

> [!div class="nextstepaction"]
> [Form Recognizer Studio 사용해 보세요.](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)

## <a name="try-it-sample-labeling-tool"></a>사용해 보세요. 샘플 레이블 지정 도구

샘플 레이블 지정 도구를 사용하여 영수증 데이터를 추출하는 방법을 확인할 수 있습니다. 다음이 필요합니다.

* Azure 구독 - [체험 구독을 만들](https://azure.microsoft.com/free/cognitive-services/) 수 있습니다.

* [Azure Portal Form Recognizer 인스턴스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) )입니다. 무료 가격 책정 계층(`F0`)을 사용하여 서비스를 시도할 수 있습니다. 리소스가 배포된 후 **리소스로 이동을** 클릭하여 API 키와 엔드포인트를 얻습니다.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal 키 및 엔드포인트 위치.":::

* 영수증 문서입니다. 샘플 영수증 [문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-receipt.png)를 사용할 수 있습니다.

> [!div class="nextstepaction"]
  > [사용해 보세요.](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

Form Recognizer UI에서 다음을 수행합니다.

  1. **미리 디버전된 모델을 사용하여 데이터 받기를** 선택합니다.
  1. **양식 유형** 드롭다운 메뉴에서 **영수증을** 선택합니다.

  :::image type="content" source="media/try-receipt.png" alt-text="스크린샷: 샘플 레이블 지정 도구 드롭다운 미리 구성된 모델 선택 메뉴.":::

## <a name="input-requirements"></a>입력 요구 사항

* 최상의 결과를 위해 문서당 하나의 명확한 사진 또는 고품질 스캔을 제공합니다.
* 지원되는 파일 형식: JPEG, PNG, BMP, TIFF 및 PDF(텍스트 포함 또는 검사). 텍스트 포함 PDF는 문자 추출 및 위치에 오류가 발생할 가능성을 제거하는 데 가장 좋습니다.
* PDF 및 TIFF의 경우 최대 2000페이지를 처리할 수 있습니다(무료 계층 구독을 사용하면 처음 두 페이지만 처리).
* 파일 크기는 50MB 미만이어야 합니다.
* 이미지 크기는 50x50 픽셀에서 10,000x10,000 픽셀 사이여야 합니다.
* PDF 차원은 최대 17 x 17인치이며 Legal 또는 A3 용지 크기에 해당하거나 더 작습니다.
* 학습 데이터의 총 크기는 500페이지 이하입니다.
* PDF가 암호로 잠겨 있는 경우 제출하기 전에 잠금을 제거해야 합니다.
* 감독되지 않은 학습의 경우(레이블이 지정되지 않은 데이터 제외):
  * 데이터에는 키와 값이 포함되어야 합니다.
  * 키는 값의 위 또는 왼쪽에 나타나야 합니다. 아래 또는 오른쪽에 표시할 수 없습니다.

## <a name="supported-languages-and-locales-v21"></a>지원되는 언어 및 로케일 v2.1

>[!NOTE]
 > 로캘을 지정할 필요가 없습니다. 선택적 매개 변수입니다. Form Recognizer 딥 러닝 기술은 이미지의 텍스트에 사용된 언어를 자동으로 검색합니다.

| 모델 | 언어 - 로케일 코드 | 기본값 |
|--------|:----------------------|:---------|
|Receipt| <ul><li>영어(미국)-en-US</li><li> 영어(오스트레일리아)-en-AU</li><li>영어(캐나다)-en-CA</li><li>영어(영국)-en-GB</li><li>영어(인도)-en-IN</li></ul>  | 자동 검색 |

## <a name="key-value-pair-extraction"></a>키-값 쌍 추출

|Name| Type | 설명 | 표준화된 출력 |
|:-----|:----|:----|:----|
| ReceiptType | 문자열 | 판매 영수증의 유형 |  항목화 |
| MerchantName | 문자열 | 영수증을 발급한 판매자의 이름 |  |
| MerchantPhoneNumber | phoneNumber | 판매자 전화 번호 나열 | +1 xxx xxx xxxx |
| MerchantAddress | 문자열 | 판매자의 주소 나열 |   |
| TransactionDate | date | 영수증이 발급된 날짜 | yyyy-mm-dd |
| TransactionTime | time | 영수증이 발급된 시간 | hh-mm-ss(24시간)  |
| 합계 | number(USD)| 영수증의 총 트랜잭션 합계 | 20진수 float|
| 소계 | number(USD) | 영수증의 소계(보통 세금이 적용되기 전) | 20진수 float|
| 세금 | number(USD) | 영수증에 대한 세금(판매세 또는 이와 동등한 경우가 많습니다.) | 20진수 float |
| 팁 | number(USD) | 구매자에 의해 포함된 팁 | 20진수 float|
| Items | 개체의 배열 | 추출된 품목(이름, 수량, 단가 및 총 가격) | |
| 이름 | string | 항목 이름 | |
| 수량 | number | 각 항목의 수량 | integer |
| 가격 | number | 각 항목 단위의 개별 가격| 2-10진수 float |
| 총 가격 | number | 품목에 대한 총 가격 | 2-10진수 float |

## <a name="form-recognizer-preview-v30"></a>Form Recognizer 미리 보기 v3.0

 Form Recognizer 미리 보기에는 다음과 같은 몇 가지 새로운 기능이 도입되었습니다.

* **영수증** 모델은 단일 페이지 호텔 영수증 처리를 지원합니다.

    ### <a name="hotel-receipt-key-value-pair-extraction"></a>호텔 영수증 키-값 쌍 추출

    |Name| Type | 설명 | 표준화된 출력 |
    |:-----|:----|:----|:----|
    | ArrivalDate | date | 도착 날짜 | yyyy-mm-dd |
    | 통화 | currency | 수령 금액의 통화 단위입니다. 예를 들어 여러 값이 있는 경우 USD, EUR 또는 MIXED ||
    | DepartureDate | date | 출발 날짜 | yyyy-mm-dd |
    | 항목 | array | | |
    | Items.*. 범주 | string | 항목 범주(예: 방, 세금 등) |  |
    | Items.*. 날짜 | date | 항목 날짜 | yyyy-mm-dd |
    | Items.*. 설명 | string | 항목 설명 | |
    | Items.*. TotalPrice |  number | 항목 총 가격 | integer |
    | Locale | 문자열 | 영수증의 로케일(예: en-US)입니다. | ISO 언어-군 코드   |
    | MerchantAddress | 문자열 | 판매자의 주소 나열 | |
    | MerchantAliases | array| | |
    | MerchantAliases.* | string | 가맹점의 대체 이름 |  |
    | MerchantName | 문자열 | 영수증을 발급한 판매자의 이름 | |
    | MerchantPhoneNumber | phoneNumber | 판매자 전화 번호 나열 | +1 xxx xxx xxxx|
    | ReceiptType | 문자열 | 영수증 유형(예: Hotel, Itemized) | |
    | 합계 | number | 영수증의 총 트랜잭션 합계 | 2-10진수 float |

    ### <a name="hotel-receipt-supported-languages-and-locales"></a>호텔 영수증 지원 언어 및 로케일

    | 모델 | 언어 - 로케일 코드 | 기본값 |
    |--------|:----------------------|:---------|
    |영수증(호텔) | <ul><li>영어(미국)-en-US</li></ul>| 영어(미국)-en-US|

* Form Recognizer [**v3.0 마이그레이션 가이드에**](v3-migration-guide.md) 따라 애플리케이션 및 워크플로에서 미리 보기 버전을 사용하는 방법을 알아봅니다.

* 미리 보기 버전 및 새로운 기능에 대해 자세히 알아보려면 [**REST API(미리 보기)를**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) 살펴보세요.

## <a name="next-steps"></a>다음 단계

* Form Recognizer 빠른 시작 완료:

  > [!div class="nextstepaction"]
  > [Form Recognizer 빠른 시작](quickstarts/try-sdk-rest-api.md)

* REST API 살펴보세요.

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)
