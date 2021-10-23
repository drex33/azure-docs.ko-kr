---
title: 기술 세트에 Cognitive Services 연결
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 AI 보강 파이프라인에 다중 서비스 Cognitive Services 리소스를 연결하는 방법을 알아보세요.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: 2c86f60bdb6c675331cc7c79e3cf3f6fdf58533c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245964"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Azure Cognitive Search에서 기술 세트에 Cognitive Services 리소스 연결

Azure Cognitive Search에서 선택적 [AI 보강 파이프라인](cognitive-search-concept-intro.md) 을 구성 하는 경우 제한 된 수의 문서를 무료로 보강할 수 있습니다. 더 크고 더 자주 수행 되는 작업의 경우 청구 [**가능 다중 서비스 Cognitive Services 리소스**](../cognitive-services/cognitive-services-apis-create-account.md)를 연결 해야 합니다. 다중 서비스 리소스는 단일 API 키를 통해 액세스 권한이 부여된 개별 서비스가 아닌 제공으로 ‘Cognitive Services’를 참조합니다.

기술에는 다중 서비스 리소스 키가 지정 되어 있으며 Microsoft에서 다음 Api를 사용 하 여 요금을 청구할 수 있습니다.

+ 이미지 분석 및 OCR(광학 인식)을 위한 [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
+ 언어 검색, 엔터티 인식, 감정 분석 및 핵심 구 추출을 위한 [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)
+ [텍스트 번역](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

> [!NOTE]
> AI 보강는 Cognitive Services 리소스를 연결 하지 않고도 빠른 시작, 자습서 또는 제한 된 테스트를 완료할 수 있도록 적은 양의 여유 처리를 제공 합니다. 무료 강화 인덱서 당 하루 20 개의 문서로 제한 됩니다. 연습을 반복 하려면 [인덱서를 다시 설정](search-howto-run-reset-indexers.md) 하 여 카운터를 다시 설정할 수 있습니다.

## <a name="azure-portal"></a>[**Azure portal**](#tab/cogkey-portal)

1. 검색 서비스와 [동일한 지역](#same-region-requirement) 에 [다중 서비스 Cognitive Services 리소스](../cognitive-services/cognitive-services-apis-create-account.md) 를 만듭니다.

1. 기술 정의에 키를 추가 합니다.

   + [데이터 가져오기 마법사](search-import-data-portal.md)를 사용 하는 경우 두 번째 단계인 "AI 강화 추가"에서 키를 입력 합니다.

   + 새 기술 또는 기존 키를 추가 하는 경우에는 **Cognitive Services** 탭에 키를 입력 합니다.

   :::image type="content" source="media/cognitive-search-attach-cognitive-services/attach-existing2.png" alt-text="키 페이지의 스크린샷" border="true":::

## <a name="rest"></a>[**REST**](#tab/cogkey-rest)

1. 검색 서비스와 [동일한 지역](#same-region-requirement) 에 [다중 서비스 Cognitive Services 리소스](../cognitive-services/cognitive-services-apis-create-account.md) 를 만듭니다.

1. `cognitiveServices` [기술 요청의](/rest/api/searchservice/create-skillset)본문에 섹션을 지정 하 여 기술를 만들거나 업데이트 합니다.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="net-sdk"></a>[**.NET SDK**](#tab/cogkey-dotnet)

다음 코드 조각은 간단 하 게 하기 위해 잘린 [azure-검색-dotnet-샘플](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/tutorial-ai-enrichment/v11/Program.cs)에서 가져온 것입니다.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();

string searchServiceUri = configuration["SearchServiceUri"];
string adminApiKey = configuration["SearchServiceAdminApiKey"];
string cognitiveServicesKey = configuration["CognitiveServicesKey"];

SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));

// Create the skills
Console.WriteLine("Creating the skills...");
OcrSkill ocrSkill = CreateOcrSkill();
MergeSkill mergeSkill = CreateMergeSkill();

// Create the skillset
Console.WriteLine("Creating or updating the skillset...");
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

---

## <a name="how-the-key-is-used"></a>키를 사용 하는 방법

AI 보강 중 Cognitive Search는 Computer Vision, 텍스트 번역 및 Text Analytics를 기반으로 하는 [기본 제공 기술](cognitive-search-predefined-skills.md) 에 대 한 Cognitive Services API를 호출 합니다. Cognitive Services에 대 한 백 엔드 호출을 수행 하는 기본 제공 기술은 엔터티 연결, 엔터티 인식, 이미지 분석, 핵심 구 추출, 언어 감지, OCR, PII 검색, 감정 또는 텍스트 번역을 포함 합니다.

사용자 지정 기술 또는 유틸리티 기술(조건부, 문서 추출, 셰이퍼, 텍스트 병합, 텍스트 분할)만으로 구성된 기술 세트의 경우 키 및 Cognitive Services 섹션을 생략할 수 있습니다. 청구 가능한 기술 사용이 하루에 인덱서 당 20 개의 트랜잭션 미만으로 사용 되는 경우 섹션을 생략할 수도 있습니다.

기술 정의에 제공 하는 키는 요금 청구에 사용 되지만 연결에는 사용 되지 않습니다. 내부적으로 검색 서비스는 내부 네트워크를 통해 [동일한 물리적 지역](https://azure.microsoft.com/global-infrastructure/services/?products=search)에 배치 된 Cognitive Services 리소스에 연결 합니다. 기술력과에는 초당 20 개의 API 호출을 초과 하는 키가 필요 합니다.

### <a name="exceptions-and-special-cases"></a>예외 및 특수 사례

+ Cognitive Services (즉, 조건부, Shaper, 텍스트 병합 및 텍스트 분할 기술)를 호출 하지 않는 기술은 요금이 청구 되지 않습니다. 

+ [사용자 지정 엔터티 조회](cognitive-search-skill-custom-entity-lookup.md)는 Cognitive Services가 아닌 Azure Cognitive Search에 의해 측정되지만 하루에 인덱서당 20개를 초과하는 트랜잭션을 잠금 해제하려면 Cognitive Services 리소스 키가 필요합니다. 이 기술의 경우에만 리소스 키는 트랜잭션 수를 차단 해제하지만 청구와는 관련이 없습니다.

### <a name="other-costs-of-ai-enrichment"></a>AI 보강의 기타 비용

이미지 추출은 보강 전에 문서가 깨진 경우 발생하는 Azure Cognitive Search 작업입니다. 이미지 추출은 무료 계층에서 20개의 무료 일일 추출을 제외하고 모든 계층에서 청구 가능합니다. 이미지 추출 비용은 Blob 내부의 이미지 파일, 다른 파일(PDF 및 기타 앱 파일)에 포함된 이미지, [문서 추출](cognitive-search-skill-document-extraction.md)을 사용하여 추출한 이미지에 적용됩니다. 이미지 추출 가격 책정은 [Azure Cognitive Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)를 참조하세요.

텍스트 추출은 [문서 크래킹](search-indexer-overview.md#document-cracking) 구문 중에도 발생합니다. 청구되지 않습니다.

> [!TIP]
> 스킬셋 처리 비용을 낮추려면 [증분 보강(미리보기)](cognitive-search-incremental-indexing-conceptual.md)을 활성화하여 기술 세트 변경의 영향을 받지 않는 보강을 캐시하고 재사용합니다. 캐싱에는 Azure Storage가 필요합니다([가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/) 참조). 특히 이미지 추출 및 분석을 사용하는 기술 세트의 경우 기존 강화를 재사용할 수 있는 경우 기술 세트 실행의 누적 비용이 더 낮습니다.

## <a name="same-region-requirement"></a>동일한 지역 요구 사항

Cognitive Search와 Cognitive Services는 모두 [제품 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=search) 페이지에 표시된 것처럼 동일한 물리적 지역 내에 있어야 합니다. Cognitive Search를 제공하는 대부분의 지역은 Cognitive Services를 제공합니다.

두 서비스가 없는 지역에서 AI 보강을 시도하면 "제공된 키가 검색 서비스 지역에 대한 올바른 CognitiveServices 유형 키가 아닙니다."라는 메시지가 표시됩니다.

> [!NOTE]
> 일부 기본 제공 기술은 비 지역별 Cognitive Services(예: [텍스트 번역 기술](cognitive-search-skill-text-translation.md))를 기반으로 합니다. 비 지역별 기술을 사용하는 것은 Azure Cognitive Search 지역이 아닌 다른 지역에서 요청을 처리할 수 있음을 의미합니다. 비 지역별 서비스에 대한 자세한 내용은 [지역별 Cognitive Services 제품](https://aka.ms/allinoneregioninfo) 페이지를 참조하세요.

## <a name="example-estimate-costs"></a>예제: 비용 추정

인식 검색 인덱싱과 관련된 비용을 추정하려면 평균 문서가 어떤 모양인지부터 생각하여 몇 가지 숫자를 실행할 수 있도록 합니다. 예를 들어 다음과 같을 수 있습니다.

+ PDF 파일 1,000개
+ 각 6페이지
+ 페이지당 이미지 하나(이미지 6,000개)
+ 페이지당 3,000자

각 PDF의 문서 크래킹, 이미지 및 텍스트 추출, 이미지의 OCR(광학 문자 인식) 및 조직의 엔터티 인식으로 구성된 파이프라인이 있다고 가정합니다.

이 문서에 표시된 가격은 가상입니다. 예측 프로세스를 설명하는 데 사용됩니다. 비용은 낮아질 수 있습니다. 트랜잭션의 실제 가격은 [Cognitive Services 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services)을 참조하세요.

1. 텍스트 및 이미지 콘텐츠로 문서 해독의 경우, 텍스트 추출이 현재 무료입니다. 6,000개 이미지의 경우 추출된 모든 1,000개 이미지에 대해 $1를 가정합니다. 이 단계의 비용은 $6.00입니다.

2. 영어로 된 6,000개의 이미지 OCR의 경우, OCR 인지 기술은 최적의 알고리즘(DescribeText)을 사용합니다. 분석할 1,000개 이미지당 비용이 $2.50라고 가정할 경우 이 단계를 위해 $15.00를 지불합니다.

3. 엔터티 추출의 경우 페이지당 총 3개의 텍스트 레코드가 있습니다. 각 레코드는 1,000자입니다. 페이지당 3개의 텍스트 레코드* 6,000장의 페이지 = 18,000개의 텍스트 레코드입니다. 1,000개의 텍스트 레코드당 $2.00라고 가정할 경우 이 단계의 비용은 $36.00입니다.

비용을 모두 합하면, 설명한 기술 세트를 통해 이러한 형식의 PDF 문서 1,000개를 수집하는 데 $57.00 정도를 지불합니다.

## <a name="next-steps"></a>다음 단계

+ [Azure Cognitive Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기술 집합 만들기(REST)](/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
