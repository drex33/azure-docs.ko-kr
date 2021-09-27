---
title: Form Recognizer 사용자 지정 기술(C#)
titleSuffix: Azure Cognitive Search
description: C# 및 Visual Studio를 사용하여 Form Recognizer 사용자 지정 기술을 만드는 방법을 알아봅니다.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: b5074e3d07a1df199e189dfb906190c82bde6905
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124796593"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>예: Form Recognizer 사용자 지정 기술 만들기

이 Azure Cognitive Search 기술 예에서는 C# 및 Visual Studio를 사용하여 Form Recognizer 사용자 지정 기술을 만드는 방법을 알아봅니다. Form Recognizer는 문서를 분석하고 키/값 쌍 및 테이블 데이터를 추출합니다. Form Recognizer를 [사용자 지정 기술 인터페이스](cognitive-search-custom-skill-interface.md)로 래핑하면 엔드투엔드 보강 파이프라인의 단계로 이 기능을 추가할 수 있습니다. 그러면 파이프라인이 문서를 로드하고 다른 변환을 수행할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 모든 버전.
- 동일한 형식의 5개 이상의 양식 이 가이드에서 제공하는 샘플 데이터를 사용할 수 있습니다.

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

[!INCLUDE [create resource](../applied-ai-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>모델 학습

이 기술을 사용하기 전에 입력 양식을 사용하여 Form Recognizer 모델을 교육해야 합니다. 모델 학습 방법을 알아보려면 [cURL 빠른 시작](../applied-ai-services/form-recognizer/quickstarts/client-library.md?pivots=programming-language-rest-api)을 따릅니다. 해당 빠른 시작에 제공된 샘플 양식을 사용하거나 사용자 고유의 데이터를 사용할 수 있습니다. 모델을 학습한 후에는 해당 ID 값을 안전한 위치에 복사합니다.

## <a name="set-up-the-custom-skill"></a>사용자 지정 기술 설정

이 자습서에서는 [Azure Search 파워 기술](https://github.com/Azure-Samples/azure-search-power-skills) GitHub 리포지토리에 있는 [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/main/Vision/AnalyzeForm) 프로젝트를 사용합니다. 이 리포지토리를 로컬 컴퓨터에 복제하고 **Vision/AnalyzeForm/** 으로 이동하여 프로젝트에 액세스합니다. 그런 다음 Visual Studio에서 _AnalyzeForm.csproj_ 를 엽니다. 이 프로젝트는 [사용자 지정 기술 인터페이스](cognitive-search-custom-skill-interface.md)를 충족하고 Azure Cognitive Search 보강에 사용할 수 있는 Azure 함수 리소스를 만듭니다. 양식 문서를 입력으로 사용하고 지정한 키/값 쌍을 텍스트로 출력합니다.

먼저 프로젝트 수준 환경 변수를 추가합니다. 왼쪽 창에서 **AnalyzeForm** 프로젝트를 찾은 다음 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다. **속성** 창에서 **디버그** 탭을 클릭한 다음 **환경 변수** 필드를 찾습니다. **추가** 를 클릭하여 다음 변수를 추가합니다.
* `FORMS_RECOGNIZER_ENDPOINT_URL` - 이 값을 엔드포인트 URL로 설정합니다.
* `FORMS_RECOGNIZER_API_KEY` - 이 값을 구독 키로 설정합니다.
* `FORMS_RECOGNIZER_MODEL_ID` - 이 값을 학습한 모델의 ID로 설정합니다.
* `FORMS_RECOGNIZER_RETRY_DELAY` - 이 값을 1000으로 설정합니다. 이 값은 프로그램에서 쿼리를 다시 시도하기 전까지 대기하는 시간(밀리초)입니다.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS` - 이 값을 100으로 설정합니다. 이 값은 성공적인 응답을 가져오려고 시도하는 동안 프로그램에서 서비스를 쿼리하는 횟수입니다.

그런 다음 _AnalyzeForm.cs_ 를 열고 `fieldMappings` *field-mappings.json* 파일을 참조하는 변수를 찾습니다. 이 파일(및 이 파일을 참조하는 변수)은 양식에서 추출할 키 목록과 각 키에 대한 사용자 지정 레이블을 정의합니다. 예를 들어, `{ "Address:", "address" }, { "Invoice For:", "recipient" }` 값은 스크립트가 검색된 `Address:` 및 `Invoice For:` 필드의 값만 저장하고 해당 값에 각각 `"address"` 및 `"recipient"` 레이블을 지정함을 의미합니다.

마지막으로 `contentType` 변수를 확인합니다. 이 스크립트는 URL에서 참조하는 원격 문서의 지정된 Form Recognizer 모델을 실행하므로 콘텐츠 형식은 `application/json`입니다. HTTP 요청에 바이트 스트림을 포함하여 로컬 파일을 분석하려면 `contentType`을 파일에 적합한 [MIME 형식](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types)으로 변경해야 합니다.

## <a name="test-the-function-from-visual-studio"></a>Visual Studio에서 함수 테스트

프로젝트를 편집한 후에는 저장하고 **AnalyzeForm** 프로젝트를 Visual Studio에서 시작 프로젝트로 설정합니다(아직 설정하지 않은 경우). 그런 다음 **F5** 키를 눌러 로컬 환경에서 함수를 실행합니다. [Postman](https://www.postman.com/)과 같은 REST 서비스를 사용하여 함수를 호출합니다.

### <a name="http-request"></a>HTTP 요청

다음 요청을 수행하여 함수를 호출합니다.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>요청 본문

아래의 요청 본문 템플릿으로 시작합니다.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

여기서 학습한 양식과 동일한 형식의 URL을 제공해야 합니다. 테스트 목적으로 학습 양식 중 하나를 사용할 수 있습니다. cURL 빠른 시작을 따른 경우 양식은 Azure Blob Storage 계정에 있습니다. Azure Storage Explorer를 열고 양식 파일을 찾아 마우스 오른쪽 단추로 클릭하고 **공유 액세스 서명 가져오기** 를 선택합니다. 다음 대화 상자 창에서 URL 및 SAS 토큰을 제공합니다. 요청 본문의 `"formUrl"` 및 `"formSasToken"` 필드에 이러한 문자열을 각각 입력합니다.

> [!div class="mx-imgBorder"]
> ![Azure Storage Explorer; PDF 문서가 선택됨](media/cognitive-search-skill-form/form-sas.png)

Azure Blob Storage에 없는 원격 문서를 분석하려면 `"formUrl"` 필드에 해당 URL을 붙여넣고 `"formSasToken"` 필드를 비워 둡니다.

> [!NOTE]
> 기술이 기술 세트에 통합된 경우 URL 및 토큰은 Cognitive Search에서 제공됩니다.

### <a name="response"></a>응답

다음 예제와 유사한 응답이 표시됩니다.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Azure에 함수 게시

함수 동작에 만족하는 경우에는 게시할 수 있습니다.

1. Visual Studio의 **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다. **새로 만들기** > **게시** 를 선택합니다.
1. Visual Studio를 Azure 계정에 아직 연결하지 않았으면 **계정 추가...** 를 선택합니다.
1. 화면에 표시되는 메시지를 따릅니다. 앱 서비스, Azure 구독, 리소스 그룹, 호스팅 계획 및 사용하려는 스토리지 계정에 대한 고유한 이름을 지정합니다. 새 리소스 그룹, 새 호스팅 계획 및 새 스토리지 계정(아직 없는 경우)을 만들 수 있습니다. 작업이 완료되면 **만들기** 를 선택합니다.
1. 배포가 완료되면 사이트 URL을 확인합니다. 이 URL은 Azure에서 해당 함수 앱의 주소입니다. 임시 위치에 저장합니다.
1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동한 다음, 게시한 `AnalyzeForm` 함수를 찾습니다. **관리** 섹션 아래에 호스트 키가 표시됩니다. *기본* 호스트 키를 복사하여 임시 위치에 저장합니다.

## <a name="connect-to-your-pipeline"></a>파이프라인에 연결

Cognitive Search 파이프라인에서 이 기술을 사용하려면 기술 정의를 기술 세트에 추가해야 합니다. 다음 JSON 블록은 샘플 기술 정의입니다(특정 시나리오와 기술 세트 환경을 리플렉션하기 위해 입력 및 출력을 업데이트해야 합니다. `AzureFunctionEndpointUrl`을 함수 URL로 바꾸고 `AzureFunctionDefaultHostKey`를 호스트 키로 바꿉니다.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

이 가이드에서는 Azure Form Recognizer 서비스에서 사용자 지정 기술을 만들었습니다. 사용자 지정 기술에 대한 자세한 내용은 다음 리소스를 참조하세요. 

* [Azure Search 파워 기술: 사용자 지정 기술의 리포지토리](https://github.com/Azure-Samples/azure-search-power-skills)
* [AI 보강 파이프라인에 사용자 지정 기술 추가](cognitive-search-custom-skill-interface.md)
* [기술 집합 정의](cognitive-search-defining-skillset.md)
* [기술 세트 만들기(REST)](/rest/api/searchservice/create-skillset)
* [보강된 필드 매핑](cognitive-search-output-field-mapping.md)