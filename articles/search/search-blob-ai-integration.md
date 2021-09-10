---
title: AI를 사용하여 Blob 콘텐츠 보강
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 자연어 및 이미지 분석 기능과 이러한 프로세스가 Azure Blob에 저장된 콘텐츠에 적용되는 방식에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 083a568f6fad664c59073e2f6858900302e9d971
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528434"
---
# <a name="use-ai-to-process-and-analyze-blob-content-in-azure-cognitive-search"></a>AI를 사용하여 Azure Cognitive Search의 Blob 콘텐츠 처리 및 분석

이미지 또는 오랫동안 차등화되지 않은 텍스트로 구성된 Azure Blob Storage 콘텐츠는 딥 러닝 분석을 통해 다운스트림 애플리케이션에 유용한 중요한 정보를 표시하고 추출할 수 있습니다. [AI 보강](cognitive-search-concept-intro.md)을 사용하여 다음을 수행할 수 있습니다.

+ OCR(광학 인식)을 사용한 이미지에서 텍스트 추출
+ 사진에서 장면 설명 또는 태그 생성
+ 언어 감지 및 텍스트를 다른 언어로 번역
+ 사람, 날짜, 장소 또는 조직에 대한 참조를 찾아 엔터티 인식을 통해 구조체 유추

이러한 AI 기능 중 하나만 필요할 수 있지만 여러 기능을 동일한 파이프라인으로 결합하는 것이 일반적입니다(예: 스캔한 이미지에서 텍스트를 추출한 다음, 참조된 모든 날짜와 위치를 찾는 경우). 또한 사용자 지정 AI 또는 기계 학습 처리를 데이터 및 요구 사항에 맞게 조정된 선행 에지 외부 패키지 또는 사내 모델 형식으로 포함하는 것이 일반적입니다.

검색 인덱서에서 지원하는 모든 데이터 원본에 AI 보강을 적용할 수 있지만 Blob은 보강 파이프라인에서 가장 자주 사용되는 구조체입니다. 전체 텍스트 검색의 경우 결과는 검색 인덱스로 끌어와 집니다. 또는 검색 시나리오나 분석 시나리오의 경우 데이터 탐색을 포함하는 새로운 애플리케이션 환경을 제공하기 위해 결과가 Azure Storage로 다시 라우팅됩니다. 

이 문서에서는 Blob의 원시 데이터 변환부터 검색 인덱스 또는 지식 저장소의 쿼리 가능 정보에 이르기까지 전체 프로세스를 신속하게 파악할 수 있도록 포괄적으로 AI 보강을 확인합니다.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>AI를 사용한 Blob 데이터 ‘보강’의 의미

‘AI 보강’은 Microsoft의 기계 학습 모델 또는 제공하는 사용자 지정 학습 모델을 통합하는 Azure Cognitive Search 인덱싱 아키텍처에 속합니다. Blob을 처리하고(기존 Blob과 새 Blob 모두 들어오거나 업데이트될 때), 모든 파일 형식을 크래킹하여 이미지와 텍스트를 추출하고, 다양한 AI 기능을 사용하여 원하는 정보를 추출하고, 빠른 검색 및 탐색을 위해 검색 인덱스에 인덱싱해야 하는 엔드투엔드 시나리오를 구현할 수 있습니다. 

입력은 Azure Blob Storage의 단일 컨테이너에 있는 Blob입니다. Blob은 거의 모든 종류의 텍스트 또는 이미지 데이터일 수 있습니다. 

출력은 항상 인덱스로, 클라이언트 애플리케이션에서 빠른 텍스트 검색, 검색, 탐색에 사용됩니다. 또한 출력은 Power BI와 같은 도구 또는 데이터 과학 워크로드에서 다운스트림 분석을 위해 Azure Blob 또는 Azure 테이블에 보강된 문서를 보여주는 [‘지식 저장소’](knowledge-store-concept-intro.md)일 수도 있습니다.

입력과 출력 사이에는 파이프라인 아키텍처 자체가 있습니다. 파이프라인은 AI를 제공하는 하나 이상의 ‘기술’로 구성된 [‘기술 세트’](cognitive-search-working-with-skillsets.md)를 할당할 수 있는 [‘인덱서’](search-indexer-overview.md)를 기반으로 합니다.   파이프라인의 목적은 파이프라인을 원시 콘텐츠로 입력하지만 파이프라인을 통해 이동하는 동안 추가적인 구조체, 컨텍스트, 정보를 선택하는 ‘보강된 문서’를 생성하는 것입니다. 보강된 문서는 전체 텍스트 검색 또는 탐색 및 분석에 사용되는 반전된 인덱스 및 기타 구조를 만들기 위해 인덱싱 중에 사용됩니다.

## <a name="required-resources"></a>필요한 리소스

Azure Blob Storage 및 Azure Cognitive Search 외에도 AI를 제공하는 세 번째 서비스 또는 메커니즘이 필요합니다.

+ 기본 제공 AI의 경우 Cognitive Search는 Azure Cognitive Services 비전 및 자연어 처리 API와 통합됩니다. [Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)하여 OCR(광학 인식), 이미지 분석 또는 자연어 처리(언어 감지, 텍스트 번역, 엔터티 인식, 핵심 구 추출)를 추가할 수 있습니다. 

+ Azure 리소스를 사용하는 사용자 지정 AI의 경우 사용하려는 외부 함수 또는 모델을 래핑하는 사용자 지정 기술을 정의할 수 있습니다. [사용자 지정 기술](cognitive-search-custom-skill-interface.md)은 Azure Functions, Azure Machine Learning, Azure Form Recognizer 또는 HTTPS를 통해 액세스할 수 있는 기타 리소스에서 제공하는 코드를 사용할 수 있습니다.

+ Azure 이외의 사용자 지정 AI의 경우, 모델 또는 모듈이 HTTP를 통해 인덱서에 액세스할 수 있어야 합니다.

모든 서비스를 바로 사용할 수 없는 경우 Storage 계정 포털 페이지에서 직접 시작합니다. 왼쪽 탐색 페이지의 **Blob service** 에서 **Azure Cognitive Search 추가** 를 클릭하여 새 서비스를 만들거나 기존 서비스를 선택합니다. 

Azure Cognitive Search를 스토리지 계정에 추가하면 표준 프로세스에 따라 Azure 데이터 원본의 데이터를 보강할 수 있습니다. 편리한 AI 보강 초기 도입을 위해 Azure Cognitive Search의 **데이터 가져오기** 마법사를 권장합니다. 워크플로 중에 Cognitive Services 리소스를 연결할 수 있습니다. 이 빠른 시작에서는 [포털에서 AI 보강 파이프라인 만들기](cognitive-search-quickstart-blob.md) 단계를 안내합니다. 

다음 섹션에서는 구성 요소 및 워크플로를 자세히 살펴봅니다.

## <a name="use-a-blob-indexer"></a>Blob 인덱서 사용

AI 보강은 인덱싱 파이프라인에 대한 추가 기능이며, Azure Cognitive Search에서 이 파이프라인은 ‘인덱서’에 빌드됩니다. ‘인덱서’는 데이터를 샘플링하고, 메타데이터를 읽고, 데이터를 검색하며, 후속 가져오기를 위해 네이티브 형식의 데이터를 JSON 문서로 직렬화하기 위한 내부 논리를 포함하는 데이터 원본 인식 하위 서비스입니다. 인덱서는 AI와 별개로 가져오기에 사용되는 경우가 많지만, AI 보강 파이프라인을 빌드하려면 인덱서와 그에 따르는 기술 세트가 필요합니다. 본 섹션에서는 인덱서에 대해 자세히 알아보고, 다음 섹션에서는 기술 세트에 중점을 둡니다.

Azure Storage의 Blob은 [Blob 인덱서](search-howto-indexing-azure-blob-storage.md)를 사용하여 인덱싱됩니다. **데이터 가져오기** 마법사, REST API 또는 SDK를 사용하여 이 인덱서를 호출할 수 있습니다. 인덱서에서 사용하는 데이터 원본이 Azure Blob 컨테이너인 경우 Blob 인덱서가 호출됩니다. 가상 디렉터리를 만들어 매개 변수로 전달하거나 파일 형식 확장에서 필터링하여 Blob의 하위 집합을 인덱싱할 수 있습니다.

인덱서는 [“문서 크랙”](search-indexer-overview.md#document-cracking)을 발생시켜 콘텐츠를 검사하는 Blob을 엽니다. 이 작업은 데이터 원본에 연결한 후 파이프라인에서 첫 번째 단계입니다. Blob 데이터의 경우에는 PDF, Office 문서, 이미지, 기타 콘텐츠 형식이 검색됩니다. 텍스트 추출을 사용한 문서 크래킹은 무료로 제공됩니다. 이미지 추출을 이용한 문서 크래킹 요금은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)의 요율로 부과됩니다.

모든 문서가 크래킹되지만, 보강은 보강 기술을 명시적으로 제공하는 경우에만 수행됩니다. 예를 들어 파이프라인이 이미지 분석으로만 구성된 경우 컨테이너 또는 문서의 텍스트는 무시됩니다.

Blob 인덱서는 구성 매개 변수와 함께 제공되며 기본 데이터가 충분한 정보를 제공하는 경우 변경 내용 추적을 지원합니다. 자세한 내용은 [Blob 인덱서 구성 방법](search-howto-indexing-azure-blob-storage.md)을 참조하세요.

## <a name="add-ai-components"></a>AI 구성 요소 추가

AI 보강은 패턴 또는 특성을 찾은 다음, 그에 따라 작업을 수행하는 모듈을 가리킵니다. 대표적인 예로는 사진 내 얼굴 인식, 사진 내 텍스트 설명, 문서 내 핵심 구 감지, OCR(또는 이진 파일 내 인쇄 또는 필기 텍스트 인식)이 있습니다.

Azure Cognitive Search에서 ‘기술’은 단독으로 또는 다른 기술과 함께 사용할 수 있는 AI 처리의 개별 구성 요소입니다. 

+ 기본 제공 기술은 Cognitive Services로 지원되며, 이미지 분석은 Computer Vision을 기반으로 하고 자연어 처리는 Text Analytics를 기반으로 합니다. 전체 목록은 [콘텐츠 보강을 위한 기본 제공 기술](cognitive-search-predefined-skills.md)을 참조하세요.

+ 사용자 지정 기술은 파이프라인에 통합할 수 있는 [인터페이스 정의](cognitive-search-custom-skill-interface.md)에 래핑된 사용자 지정 코드입니다. 고객 솔루션에서는 오픈 소스, 타사 또는 자사 AI 모듈을 제공하는 사용자 지정 기술과 함께 두 모듈을 모두 사용하는 것이 일반적입니다.

‘기술 세트’는 파이프라인에서 사용되는 기술 모음이며, 문서 크래킹 단계에서 콘텐츠를 사용할 수 있게 되면 호출됩니다. 인덱서는 정확히 하나의 기술 세트만 사용할 수 있지만, 해당 기술 세트는 다른 시나리오에서 재사용할 수 있도록 인덱서와는 별개로 존재합니다.

사용자 지정 기술은 복잡해 보일 수 있지만 구현 측면에서 간단하고 단순할 수 있습니다. 패턴 일치 또는 분류 모델을 제공하는 기존 패키지가 있는 경우 Blob에서 추출한 콘텐츠를 이러한 모델에 전달하여 처리할 수 있습니다. AI 보강은 Azure 기반이므로 모델도 Azure에 있어야 합니다. 몇 가지 일반적인 호스팅 방법론에는 [Azure Functions](cognitive-search-create-custom-skill-example.md) 또는 [컨테이너](https://github.com/Microsoft/SkillsExtractorCognitiveSearch) 사용이 포함됩니다.

Cognitive Services로 지원되는 기본 제공 기술을 사용하려면 리소스에 대한 액세스 권한을 제공하는 [연결된 Cognitive Services](cognitive-search-attach-cognitive-services.md) 범용 구독 키가 필요합니다. 범용 키는 이미지 분석, 언어 감지, 텍스트 번역, 텍스트 분석을 제공합니다. 다른 기본 제공 기술은 Azure Cognitive Search 기능이며 추가 서비스 또는 키가 필요하지 않습니다. 쉐이퍼, 분할기, 병합기는 파이프라인을 디자인할 때 필요한 도우미 기술의 예입니다.

사용자 지정 기술 및 기본 제공 유틸리티 기술만 사용하는 경우 Cognitive Services에 관련된 종속성 또는 비용이 없습니다.

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>다운스트림 솔루션에서 AI 보강 출력 사용

AI 보강의 출력은 Azure Cognitive Search 검색 인덱스이거나 Azure Storage [지식 저장소](knowledge-store-concept-intro.md)입니다.

Azure Cognitive Search에서는 검색 인덱스가 클라이언트 앱에서 자유 텍스트 및 필터링된 쿼리를 통한 대화형 탐색에 사용됩니다. AI를 통해 만든 보강된 문서는 JSON 형식으로 지정되고 인덱서가 제공하는 모든 이점을 활용하여 Azure Cognitive Search에서 모든 문서가 인덱싱되는 것과 동일한 방식으로 인덱싱됩니다. 예를 들어 인덱싱하는 동안 Blob 인덱서가 구성 매개 변수 및 설정을 참조하여 필드 매핑 또는 변경 검색 논리를 활용합니다. 이러한 설정은 일반 인덱싱 및 AI 보강 워크로드에서 전적으로 사용할 수 있습니다. 인덱싱 후 콘텐츠가 Azure Cognitive Search에 저장되면 풍부한 쿼리 및 필터 식을 작성하여 콘텐츠를 이해할 수 있습니다.

Azure Storage의 지식 저장소에는 Blob 컨테이너 또는 Table Storage의 테이블이라는 두 가지 매니페스트가 있습니다. 

+ Blob 컨테이너는 보강된 문서를 전체적으로 캡처합니다. 이는 다른 프로세스에 공급하려는 경우에 유용합니다. 

+ 반면, Table Storage는 보강된 문서의 물리적 프로젝션을 수용할 수 있습니다. 특정 부분을 포함하거나 제외하는 보강된 문서의 조각 또는 계층을 만들 수 있습니다. Power BI 내 분석의 경우 Azure Table Storage의 테이블은 추가적인 시각화 및 탐색을 위한 데이터 원본이 됩니다.

파이프라인의 끝에 있는 보강된 문서는 보강 중에 추출되거나 생성된 새 정보를 포함하는 추가 필드가 있다는 점에서 원래 입력 버전과 다릅니다. 따라서 사용하는 출력 구조와 관계없이 원래 콘텐츠와 만든 콘텐츠를 조합하여 작업할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다양한 방식으로 Cognitive Services를 결합하고 시나리오에 대한 기존 Cognitive Service가 없는 경우 사용자 지정 기술을 작성하는 등 AI 보강을 통해 Azure Storage에서 데이터를 최대한 활용하기 위해 수행할 수 있는 것이 많습니다. 자세한 내용은 다음 링크를 참조하세요.

+ [Azure Portal을 사용하여 Blob 업로드, 다운로드, 나열(Azure Blob Storage)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Blob 인덱서 설정(Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [AI 보강 개요(Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [기술 세트 만들기(Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [주석 트리의 노드 매핑(Azure Cognitive Search)](cognitive-search-output-field-mapping.md)
