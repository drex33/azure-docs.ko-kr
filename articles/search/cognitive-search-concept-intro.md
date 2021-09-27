---
title: AI 보강 개념
titleSuffix: Azure Cognitive Search
description: 미리 정의된 인지 기술과 사용자 지정 AI 알고리즘을 모두 사용하여 Azure Cognitive Search 인덱스에서 검색 가능한 콘텐츠를 만드는 데 사용되는 콘텐츠 추출, NLP(자연어 처리) 및 이미지 처리입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.custom: references_regions
ms.openlocfilehash: b1c7a8f29c08f00cc69dbd304c8215180f5ace92
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124796612"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search의 AI 보강

Azure Cognitive Search에서 AI 보강은 인덱싱을 수행하는 동안 콘텐츠 변환과 생성을 추가하는 기본 제공 인식 기술 및 사용자 지정 기술을 나타냅니다. 보강은 이미지로부터 정보를 추출하고, 감정, 주요 구문을 탐지하며, 텍스트에서 엔티티를 찾아내고 일부를 이름 짓는 등 이전에는 존재하지 않았던 새로운 정보를 생성합니다. 보강은 구분되지 않은 텍스트에도 구조체를 추가합니다. 이러한 모든 프로세스는 전체 텍스트 검색을 보다 효율적으로 만드는 문서를 생성합니다. 대부분의 경우 보강 문서는 검색 이외의 시나리오 (예: 정보 마이닝)에 유용합니다.

보강은 [인덱서](search-indexer-overview.md)에 연결된 [기술 세트](cognitive-search-working-with-skillsets.md)에 의해 정의됩니다. 인덱서는 콘텐츠를 추출하고 설정하지만, 기술 세트는 이미지, blob 및 기타 비정형 데이터 원본에서 새로운 정보 및 구조를 식별, 분석 및 생성합니다. 보강 파이프라인의 출력은 [검색 인덱스](search-what-is-an-index.md) 또는 [지식 저장소](knowledge-store-concept-intro.md)입니다.

기술 세트는 Cognitive Search의 기본 제공 기술을 포함하거나 [*사용자 지정 기술*](cognitive-search-create-custom-skill-example.md)에서 제공하는 외부 처리를 포함할 수 있습니다. 사용자 지정 기술의 예제로는 금융, 과학 출판물 또는 의약품과 같은 특정 도메인을 대상으로 하는 사용자 지정 엔터티 모듈 또는 문서 분류자가 있습니다.

기본 제공 기술은 다음과 같은 범주로 분류됩니다.

+ **자연어 처리** 기술에는 [엔터티 인식](cognitive-search-skill-entity-recognition-v3.md), [언어 감지](cognitive-search-skill-language-detection.md), [핵심 구 추출](cognitive-search-skill-keyphrases.md), 텍스트 조작, [감정 검색(오피니언 마이닝)](cognitive-search-skill-sentiment-v3.md) 및 [PII 검색](cognitive-search-skill-pii-detection.md)이 포함됩니다. 해당 기술을 사용하면 비정형 텍스트가 인덱스에서 검색 및 필터링 가능한 필드로 매핑됩니다.

+ **이미지 처리** 기술에는 [OCR(광학 인식)](cognitive-search-skill-ocr.md)을 비롯하여 얼굴 감지, 이미지 해석, 이미지 인식(유명한 사람 및 랜드마크) 또는 이미지 방향과 같은 특성 등의 [시각적 특징](cognitive-search-skill-image-analysis.md) 식별이 포함됩니다. 이러한 기술은 Azure Cognitive Search의 쿼리 기능을 사용하여 검색 가능한 이미지 콘텐츠의 텍스트 표현을 만듭니다.

![보강 파이프라인 다이어그램](./media/cognitive-search-intro/cogsearch-architecture.png "보강 파이프라인 개요")

Azure Cognitive Search의 기본 제공 기술은 Cognitive Services API에서 미리 학습된 기계 학습 모델인 [Computer Vision](../cognitive-services/computer-vision/index.yml) 및 [Text Analytics](../cognitive-services/text-analytics/overview.md)를 기반으로 합니다. 콘텐츠를 처리하는 동안 이러한 리소스를 활용하려는 경우 Cognitive Services 리소스를 연결할 수 있습니다.

자연어 및 이미지 처리는 데이터 수집 단계에서 적용되며, 여기서 결과는 Azure Cognitiv Search의 검색 가능한 인덱스에서 문서의 컴퍼지션 중 일부가 됩니다. 데이터는 Azure 데이터 집합으로 소싱된 다음, 필요한 [기본 제공 기술](cognitive-search-predefined-skills.md)을 사용하여 인덱싱 파이프라인을 통해 푸시됩니다.  

## <a name="feature-availability"></a>기능 가용성

AI 보강은 Azure Cognitive Services도 사용할 수 있는 지역에서 사용할 수 있습니다. [지역별 사용 가능한 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=search) 페이지에서 AI 보강의 현재 가용성을 확인할 수 있습니다. AI 보강은 다음을 제외한 지원되는 모든 지역에서 사용할 수 있습니다.

+ 오스트레일리아 남동부
+ 중국 북부 2
+ 노르웨이 동부
+ 독일 중서부

검색 서비스가 이러한 지역 중 하나에 있는 경우 기술 세트를 만들고 사용할 수 없지만 다른 모든 검색 서비스 기능은 사용할 수 있으며 완전히 지원됩니다.

## <a name="when-to-use-ai-enrichment"></a>AI 보강을 사용하는 경우

원시 콘텐츠가 비정형 텍스트, 이미지 콘텐츠 또는 언어 감지 및 번역이 필요한 콘텐츠인 경우 보강을 고려해야 합니다. 기본 제공되는 인지 기술을 통해 AI를 적용하면 이 콘텐츠가 잠금 해제되어 검색 및 데이터 과학 앱에서의 가치 및 효용성이 늘어날 수 있습니다. 

또한 파이프라인에 통합하고 싶은 오픈 소스, 타사 또는 자사 코드가 있을 경우, 사용자 지정 기술을 추가하는 것이 좋을 수 있습니다. 여러 문서 유형의 핵심 특성을 식별하는 분류 모델이 이 범주에 포함되지만, 콘텐츠에 가치를 부여하는 어떤 패키지라도 사용할 수 있습니다.

### <a name="use-cases-for-built-in-skills"></a>기본 제공 기술에 대한 사용 사례

기본 제공 기술을 사용하여 어셈블되는 [기술 세트](cognitive-search-defining-skillset.md)는 다음과 같은 애플리케이션 시나리오에 적합합니다.

+ 스캔한 문서(JPEG)에서 서체 및 필기 텍스트를 인식하는 [OCR(광학 문자 인식)](cognitive-search-skill-ocr.md)은 가장 일반적으로 사용되는 기술입니다. OCR 기술을 연결하면 JPEG 파일에서 텍스트를 식별, 추출 및 수집합니다.

+ 다국어 콘텐츠의 [텍스트 번역](cognitive-search-skill-text-translation.md)은 일반적으로 사용되는 또 다른 기술입니다. 언어 감지는 텍스트 번역에 기본 제공되지만 모음 콘텐츠의 언어 코드만 원하는 경우 독립적으로 [언어 감지](cognitive-search-skill-language-detection.md)를 실행할 수도 있습니다.

+ 이미지와 텍스트가 결합된 PDF PDF의 텍스트는 인덱싱 중에 보강 단계를 사용하지 않고 추출할 수 있지만, 이미지 및 자연어 처리를 추가하면 표준 인덱싱보다 더 나은 결과를 얻을 수 있습니다.

+ 내재된 의미가 있는 콘텐츠 또는 더 큰 문서에 숨겨진 컨텍스트를 포함하고 있는 비정형 또는 반정형 문서 

  특히 Blob은 단일 “필드”로 압축된 큰 콘텐츠 본문을 포함하는 경우가 많습니다. 이미지 및 자연어 처리 기술을 인덱서에 연결하면 원시 콘텐츠에 존재하지만, 그렇지 않은 경우 별도의 필드로 표시되는 새 정보를 만들 수 있습니다. 즉시 사용 가능한 기본 제공 인식 기술로는 [핵심 구 추출](cognitive-search-skill-keyphrases.md) 및 [엔터티 인식](cognitive-search-skill-entity-recognition-v3.md)(예: 사람, 조직 및 위치)이 있습니다.

  또한 기본 제공 기술을 사용하여 텍스트 분할, 병합 및 셰이프 작업을 통해 콘텐츠를 재구성하는 데 사용할 수도 있습니다.

### <a name="use-cases-for-custom-skills"></a>사용자 지정 기술의 사용 사례

사용자 지정 기술은 양식 인식, 또는 [사용자 지정 기술 웹 인터페이스](cognitive-search-custom-skill-interface.md)에서 제공하고 래핑하는 모델을 사용하는 사용자 지정 엔터티 검색처럼 좀 더 복잡한 시나리오를 지원할 수 있습니다. 사용자 지정 기술의 예로는 [Form Recognizer](../applied-ai-services/form-recognizer/overview.md), [Bing Entity Search API](./cognitive-search-create-custom-skill-example.md) 통합, [사용자 지정 엔터티 인식](https://github.com/Microsoft/SkillsExtractorCognitiveSearch) 등이 있습니다.

## <a name="enrichment-steps"></a>보강 단계 <a name="enrichment-steps"></a>

보강 파이프라인은 [*기술 세트*](cognitive-search-working-with-skillsets.md)가 포함된 [*인덱서*](search-indexer-overview.md)로 이루어져 있습니다. 기술 세트는 보강 단계를 정의하고 인덱서는 기술을 구동합니다. 인덱서를 구성할 때 [검색 인덱스](search-what-is-an-index.md)에 보강된 콘텐츠를 보내는 출력 필드 매핑이나 [지식 저장소](knowledge-store-concept-intro.md)의 데이터 구조를 정의하는 프로젝션과 같은 속성을 포함할 수 있습니다.

인덱싱이 완료되면 [Azure Cognitive Search에서 지원하는 쿼리 유형](search-query-overview.md) 모두를 통한 검색 요청에서 콘텐츠에 액세스할 수 있습니다.

### <a name="step-1-connection-and-document-cracking-phase"></a>1단계: 연결 및 문서 크래킹 단계

인덱서는 인덱서 데이터 원본에 제공된 정보를 사용하여 외부 원본에 연결합니다. 인덱서가 리소스에 연결되면 텍스트 및 이미지를 추출하기 위해 [“문서를 크래킹”](search-indexer-overview.md#document-cracking)합니다. 텍스트 콘텐츠가 텍스트 처리를 위해 큐에서 대기하는 동안 이미지 처리를 수행하는 기술로 이미지 콘텐츠를 라우팅할 수 있습니다. 

![문서 크래킹 단계](./media/cognitive-search-intro/document-cracking-phase-blowup.png "문서 크래킹")

이 단계는 AI 보강이 이뤄질 모든 초기 또는 원시 콘텐츠를 어셈블합니다. 각 문서에 대해 보강 트리가 만들어집니다. 초기에는 트리가 루트 노드 표현에 불과하지만 기술 세트 실행 중에 트리가 증가하여 구조를 얻게 됩니다.

### <a name="step-2-skillset-enrichment-phase"></a>2단계: 기술 세트 보강 단계

기술 세트는 각 문서에 대해 수행되는 원자성 작업을 정의합니다. 예를 들어 PDF에서 텍스트 및 이미지를 추출하면 기술 세트에서 엔터티 인식, 언어 감지 또는 핵심 구 추출을 적용하여 원본에서 기본적으로 사용할 수 없는 새 필드를 인덱스에 생성합니다. 

![보강 단계](./media/cognitive-search-intro/enrichment-phase-blowup.png "보강 단계")

 기술 세트는 간단하거나 매우 복잡할 수 있으며 처리 유형뿐만 아니라 작업 순서도 결정합니다. 대부분의 기술 세트에는 약 3 ~ 5개의 기술이 포함되어 있습니다.

기술 세트 및 인덱서의 일부로 정의된 [출력 필드 매핑](cognitive-search-output-field-mapping.md)은 보강 파이프라인을 완벽하게 지정합니다. 이 모든 부분을 한데 모으는 방법에 대한 자세한 정보 [기술 집합 정의](cognitive-search-defining-skillset.md)를 참조하세요.

내부적으로 파이프라인은 보강된 문서 컬렉션을 생성합니다. 보강된 문서의 어느 부분을 검색 인덱스의 인덱싱 가능 필드에 매핑할지 결정할 수 있습니다. 예를 들어 핵심 구 추출과 엔터티 인식 기술을 적용한 경우 새 필드는 보강된 문서의 일부가 되며 인덱스의 필드에 매핑될 수 있습니다. 입/출력에 대한 자세한 내용은 [주석](cognitive-search-concept-annotations-syntax.md)을 참조하세요.

### <a name="step-3-indexing"></a>3단계: 인덱싱

인덱싱은 원시 및 보강 콘텐츠가 검색 인덱스에서 필드로, 지식 저장소를 생성하는 경우 [프로젝션](knowledge-store-projection-overview.md)으로 수집되는 프로세스입니다. 암시적 또는 명시적 필드 매핑을 사용하여 올바른 필드에 콘텐츠를 전송하는 두 가지 모두에 동일한 보강 콘텐츠가 표시될 수 있습니다.

보강 콘텐츠는 기술 세트 실행 중에 생성되며 저장하지 않는 한 임시적입니다. 보강 콘텐츠가 검색 인덱스에 표시되려면 검색 인덱스의 필드에 보강 콘텐츠를 보낼 수 있도록 인덱서에 매핑 정보가 있어야 합니다. [출력 필드 매핑](cognitive-search-output-field-mapping.md)이 이러한 연결을 설정합니다.

## <a name="saving-enriched-output"></a>보강 출력 저장

Azure Cognitive Search에서 인덱서는 만들어지는 출력을 저장합니다.

[검색 가능 인덱스](search-what-is-an-index.md)는 항상 인덱서에서 생성되는 출력 중 하나입니다. 인덱스의 사양은 인덱서 요구 사항입니다. 기술 세트를 연결하면 기술 세트의 출력과 원본에서 직접 가져온 모든 필드가 인덱스를 채우는 데 사용됩니다. 일반적으로 핵심 구 또는 감정 점수와 같은 특정 기술에 대한 출력은 해당 목적용으로 만든 필드의 인덱스로 수집됩니다.

[지식 저장소](knowledge-store-concept-intro.md)는 지식 마이닝과 같은 다운스트림 앱에 사용되는 선택적 출력입니다. 지식 저장소는 기술 세트 내에서 정의됩니다. 해당 정의는 보강 문서가 테이블이나 개체(파일 또는 Blob)로 프로젝션되는지 여부를 결정합니다. 테이블 형식 프로젝션은 Power BI와 같은 도구의 대화형 분석에 적합하지만 파일 및 Blob은 일반적으로 데이터 과학 또는 유사한 프로세스에 사용됩니다.

마지막으로, 인덱서는 이후 기술 세트 실행에서 다시 사용할 수 있도록 Azure Blob Storage의 [보강 문서를 캐시](cognitive-search-incremental-indexing-conceptual.md)할 수 있습니다. 캐시된 보강은 나중에 다시 실행하는 것과 동일한 기술 세트에서 사용할 수 있습니다. 캐싱은 기술 세트에 이미지 분석 또는 OCR이 포함되어 있고 이미지 파일을 다시 처리하는 시간과 비용을 방지하려는 경우에 유용합니다.

인덱스와 지식 저장소는 서로 완전히 독립적입니다. 인덱서 요구 사항을 충족하기 위해 인덱스를 연결해야 하지만, 유일한 목표가 지식 저장소인 경우에는 인덱스를 채운 후 무시해도 됩니다. 그러나 삭제하지 마세요. 인덱서 및 기술 세트를 다시 실행하려면 인덱서를 실행하기 위해 인덱스가 필요합니다.

## <a name="using-enriched-content"></a>보강 콘텐츠 사용

처리가 완료되면 Azure Cognitive Search에서 전체 텍스트를 검색할 수 있는 보강된 문서로 구성된 [검색 인덱스](search-what-is-an-index.md)가 생성됩니다. [인덱스 쿼리](search-query-overview.md)는 개발자와 사용자가 파이프라인에서 생성된 보강된 콘텐츠에 액세스하는 방법입니다. 인덱스는 Azure Cognitive Search에 대해 만들 수 있는 다른 항목과 비슷합니다. 사용자 정의 분석기로 텍스트 분석을 보완하거나, 퍼지 검색 쿼리를 호출하거나, 필터를 추가하거나, 점수 매기기 프로필을 사용하여 검색 관련성을 조정할 수 있습니다.

[지식 저장소](knowledge-store-concept-intro.md)도 있을 수 있습니다. 지식 저장소는 분석 또는 기계 학습과 같은 정보 마이닝 시나리오에서 사용할 수 있는 데이터를 포함합니다. [Storage Explorer](knowledge-store-view-storage-explorer.md), [Power BI](knowledge-store-connect-power-bi.md) 또는 Azure Storage에 연결된 앱을 사용할 수 있습니다.

## <a name="checklist-a-typical-workflow"></a>검사 목록: 일반적인 워크플로

1. 프로젝트를 시작할 때 데이터의 하위 집합을 사용하는 것이 도움이 됩니다. 인덱서 및 기술 세트 디자인은 반복적인 프로세스이며 작은 대표 데이터 세트로 작업하는 경우 더 빠르게 반복됩니다.

1. 데이터에 대한 연결을 지정하는 [데이터 원본](/rest/api/searchservice/create-data-source)을 만듭니다.

1. [기술 세트](/rest/api/searchservice/create-skillset)를 만들어 보강을 추가합니다.

1. 검색 인덱스를 정의하는 [인덱스 스키마](/rest/api/searchservice/create-index)를 만듭니다.

1. [인덱서](/rest/api/searchservice/create-indexer)를 만들어 위의 구성 요소를 모두 가져옵니다. 인덱서 만들기 또는 실행은 데이터를 검색하고, 기술 세트를 실행하고, 인덱스를 로드합니다.

1. 쿼리를 실행하여 결과를 평가하고 코드를 수정하여 기술 집합, 스키마 또는 인덱서 구성을 업데이트합니다.

위의 단계를 반복하려면 파이프라인을 다시 작성하기 전에 [인덱서를 다시 설정](search-howto-reindex.md)하거나, 각 실행에서 개체를 삭제하고 다시 만듭니다(무료 계층을 사용하는 경우 권장). 또한 가능한 경우 기존 보강을 다시 사용하려면 [보강 캐싱을 사용하도록 설정](cognitive-search-incremental-indexing-conceptual.md)해야 합니다.

## <a name="next-steps"></a>다음 단계

+ [빠른 시작: 포털에서 AI 보강 시도 연습](cognitive-search-quickstart-blob.md)
+ [자습서: AI 보강 REST API 알아보기](cognitive-search-tutorial-blob.md)
+ [기술 세트 개념](cognitive-search-working-with-skillsets.md)
+ [지식 저장소 개념](knowledge-store-concept-intro.md)
+ [기술 세트 만들기](cognitive-search-defining-skillset.md)
+ [지식 저장소 만들기](knowledge-store-create-rest.md)