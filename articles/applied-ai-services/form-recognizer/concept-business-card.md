---
title: 양식 인식기 비즈니스 카드 모델
titleSuffix: Azure Applied AI Services
description: 미리 작성 한 비즈니스 카드 모델을 사용 하 여 데이터 추출 및 분석을 포괄 하는 개념
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 30b83d0edea0c3b54b0d15300e043c7f86fa6573
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027744"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-business-card-model"></a>양식 인식기 비즈니스 카드 모델

비즈니스 카드 모델은 강력한 OCR (광학 문자 인식) 기능을 심층 학습 모델과 결합 하 여 비즈니스 카드 이미지에서 핵심 정보를 분석 하 고 추출 합니다. API는 인쇄 된 명함을 분석 합니다. 이름, 성, 회사 이름, 전자 메일 주소 및 전화 번호와 같은 주요 정보를 추출 합니다.  및는 구조화 된 JSON 데이터 표현을 반환 합니다.

***양식 인식기 스튜디오를 사용 하 여 처리 되는 샘플 비즈니스 카드***

:::image type="content" source="./media/studio/overview-business-card-studio.png" alt-text="샘플 명함" lightbox="./media/overview-business-card.jpg":::

## <a name="development-options"></a>개발 옵션

다음 리소스는 폼 인식기 v 2.1에서 지원 됩니다.

| 기능 | 리소스 |
|----------|-------------------------|
|**비즈니스 카드 모델**|  <ul><li>[**Form Recognizer 레이블 지정 도구**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-business-cards)</li><li>[**클라이언트 라이브러리 SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker 컨테이너**](containers/form-recognizer-container-install-run.md?tabs=business-card#run-the-container-with-the-docker-compose-up-command)</li></ul>|

다음 리소스는 폼 인식기 v 3.0에서 지원 됩니다.

| 기능 | 리소스 | 모델 ID |
|----------|-------------|-----------|
|**비즈니스 카드 모델**| <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript SDK**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**미리 작성-businessCard**|

### <a name="try-form-recognizer"></a>Form Recognizer 사용해보기

양식 인식기 스튜디오 또는 샘플 레이블 도구를 사용 하 여 비즈니스 카드에서 이름, 직함, 주소, 전자 메일, 회사 이름 등의 데이터를 추출 하는 방법을 확인 합니다. 다음이 필요 합니다.

* Azure 구독 – [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)

* Azure Portal의 [폼 인식기 인스턴스입니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) . 무료 가격 책정 계층(`F0`)을 사용하여 서비스를 시도할 수 있습니다. 리소스를 배포한 후 **리소스로 이동** 을 선택 하 여 API 키 및 끝점을 가져옵니다.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio(미리 보기)

> [!NOTE]
> 양식 인식기 스튜디오는 preview (v 3.0) API에서 사용할 수 있습니다.

1. 폼 인식기 스튜디오 홈 페이지에서 **비즈니스 카드** 를 선택 합니다.

1. 샘플 비즈니스 카드를 분석 하거나 **+ 추가** 단추를 선택 하 여 사용자 고유의 샘플을 업로드할 수 있습니다.

1. **분석** 단추를 선택 합니다.

    :::image type="content" source="media/studio/business-card-analyze.png" alt-text="스크린샷: 비즈니스 카드 분석 메뉴.":::

    > [!div class="nextstepaction"]
    > [폼 인식기 스튜디오 체험](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=businessCard)

#### <a name="sample-labeling-tool"></a>샘플 레이블 지정 도구

비즈니스 카드 문서가 필요 합니다. [샘플 비즈니스 카드 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/businessCard.png)를 사용할 수 있습니다.

  1. 샘플 레이블 지정 도구 홈 페이지에서 미리 작성 된 **모델을 사용 하 여 데이터 가져오기를** 선택 합니다.

  1. **양식 유형** 드롭다운 메뉴에서 **비즈니스 카드** 를 선택 합니다.

      :::image type="content" source="media/try-business-card.png" alt-text="스크린샷: 샘플 레이블 지정 도구 드롭다운 미리 작성 한 모델 선택 메뉴입니다.":::

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

>[!NOTE]
 > 로캘을 지정할 필요가 없습니다. 선택적 매개 변수입니다. Form Recognizer 딥 러닝 기술은 이미지의 텍스트에 사용된 언어를 자동으로 검색합니다.

| 모델 | 언어-로캘 코드 | 기본값 |
|--------|:----------------------|:---------|
|명함| <ul><li>영어 (미국) — en-us</li><li> 영어 (오스트레일리아) — en-us</li><li>영어 (캐나다) — en-CA</li><li>영어 (영국)-GB</li><li>영어 (인도)-en-us</li></ul>  | 자동 검색 됨 |

## <a name="field-extraction"></a>필드 추출

|이름| Type | 설명 |표준화 된 출력 |
|:-----|:----|:----|:----:|
| ContactNames | 개체의 배열 | 담당자 이름 |  |
| FirstName | String | 연락처의 이름 |  |
| LastName | String | 연락처의 성 |  |
| CompanyNames | 문자열 배열 | 회사 이름|  |
| 부서 | 문자열 배열 | 부서 또는 연락처의 조직 |  |
| JobTitles | 문자열 배열 | 연락처의 나열 된 작업 제목 |  |
| 전자 메일 | 문자열 배열 | 연락처 전자 메일 주소 |  |
| Websites | 문자열 배열 | 회사 웹 사이트 |  |
| 주소 | 문자열 배열 | 비즈니스 카드에서 추출 된 주소 | |
| MobilePhones | 전화 번호 배열 | 명함에서 휴대폰 번호 |+ 1 xxx xxx xxxx |
| Faxes | 전화 번호 배열 | 회사 카드의 팩스 전화 번호 | + 1 xxx xxx xxxx |
| WorkPhones | 전화 번호 배열 | 명함의 회사 전화 번호 | + 1 xxx xxx xxxx |
| OtherPhones     | 전화 번호 배열 | 명함에서 기타 전화 번호 | + 1 xxx xxx xxxx |

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
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)
