---
title: 사용자 지정 엔터티를 사용하여 Cognitive Search 인덱스 보강
titleSuffix: Azure Cognitive Services
description: 사용자 지정 NER(명명된 엔터티 인식)을 사용하여 Cognitive Search 인덱스를 향상시킵니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: tutorial
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 555269beeb5e8b36597998a8941e2b64120691b4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484171"
---
# <a name="tutorial-enrich-a-cognitive-search-index-with-custom-entities-from-your-data"></a>자습서: 데이터에서 사용자 지정 엔터티를 사용하여 Cognitive Search 인덱스 보강

기업에서 전자 문서가 많다는 것은 전자 문서를 검색하는 데 시간과 비용이 많이 든다는 것을 의미할 수 있습니다. [Azure Cognitive Search](/azure/search/search-create-service-portal)는 인덱스를 기반으로 하여 파일을 검색하는 데 도움이 될 수 있습니다. 사용자 지정 NER은 파일에서 관련 엔터티를 추출하고 이러한 파일을 인덱싱하는 프로세스를 보강하여 도움이 될 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

* 사용자 지정 명명된 엔터티 인식 프로젝트를 만듭니다.
* Azure Function을 게시합니다.
* 인덱스를 Azure Cognitive Search에 추가합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure Blob 스토리지 계정에 연결된 Azure 언어 리소스](../how-to/create-project.md).
    * 더 쉽게 설정하려면 Azure Portal을 사용하여 리소스를 만드는 지침을 따르는 것이 좋습니다. 
* 현재 구독의 [Azure Cognitive Search Service](/azure/search/search-create-service-portal)
    * 모든 계층 및 모든 지역을 이 서비스에 사용할 수 있습니다.
* [Azure 함수 앱](/azure/azure-functions/functions-create-function-app-portal)
* 이 [샘플 데이터](https://go.microsoft.com/fwlink/?linkid=2175226)를 다운로드합니다.

## <a name="create-a-custom-ner-project-through-language-studio"></a>언어 스튜디오를 통해 사용자 지정 NER 프로젝트 만들기

1. [언어 스튜디오 포털](https://aka.ms/LanguageStudio)을 통해 로그인하고, **사용자 지정 엔터티 추출** 을 선택합니다.

2. 언어 리소스를 선택합니다. 리소스 및 스토리지 계정에 대해 [ID 관리와 역할을 사용하도록 설정](../how-to/create-project.md#enable-identity-management-for-your-resource)했는지 확인합니다.

3. 프로젝트 화면의 위쪽에서 **새 프로젝트 만들기** 를 선택합니다. 요청되면 표시되는 메뉴에서 스토리지 계정을 선택합니다.

    :::image type="content" source="../media/create-project.png" alt-text="프로젝트 만들기 페이지의 스크린샷" lightbox="../media/create-project.png":::

4. 다음과 같은 프로젝트 정보를 입력합니다.

    | 키 | Description |
    |--|--|
    | Name | 프로젝트의 이름입니다. 프로젝트가 만들어지면 해당 이름을 바꿀 수 없습니다. |
    | 설명 | 프로젝트에 대한 설명 |
    | 언어 | 프로젝트에 있는 파일의 언어입니다.|

    > [!NOTE]
    > 문서가 여러 언어로 있는 경우 프로젝트 만들기에서 **여러 언어** 옵션을 선택하고 **언어** 옵션을 대부분의 문서 언어로 설정합니다.

5. 이 자습서에서는 **기존 태그 파일** 을 사용하고, 샘플 데이터에서 다운로드한 태그 파일을 선택합니다.

## <a name="train-your-model"></a>모델 학습

1. 왼쪽 메뉴에서 **학습** 을 선택합니다.

2. 새 모델을 학습시키려면 **새 모델 학습** 을 선택하고, 아래 텍스트 상자에서 모델 이름을 입력합니다.

    :::image type="content" source="../media/train-model.png" alt-text="새 모델 만들기" lightbox="../media/train-model.png":::

3. 페이지 아래쪽에서 **학습** 단추를 선택합니다.

4. 학습이 완료되면 [모델의 평가 세부 정보를 보고](../how-to/view-model-evaluation.md) [모델을 향상](../how-to/improve-model.md)시킬 수 있습니다.

## <a name="deploy-your-model"></a>모델 배포

1. 왼쪽 메뉴에서 **모델 배포** 를 선택합니다.

2. 배포하려는 모델을 선택하고, 위쪽 메뉴에서 **모델 배포** 를 클릭합니다. 학습이 성공적으로 완료된 모델만 표시됩니다.

## <a name="prepare-your-secrets-for-the-azure-function"></a>Azure 함수에 대한 비밀 준비

다음으로, Azure 함수에 대한 비밀을 준비해야 합니다. 프로젝트 비밀은 다음과 같습니다. 
* 엔드포인트
* 리소스 키
* 모델 ID

### <a name="get-your-custom-ner-project-secrets"></a>사용자 지정 NER 프로젝트 비밀 가져오기

* 대/소문자를 구분하는 **프로젝트 이름** 이 필요합니다.

* 배포 슬롯도 필요합니다. 
   * 언어 스튜디오를 통해 모델을 배포한 경우 배포 슬롯은 기본적으로 `prod`입니다. 
   * API를 사용하여 모델을 프로그래밍 방식으로 배포한 경우 요청에서 할당한 배포 이름입니다.

### <a name="get-your-resource-keys-endpoint"></a>리소스 키 엔드포인트 가져오기

1. [Azure Portal](https://ms.portal.azure.com/#home)에서 리소스로 이동합니다.

2. 왼쪽 메뉴에서 **키 및 엔드포인트** 를 선택합니다. API 요청에 대한 엔드포인트 및 키 중 하나가 필요합니다.

    :::image type="content" source="../../media/azure-portal-resource-credentials.png" alt-text="Azure Portal의 키 및 엔드포인트 화면을 보여 주는 스크린샷" lightbox="../../media/azure-portal-resource-credentials.png":::
   
## <a name="edit-and-deploy-your-azure-function"></a>Azure Function 편집 및 배포

1. [제공된 샘플 함수](https://aka.ms/ct-cognitive-search-integration-tool)를 다운로드하여 사용합니다.

2. 샘플 함수가 다운로드되면 *program.cs* 파일을 열고 앱 비밀을 입력합니다.

3. [함수를 Azure에 게시](/azure/azure-functions/functions-develop-vs?tabs=in-process#publish-to-azure)합니다.

## <a name="use-the-integration-tool"></a>통합 도구 사용

다음 섹션에서는 [Cognitive Search 통합 도구](https://aka.ms/ct-cognitive-search-integration-tool)를 사용하여 프로젝트를 Azure Cognitive Search와 통합합니다. 지금 이 리포지토리를 다운로드합니다. 

### <a name="prepare-configuration-file"></a>구성 파일 준비

1. 방금 다운로드한 폴더에서 [샘플 구성 파일](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/dev/CustomTextAnalytics.CognitiveSearch/Samples/configs.json)을 찾습니다. 텍스트 편집기에서 엽니다. 

2. 다음을 수행하여 스토리지 계정 연결 문자열을 가져옵니다.
    1. [Azure Portal](https://ms.portal.azure.com/#home)에서 스토리지 계정 개요 페이지로 이동합니다.
    2. 화면 위쪽 섹션에서 컨테이너 이름을 구성 파일의 `blobStorage` 아래에 있는 `containerName` 필드에 복사합니다.  
    3. 화면 왼쪽 메뉴의 **액세스 키** 섹션에서 **연결 문자열** 을 구성 파일의 `blobStorage` 아래에 있는 `connectionString` 필드에 복사합니다.

1. 다음을 수행하여 Cognitive Search 엔드포인트 및 키를 가져옵니다.
    1. [Azure Portal](https://ms.portal.azure.com/#home)에서 스토리지 계정 개요 페이지로 이동합니다.
    2. 페이지의 오른쪽 위 섹션에서 **URL** 을 `cognitiveSearch` 내의 `endpointUrl` 필드에 복사합니다.
    3. 화면 왼쪽 메뉴의 **키** 섹션으로 이동합니다. **기본 관리자 키** 를 `cognitiveSearch` 내의 `apiKey` 필드에 복사합니다.

3. Azure Function 엔드포인트 및 키 가져오기
    
    1. Azure Function 엔드포인트 및 키를 가져오려면 [Azure Portal](https://ms.portal.azure.com/#home)의 함수 개요 페이지로 이동합니다.
    2. 화면 왼쪽의 **Functions** 메뉴로 이동하여 만든 함수를 클릭합니다.
    3. 위쪽 메뉴에서 **함수 URL 가져오기** 를 클릭합니다. URL은 `YOUR-ENDPOINT-URL?code=YOUR-API-KEY` 형식으로 지정됩니다. 
    4. `YOUR-ENDPOINT-URL`을 구성 파일의 `azureFunction` 아래에 있는 `endpointUrl` 필드에 복사합니다. 
    5. `YOUR-API-KEY`를 구성 파일의 `azureFunction` 아래에 있는 `apiKey` 필드에 복사합니다. 

### <a name="prepare-schema-file"></a>스키마 파일 준비

이전에 다운로드한 폴더에서 [샘플 스키마 파일](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/dev/CustomTextAnalytics.CognitiveSearch/Samples/app-schema.json)을 찾습니다. 텍스트 편집기에서 엽니다. 

`entityNames` 배열의 항목은 프로젝트를 만드는 동안 할당한 엔터티 이름이 됩니다. [언어 스튜디오](https://aka.ms/custom-extraction)의 프로젝트에서 복사하여 붙여넣을 수 있거나, 

### <a name="run-the-index-command"></a>`Index` 명령을 실행할 수 있습니다.

구성 파일 및 스키마 파일이 완성되면 프로젝트를 인덱싱할 수 있습니다. 구성 파일을 CLI 도구와 동일한 경로에 배치하고 다음 명령을 실행합니다.

```cli
    indexer index --schema <path/to/your/schema> --index-name <name-your-index-here>
```

`name-your-index-here`를 Cognitive Search 인스턴스에 표시되는 인덱스 이름으로 바꿉니다.

## <a name="next-steps"></a>다음 단계

* [Cognitive Search SDK를 사용하여 앱 검색](/azure/search/search-howto-dotnet-sdk#run-queries)
