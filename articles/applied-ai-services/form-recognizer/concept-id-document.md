---
title: Form Recognizer ID 문서 모델
titleSuffix: Azure Applied AI Services
description: 미리 작성된 ID 문서 모델을 사용한 데이터 추출 및 분석을 포함하는 개념
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 4b73526ad245d9364b0ada997fc014e0dd16df40
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129717691"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-id-document-model"></a>Form Recognizer ID 문서 모델

ID 문서 모델은 강력한 OCR(광학 문자 인식) 기능을 딥 러닝 모델과 결합하여 미국 운전면허증(50개 주 및 미국 특별구) 및 국제 Passport 구문 페이지(비주얼 및 기타 여행 문서 제외)에서 주요 정보를 분석하고 추출합니다. API는 ID 문서를 분석합니다. 는 이름, 성, 주소 및 생년월일과 같은 주요 정보를 추출합니다. 및 는 구조화 된 JSON 데이터 표현을 반환합니다.

***[Form Recognizer 샘플 레이블 지정 도구를](https://fott-2-1.azurewebsites.net/)사용하여 처리된 샘플 미국 드라이버 라이선스:***

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="샘플 식별 카드" lightbox="./media/overview-id.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Form Recognizer Studio(미리 보기) 사용해 보기

* Form Recognizer Studio는 미리 보기(v3.0) API와 함께 사용할 수 있습니다.

* Form Recognizer Studio ID 문서 기능을 통해 이름, 컴퓨터에서 읽을 수 있는 영역, 만료 날짜를 추출합니다.

> [!div class="nextstepaction"]
> [Form Recognizer Studio 사용해 보세요.](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument)

## <a name="try-it-sample-labeling-tool"></a>사용해 보세요. 샘플 레이블 지정 도구

샘플 레이블 지정 도구를 사용하여 ID 문서 데이터를 추출하는 방법을 확인할 수 있습니다. 다음이 필요합니다.

* Azure 구독 - [체험 구독을 만들](https://azure.microsoft.com/free/cognitive-services/) 수 있습니다.

* [Azure Portal Form Recognizer 인스턴스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) )입니다. 무료 가격 책정 계층(`F0`)을 사용하여 서비스를 시도할 수 있습니다. 리소스가 배포된 후 **리소스로 이동을** 클릭하여 API 키와 엔드포인트를 얻습니다.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal 키 및 엔드포인트 위치.":::

* ID 문서입니다. 샘플 ID [문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/id-license.jpg)를 사용할 수 있습니다.

> [!div class="nextstepaction"]
  > [사용해 보세요.](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

Form Recognizer UI에서 다음을 수행합니다.

  1. **미리 디버전된 모델을 사용하여 데이터 받기를** 선택합니다.
  1. **양식 유형** 드롭다운 메뉴에서 **영수증을** 선택합니다.

  :::image type="content" source="media/try-id-document.png" alt-text="스크린샷: 샘플 레이블 지정 도구 드롭다운 미리 구성된 모델 선택 메뉴.":::

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

## <a name="supported-languages-and-locales-v21"></a>지원되는 언어 및 로케일 v2.1

| 모델 | 언어 - 로케일 코드 | 기본값 |
|--------|:----------------------|:---------|
|ID 문서| <ul><li>영어(미국)-en-US(운전면허)</li><li>국제 Passport의 구문 페이지</br> (경비 및 기타 여행 문서 제외)</li></ul></br>|영어(미국)-en-US|

## <a name="key-value-pair-extraction"></a>키-값 쌍 추출

|Name| Type | 설명 | 표준화된 출력|
|:-----|:----|:----|:----|
|  CountryRegion | countryRegion | ISO 3166 표준을 준수하는 국가 또는 지역 코드 |  |
|  DateOfBirth | date | 생년월일 | yyyy-mm-dd |
|  DateOfExpiration | date | 만료 날짜 DOB | yyyy-mm-dd |
|  DocumentNumber | 문자열 | 관련 passport 번호, 운전 면허 번호 등 |  |
|  FirstName | 문자열 | 지정된 이름 및 중간 이니셜(해당하는 경우)을 추출했습니다 |  |
|  LastName | 문자열 | 추출된 성 |  |
|  Nationality | countryRegion | ISO 3166 표준을 준수하는 국가 또는 지역 코드(Passport만 해당) |  |
|  성별 | 문자열 | 추출 가능한 값에는 "M", "F" 및 "X"가 포함됩니다 | |
|  MachineReadableZone | 개체 | 두 줄의 44 문자를 포함하여 Passport MRZ을 추출했습니다 | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
|  DocumentType | 문자열 | 문서 유형(예: 여권, 운전 면허증) | "passport" |
|  주소 | 문자열 | 추출된 주소(운전 면허증에만 해당) ||
|  지역 | 문자열 | 추출된 지역, 시, 도 등(운전 면허증에만 해당) |  |

## <a name="form-recognizer-preview-v30"></a>Form Recognizer 미리 보기 v3.0

 Form Recognizer 미리 보기에는 다음과 같은 몇 가지 새로운 기능이 도입되었습니다.

* **ID 문서(v3.0)** 모델은 미국 운전면허에서 보증, 제한 및 차량 분류 추출을 지원합니다.

    ### <a name="id-document-preview-key-value-pair-extraction"></a>ID 문서 미리 보기 키-값 쌍 추출

    |Name| Type | 설명 | 표준화 된 출력|
    |:-----|:----|:----|:----|
    | 🆕 인증 | string | Motorcycle 또는 School bus와 같은 드라이버에 부여 된 추가 권한입니다.  | |
    | 🆕 제한 사항 | string | 일시 중단 또는 해지 된 라이선스에 적용 되는 제한 된 구동 권한입니다.| |
    | 🆕 VehicleClassification | string | 드라이버에서 구동 될 수 있는 차량 유형입니다. ||
    |  CountryRegion | countryRegion | ISO 3166 표준을 준수하는 국가 또는 지역 코드 |  |
    |  DateOfBirth | date | DOB | yyyy-mm-dd |
    |  DateOfExpiration | date | 만료 날짜 DOB | yyyy-mm-dd |
    |  DocumentNumber | 문자열 | 관련 passport 번호, 운전 면허 번호 등 |  |
    |  FirstName | 문자열 | 지정된 이름 및 중간 이니셜(해당하는 경우)을 추출했습니다 |  |
    |  LastName | 문자열 | 추출된 성 |  |
    |  Nationality | countryRegion | ISO 3166 표준을 준수 하는 국가 또는 지역 코드 (Passport 전용) |  |
    |  성별 | 문자열 | 추출 가능한 값에는 "M", "F" 및 "X"가 포함됩니다 | |
    |  MachineReadableZone | 개체 | 두 줄의 44 문자를 포함하여 Passport MRZ을 추출했습니다 | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
    |  DocumentType | 문자열 | 문서 유형(예: 여권, 운전 면허증) | "passport" |
    |  주소 | 문자열 | 추출된 주소(운전 면허증에만 해당) ||
    |  지역 | 문자열 | 추출된 지역, 시, 도 등(운전 면허증에만 해당) |  |

* 응용 프로그램 및 워크플로에서 미리 보기 버전을 사용 하는 방법에 대 한 자세한 내용은 [**인식기 v 3.0 마이그레이션 가이드를 참조**](v3-migration-guide.md) 하세요.

* 미리 보기 버전 및 새로운 기능에 대 한 자세한 내용은 [**REST API (미리 보기)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) 를 살펴보세요.

## <a name="next-steps"></a>다음 단계

* 양식 인식기 빠른 시작을 완료 합니다.

  > [!div class="nextstepaction"]
  > [Form Recognizer 빠른 시작](quickstarts/try-sdk-rest-api.md)

* REST API를 살펴보세요.

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)