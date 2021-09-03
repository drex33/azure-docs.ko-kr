---
title: IDs - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Form Recognizer 미리 빌드된 ID API를 사용하여 ID 문서에서 데이터 추출과 관련된 개념을 알아봅니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: bfa6cb13533a92c65369b8016971e0c1e37cdb61
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538731"
---
# <a name="form-recognizer-prebuilt-identification-id-document-model"></a>미리 빌드된 ID(ID) 문서 모델 Form Recognizer

Azure Form Recognizer는 미리 빌드한 ID 모델을 사용하여 정부에서 발급한 ID(ID 문서)에서 정보를 분석하고 추출할 수 있습니다. 강력한 [OCR(광학 문자 인식)](../../cognitive-services/computer-vision/overview-ocr.md) 기능을 ID 인식 기능과 결합하여 전 세계 Passport 및 미국 운전면허(모든 50개 주 및 D.C.)에서 주요 정보를 추출합니다. ID API는 이름, 성, 생년월일, 문서 번호 등과 같은 ID 문서에서 주요 정보를 추출합니다. 이 API는 Form Recognizer v2.1에서 클라우드 서비스로 사용할 수 있습니다. 

## <a name="customer-scenarios"></a>고객 시나리오

ID API를 사용하여 추출된 데이터는 재무, 의료 및 보험, 정부 등을 비롯한 업계에서 KYC(Know Your Customer)와 같은 시나리오에 대한 다양한 작업을 수행하는 데 사용할 수 있습니다. 다음은 몇 가지 예입니다.

* 디지털 온보딩 - 최종 사용자는 모바일 애플리케이션을 사용하여 해당 ID를 검색하고 다양한 서비스에 온보딩할 수 있습니다. 원격 고객 검증은 ID 데이터 추출을 통해 지원합니다. 

* 유효성 검사 및 ID 일치 - 최종 사용자는 애플리케이션을 채우고 ID 이미지를 연결할 수 있습니다. 미리 빌드된 ID를 사용하여 은행에서는 정보가 현재 데이터와 일치하는지 확인할 수 있습니다.

* 양식 사전 채우기 - 보험 청구 프로세스의 일부로 최종 사용자는 자신의 ID를 제출하고 필드는 온라인 문서에 미리 채워져 프로세스 시간이 절감됩니다.

또한 ID API는 [AI Builder ID 판독기 기능](/ai-builder/prebuilt-id-reader)을 제공합니다.

## <a name="try-it"></a>시도

Form Recognizer ID 서비스를 사용해보려면 다음과 같은 온라인 샘플 UI 도구로 이동합니다.

> [!div class="nextstepaction"]
> [ID 문서 모델 사용해 보기](https://aka.ms/fott-2.1-ga "사전 구축된 모델로 시작하여 ID 문서에서 데이터를 추출합니다.")

## <a name="what-does-the-id-service-do"></a>ID 서비스는 어떤 작업을 수행하나요?

미리 구성된 ID 서비스는 전 세계 Passport 및 미국 운전면허증에서 키 값을 추출하여 구조화된 JSON 응답으로 반환합니다.

### <a name="drivers-license-example"></a>**운전 면허증 예제**

![샘플 운전 면허증](./media/id-example-drivers-license.JPG)

### <a name="passport-example"></a>**Passport 예제**

![샘플 Passport](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>추출된 필드

|이름| 유형 | Description | 값(표준화된 출력) |
|:-----|:----|:----|:----|
|  CountryRegion | countryRegion | ISO 3166 표준을 준수하는 국가 또는 지역 코드 | "USA" |
|  DateOfBirth | date | YYYY-MM-DD 형식의 DOB | "1980-01-01" |
|  DateOfExpiration | date | YYYY-MM-DD 형식의 만료 날짜 | "2019-05-05" |
|  DocumentNumber | 문자열 | 관련 passport 번호, 운전 면허 번호 등 | "340020013" |
|  FirstName | 문자열 | 지정된 이름 및 중간 이니셜(해당하는 경우)을 추출했습니다 | "JENNIFER" |
|  LastName | 문자열 | 추출된 성 | "BROOKS" |
|  Nationality | countryRegion | ISO 3166 표준을 준수하는 국가 또는 지역 코드 | "USA" |
|  성별 | 문자열 | 추출 가능한 값에는 "M", "F" 및 "X"가 포함됩니다 | "F" |
|  MachineReadableZone | 개체 | 두 줄의 44 문자를 포함하여 Passport MRZ을 추출했습니다 | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
|  DocumentType | 문자열 | 문서 유형(예: 여권, 운전 면허증) | "passport" |
|  주소 | 문자열 | 추출된 주소(운전 면허증에만 해당) | "123 주소에서 도시 WA 99999-1234"|
|  지역 | 문자열 | 추출된 지역, 시, 도 등(운전 면허증에만 해당) | "워싱턴" |

### <a name="additional-features"></a>추가 기능

또한 ID API는 다음 정보를 반환합니다.

* 필드 신뢰 수준(각 필드는 연결된 신뢰도의 값을 반환)
* OCR 원시 텍스트(엔터티 ID 문서에 대해 OCR 추출된 텍스트 출력)
* 미국 운전 면허증의 추출된 각 필드에 대한 경계 상자
* 여권의 MRZ(컴퓨터 판독 가능 영역)에 대한 경계 상자

  > [!NOTE]
  > 미리 빌드된 ID가 ID 신뢰성을 검색하지 않음
  >
  > Form Recognizer 미리 빌드된 ID는 ID 데이터에서 키 데이터를 추출합니다. 그러나 원래 ID 문서의 유효성 또는 신뢰성은 검색하지 않습니다.

## <a name="input-requirements"></a>입력 요구 사항

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-identity-document-types"></a>지원되는 ID 문서 유형

* **미리 빌드된 ID v2.1** 은 전 세계 Passport 및 미국 운전면허증에서 키 값을 추출합니다.

  > [!NOTE]
  > ID 형식 지원
  >
  > 현재 지원되는 ID 형식에는 전 세계 Passport 및 미국 운전면허증이 포함됩니다. ID 지원을 전 세계의 다른 ID 문서로 확장하기 위해 적극적으로 노력하고 있습니다.

## <a name="analyze-id-document"></a>ID 문서 분석

[ID 분석](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7daad1f2612c46f5822) 작업은 ID의 이미지 또는 PDF를 입력으로 사용하여 관심있는 값을 추출합니다. 호출은 `Operation-Location`이라는 응답 헤더 필드를 반환합니다. `Operation-Location` 값은 다음 단계에서 사용할 결과 ID를 포함하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>ID 문서 분석 결과 가져오기

<!---
Need to update this with updated APIM links when available
-->

두 번째 단계는 [**ID 문서 분석 결과 가져오기**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707) 작업을 호출하는 것입니다. 이 작업은 ID 분석 작업으로 만들어진 결과 ID를 입력으로 사용합니다. 다음과 같은 가능한 값을 가진 **상태** 필드가 포함된 JSON 응답을 반환합니다. **succeeded** 값이 반환될 때까지 이 작업을 반복적으로 호출합니다. 3~5초의 간격을 사용하여 RPS(초당 요청 수) 속도를 초과하지 않도록 합니다.

|필드| 형식 | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | notStarted: 분석 작업이 시작되지 않았습니다. |
| |  | running: 분석 작업이 진행 중입니다. |
| |  | failed: 분석 작업이 실패했습니다. |
| |  | succeeded: 분석 작업이 성공했습니다. |

**상태** 필드의 값이 **성공** 이면 JSON 응답에는 영수증 해석 및 텍스트 인식 결과가 포함됩니다. ID 결과는 명명된 필드 값의 사전으로 구성됩니다. 여기서 각 값은 추출된 텍스트, 정규화된 값, 경계 상자, 신뢰도 및 해당 단어 요소를 포함합니다. 텍스트 인식 결과는 텍스트, 경계 상자 및 신뢰도 정보를 사용하여 줄과 단어의 계층 구조로 정리됩니다.

![샘플 영수증 결과](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>샘플 JSON 출력

성공적인 JSON 응답의 다음 예를 참조하세요(단순성을 위해 출력이 단축됨). `readResults` 노드에는 인식된 모든 텍스트가 포함됩니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다. `documentResults` 노드에는 모델이 검색한 ID 값이 포함됩니다. 이 노드는 또한 이름, 성, 문서 번호 등과 같은 유용한 키/값 쌍을 찾을 수 있습니다.

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
          }
        ]
      }
    ],

     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "CountryRegion": {
            "type": "countryRegion",
            "valueCountryRegion": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "countryRegion",
            "valueCountryRegion": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "string",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>다음 단계

* 사용자 고유의 ID와 샘플을 [Form Recognizer 샘플 UI](https://aka.ms/fott-2.1-ga)에서 사용해 보세요.
* 사용자가 선택한 개발 언어로 Form Recognizer를 사용하여 ID 처리 앱 작성을 시작하려면 [Form Recognizer 빠른 시작](quickstarts/client-library.md)을 완료하세요.


