---
title: 폼 인식기 ID 문서 모델
titleSuffix: Azure Applied AI Services
description: 미리 빌드된 ID 문서 모델을 사용 하 여 데이터 추출 및 분석을 포괄 하는 개념
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: d598c3af52f8b62b23b49f9d661a79a2979574a0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027307"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-id-document-model"></a>폼 인식기 ID 문서 모델

ID 문서 모델은 강력한 OCR (광학 인식) 기능을 심층 학습 모델과 결합 하 여 미국 드라이버 라이선스 (특별구의 모든 50 상태 및 지역) 및 국제 passport biographical 페이지 (해당 여행 문서 제외)의 주요 정보를 분석 하 고 추출 합니다. API는 id 문서를 분석 합니다. 이름, 성, 주소, 생년월일 등의 주요 정보를 추출 합니다. 및는 구조화 된 JSON 데이터 표현을 반환 합니다.

***양식 인식기 스튜디오를 사용 하 여 처리 되는 샘플 미국 드라이버 라이선스***

:::image type="content" source="media/studio/drivers-license.png" alt-text="샘플 식별 카드" lightbox="media/overview-id.jpg":::

## <a name="development-options"></a>개발 옵션

다음 리소스는 폼 인식기 v 2.1에서 지원 됩니다.

| 기능 | 리소스 |
|----------|-------------------------|
|**ID 문서 모델**| <ul><li>[**Form Recognizer 레이블 지정 도구**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-identity-id-documents)</li><li>[**클라이언트 라이브러리 SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker 컨테이너**](containers/form-recognizer-container-install-run.md?tabs=id-document#run-the-container-with-the-docker-compose-up-command)</li></ul>|

다음 리소스는 폼 인식기 v 3.0에서 지원 됩니다.

| 기능 | 리소스 | 모델 ID |
|----------|-------------|-----------|
|**ID 문서 모델**|<ul><li> [**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript SDK**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**미리 빌드된-idDocument**|

### <a name="try-form-recognizer"></a>Form Recognizer 사용해보기

형식 인식기 스튜디오 또는 샘플 레이블 지정 도구를 사용 하 여 이름, 생년월일, 컴퓨터에서 읽을 수 있는 영역 및 만료 날짜를 비롯 한 데이터를 ID 문서에서 추출 하는 방법을 참조 하세요. 다음이 필요 합니다.

* Azure 구독 – [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)

* Azure Portal의 [폼 인식기 인스턴스입니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) . 무료 가격 책정 계층(`F0`)을 사용하여 서비스를 시도할 수 있습니다. 리소스를 배포한 후 **리소스로 이동** 을 선택 하 여 API 키 및 끝점을 가져옵니다.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio(미리 보기)

> [!NOTE]
> 양식 인식기 스튜디오는 preview (v 3.0) API에서 사용할 수 있습니다.

1. 폼 인식기 스튜디오 홈 페이지에서 **송장** 을 선택 합니다.

1. 샘플 송장을 분석 하거나 **+ 추가** 단추를 선택 하 여 사용자 고유의 샘플을 업로드할 수 있습니다.

1. **분석** 단추를 선택 합니다.

    :::image type="content" source="media/studio/id-document-analyze.png" alt-text="스크린샷: ID 문서 분석 메뉴.":::

    > [!div class="nextstepaction"]
    > [폼 인식기 스튜디오 체험](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument)

#### <a name="sample-labeling-tool"></a>샘플 레이블 지정 도구

ID 문서가 필요 합니다. [샘플 ID 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png)를 사용할 수 있습니다.

1. 샘플 레이블 지정 도구 홈 페이지에서 미리 작성 된 **모델을 사용 하 여 데이터 가져오기를** 선택 합니다.

1. **양식 유형** 드롭다운 메뉴에서 **id 문서** 를 선택 합니다.

    :::image type="content" source="media/try-id-document.png" alt-text="스크린샷: 샘플 레이블 지정 도구 드롭다운 미리 작성 한 모델 선택 메뉴입니다.":::

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

## <a name="supported-languages-and-locales-v21"></a>지원 되는 언어 및 로캘 v 2.1

| 모델 | 언어-로캘 코드 | 기본값 |
|--------|:----------------------|:---------|
|ID 문서| <ul><li>영어 (미국) — en-us (드라이버 라이선스)</li><li>국제 여권의 Biographical 페이지</br> (상대 및 기타 여행 문서 제외)</li></ul></br>|영어 (미국) — en-us|

## <a name="field-extraction"></a>필드 추출

|이름| Type | 설명 | 표준화 된 출력|
|:-----|:----|:----|:----|
|  CountryRegion | countryRegion | ISO 3166 표준을 준수하는 국가 또는 지역 코드 |  |
|  DateOfBirth | Date | DOB | yyyy-mm-dd |
|  DateOfExpiration | Date | 만료 날짜 DOB | yyyy-mm-dd |
|  DocumentNumber | String | 관련 passport 번호, 운전 면허 번호 등 |  |
|  FirstName | String | 지정된 이름 및 중간 이니셜(해당하는 경우)을 추출했습니다 |  |
|  LastName | String | 추출된 성 |  |
|  Nationality | countryRegion | ISO 3166 표준을 준수하는 국가 또는 지역 코드(Passport만 해당) |  |
|  성별 | String | 추출 가능한 값에는 "M", "F" 및 "X"가 포함됩니다 | |
|  MachineReadableZone | Object | 두 줄의 44 문자를 포함하여 Passport MRZ을 추출했습니다 | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
|  DocumentType | String | 문서 유형(예: 여권, 운전 면허증) | "passport" |
|  주소 | String | 추출된 주소(운전 면허증에만 해당) ||
|  지역 | String | 추출된 지역, 시, 도 등(운전 면허증에만 해당) |  |

## <a name="form-recognizer-preview-v30"></a>Form Recognizer 미리 보기 v3.0

 Form Recognizer 미리 보기에는 다음과 같은 몇 가지 새로운 기능이 도입되었습니다.

* **ID 문서(v3.0)** 모델은 미국 운전면허에서 보증, 제한 및 차량 분류 추출을 지원합니다.

### <a name="id-document-preview-field-extraction"></a>ID 문서 미리 보기 필드 추출

|이름| Type | 설명 | 표준화된 출력|
|:-----|:----|:----|:----|
| 🆕 보증 | String | 자전거 또는 학교 버스와 같은 드라이버에 부여된 추가 주행 권한입니다.  | |
| 🆕 제한 사항 | String | 일시 중단되거나 취소된 라이선스에 적용할 수 있는 제한된 운전 권한| |
| 🆕VehicleClassification | String | 드라이버에서 구동할 수 있는 차량 유형입니다. ||
|  CountryRegion | countryRegion | ISO 3166 표준을 준수하는 국가 또는 지역 코드 |  |
|  DateOfBirth | Date | 생년월일 | yyyy-mm-dd |
|  DateOfExpiration | Date | 만료 날짜 DOB | yyyy-mm-dd |
|  DocumentNumber | String | 관련 passport 번호, 운전 면허 번호 등 |  |
|  FirstName | String | 지정된 이름 및 중간 이니셜(해당하는 경우)을 추출했습니다 |  |
|  LastName | String | 추출된 성 |  |
|  Nationality | countryRegion | ISO 3166 표준을 준수하는 국가 또는 지역 코드(Passport만 해당) |  |
|  성별 | String | 추출 가능한 값에는 "M", "F" 및 "X"가 포함됩니다 | |
|  MachineReadableZone | Object | 두 줄의 44 문자를 포함하여 Passport MRZ을 추출했습니다 | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
|  DocumentType | String | 문서 유형(예: 여권, 운전 면허증) | "passport" |
|  주소 | String | 추출된 주소(운전 면허증에만 해당) ||
|  지역 | String | 추출된 지역, 시, 도 등(운전 면허증에만 해당) |  |

### <a name="migration-guide-and-rest-api-v30"></a>마이그레이션 가이드 및 REST API v3.0

* Form Recognizer [**v3.0 마이그레이션 가이드에**](v3-migration-guide.md) 따라 애플리케이션 및 워크플로에서 미리 보기 버전을 사용하는 방법을 알아봅니다.

* 미리 보기 버전 및 새로운 기능에 대해 자세히 알아보려면 [**REST API(미리 보기)를**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) 살펴보세요.

## <a name="next-steps"></a>다음 단계

* Form Recognizer 빠른 시작 완료:

  > [!div class="nextstepaction"]
  > [Form Recognizer 빠른 시작](quickstarts/try-sdk-rest-api.md)

* REST API 살펴보세요.

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)
